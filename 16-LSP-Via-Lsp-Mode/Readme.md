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

## Opening A C# Project

Ok, so now let's see if this gets past the issue we had with C#. The lsp-mode
docs explicitely show that the roslyn language server is one of the supported C# ones.

Opening my first C# file now asks me which language server I want to use. I selected
`csharp-roslyn).  I then got presented with:

![C# Project Selection](csharp-project.png)

This is useful because any given C# project may be a part of multiple C# project files (`csproj`)
at any given time, which itself can be part of multiple solution (`sln`) files (in really
large systems solution files are dynamically generated.

The interesting part about this prompt to me is the `i` option is pointing to the 
Emacs project root (since that has the `.git` folder), but in reality the solution
file that refers to all the related projects are in the `/src` folder. Let's see what
happens if we select the Emacs/VCS project root.

