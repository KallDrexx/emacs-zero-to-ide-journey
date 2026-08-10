# LSP Via LSP-Mode

So the last post looked at the native Eglot LSP integration. Many people
successfully use it and enjoy it's simplicity and performance. Unfortunately
I had issues using it for C# (the second language I tried it with) and thus
could not use it (at least not easily).

The Eglot investigation was worth it, because it taught me about Flymake, xref,
imenu, and other Emacs systems that will be important for most other LSP
integrations (and you should read that post if you haven't).

## What Is LSP-Mode

[LSP-Mode](https://emacs-lsp.github.io/lsp-mode/) is an Emacs package that provides
LSP integration with a lot of extras. You may not need the extras, but it's a
more heavyweight option than Eglot. It even includes Github Copilot support, depending
on how you feel about that.

One benefit I do find (in my research) of lsp-mode over Eglot is supposedly lsp-mode
supports multiple LSP integrations per buffer. This is important in web contexts, because
you may have one LSP for html, one for javascript, one for tailwind css, etc... You
have to use a multiplexor with Eglot for the same capabilities.

The [languages](https://emacs-lsp.github.io/lsp-mode/page/languages/) page lists a
lot more languages and language server support than Eglot, so that's a good sign for
us at least. It also looks like it has a `M-x lsp-install-server` which can make it
not required to manually install language servers (like vscode and others do).

Installation was pretty simple, I just had to add the following to my `init.el`

```elisp
(use-package lsp-mode
  :ensure t
  :hook
  (
   (lsp-mode . lsp-enable-which-key-integration)
   ;; Which major modes to activate for
   (csharp-ts-mode . lsp)
   )
  :commands lsp)
```

I'm sure I'll be adding a lot more modes, but lets start with C# since that
is the mode that Eglot gave me trouble with.

Once evaluated, we can make sure it's installed properly with `M-x lsp-doctor`. For
me the only optional performance warning was not having plists enabled. I was able
to activate that by adding `(setenv "LSP_USE_PLISTS" "true")` to the beginning of
my `init.el`, `M-x delete-package` to remove lsp-mode, and then restart emacs. 

The rest of the recommendations were things we had already done for the most part.

## C#

Ok, so now let's see if this gets past the issue we had with C#. The lsp-mode
docs explicitely show that the roslyn language server is one of the supported C# ones.

Opening my first C# file now asks me which language server I want to use. I selected
`csharp-roslyn).  I then got presented with:

![C# Project Selection](csharp-project.png)

It's not clear if it's talking about a C# project or a Emacs project. My assumption is
that the lsp-mode will attempt to use this project root to search for relevant
language server projects. For C#, I assume it'll use this root to find a C# solution file
to tie the file I just opened to. 

Using `i` the mode goes away and I can clearly tell that the lsp-mode is activated automatically:

![LSP activated](csharp-activated.png)

The top has a display showing the hiearchy of the symbol at the point. It appears that
the directory structure from the `csproj` root is up there, and clicking on them opens
`dired` to that path. The hiearchy past the current file we are in navigates to those
symbols. I suspect there is a keyboard shortcut for these but haven't looked yet.

I also see Flymake correctly telling me there are zero errors or warnings in the mode
line. I also see the mode line showing the LSP(csharp-roslyn) minor mode (which also
seems to include the process ID of the language server it launched). 

It also shows that the language server is providing two code actions for my current area.
We can execute these actions with `M-x lsp-execute-code-actions`, which gives me two
options specific to the C# line we are on (property refactors).

### Solution Not Loading

The first problem I encountered was `M-.` (go to definition) wasn't working, and 
`M-?` (find references) was also not providing any references outside this file.

My original assumption was that it only loaded the `csproj` file and not the C# 
solution file. Exploring `M-x` options led me to the `lsp-roslyn-open-solution-file` command.
This command is useful because it allows you to select a different solution file if you have
multiples.

Unfortunately, this command claimed it could not find a solution. This is where Emacs introspective
capabilities came in handy. I ran `M-x describe-function lsp-roslyn-open-solution-file` and then 
clicked on the `lsp-roslyn.el` clickable link to view the source code. This led me to the following
line of code:

```elisp
(defun lsp-roslyn--find-solution-file ()
  (let ((solutions (lsp-roslyn--find-files-in-parent-directories
                    (file-name-directory (buffer-file-name))
                    (rx (* anychar) ".sln" eos))))
    (cond
     ((not solutions) nil)
     ((eq (length solutions) 1) (cl-first solutions))
     (t (lsp-roslyn--pick-solution-file-interactively solutions)))))
```

So lsp-mode's roslyn language server integration doesn't know about the new C# solution format
yet (which has the extension slnx). I was able to use `M-:` to evaluate a new copy/paste of that
function with `slnx`, and then re-run `M-x lsp-roslyn-open-solution-file` and viola, I now
can successfully find references, go to definition, better completion, etc... 

I put the defun declaration in the `use-package lsp-mode`'s `:config` section to override it
after package load until a new version with a fix is released.

```elisp
  ;; Temporary workaround for C# slnx support
  (with-eval-after-load 'lsp-roslyn
    (defun lsp-roslyn--find-solution-file ()
      (let ((solutions (lsp-roslyn--find-files-in-parent-directories
                        (file-name-directory (buffer-file-name))
                        (rx (* anychar) "." (or "sln" "slnx") eos))))
        (cond
         ((not solutions) nil)
         ((eq (length solutions) 1) (cl-first solutions))
         (t (lsp-roslyn--pick-solution-file-interactively solutions))))))
```

### IMenu Differences

`M-x imenu` is now enhanced in that it first allows me to pick a class/struct, and presents
me with a second menu of properties and methods within the selected type. 

![imenu first level](csharp-imenu1.png)

![imenu second level](csharp-imenu2.png)

### Conclusion

So far while playing around, the C# LSP is now successfully working for me.

## Typescript

Before going into a typescript file, I removed the `node_modules` folder from the
typescript project, just to see how it handles that case compared to Eglot. 

I also added the following to the `use-package lsp-mode`'s `:hook` section

```elisp
(typescript-ts-mode . lsp)
```

That will make lsp-mode activate when the typescript major mode is loaded.

Immediately I get the `app.ts is not part of any project` lsp-mode prompt. Selecting `i`
causes me to lose all syntax highlighting and get the "the package typescript is not installed"
error.

![typescript error](ts-error.png)

Unlike the Eglot case, the error did not immediately go away with the only way to find it
again was to go to the messages buffer. Using `M-x lsp-mode` to try and reactivate it
gives me the same error but this time at least shows `Disconnected` in the mode line.

I found that lsp-mode creates a `*lsp-log*` buffer. However, this doesn't help my
current situation, presumably because this error is coming from the typescript
language server itself and not lsp-mode itself.

Unfortunately, this did not seem to be an issue with not running `pnpm install` as
was the case with Eglot. After some searching I came across
[this issue](https://github.com/emacs-lsp/lsp-mode/issues/5099). Essentially, a 
new version of typescript's compiler was released (version 7) very very recently,
and that changed the path of tsserver. Manually running `pnpm i -g typescript@6` allowed 
it to find the typescript server and work.

I think Eglot didn't have this problem because Eglot relied on the project's own
typescript `devDependency`, and since that project was pinned to an earlier version
of the typescript package it worked. It looks like lsp-mode's typescript integration
doesn't use the package specific typescript environment, but instead uses the global one.

Once this was done and I reloaded the lsp buffer, I was off to the races.

![typescript working](ts-working.png)

## Other Languages

I tried Verilog and C++ and did not really hit any issues worth noting. Everything
seemed to work just fine.

## Keymap Rebind

By default, lsp-mode has it's key bindings attached to `s-l` (Super + l). Since
I use a tiling window manager with vim style bindings, that doesn't work for me
as `s-l` changes focus to another OS window.

Changing the prefix was a matter of adding the following to the
`use-package lsp-mode`'s `:init` section:

```elisp
(setq lsp-keymap-prefix "C-c l")
```

This has to be done in the `:init` so it's active before lsp-mode starts, otherwise
lsp-mode will still bind to `s-l` but the which-key integration will think it's
`C-c l`.

## Conclusion

Lsp-mode seems to work pretty well except for a few gotchas.

The one main bug I have found is with find references (`M-?`). It claims the default is
the symbol that's under the point, but that's not actually the case. 

![find references bug](find-refs.png)

I think that's because there is no `Payload` entry anymore (despite it claiming that's the default)
and the vertical minibuffer completion has no matches (since it's `Nalu / Payload`, not `Payload`.

This is annoying but workable since I can type `payload` and still get it. It does mean there's
not purpose putting the point on the symbol I want to look for. It also means that I can't
find all references of a local value, and have to resort to highlighting and manually scanning
the code. 

I'll have to see how much that annoys me and if there is a work around.

