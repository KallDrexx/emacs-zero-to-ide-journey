# LSP Support Via Eglot
<!-- markdown toc start -->
**Table of Contents**

  - [What Are Language Servers](#what-are-language-servers)
  - [Language Server Installation](#language-server-installation)
  - [What Is Eglot](#what-is-eglot)
  - [Xref](#xref)
  - [Eldoc](#eldoc)
  - [Imenu](#imenu)
  - [Flymake](#flymake)
  - [Completion At Point](#completion-at-point)
  - [Code Actions](#code-actions)
  - [Other Languages](#other-languages)
    - [C#](#c)
    - [Typescript](#typescript)
    - [Verilog](#verilog)
  - [Conclusion](#conclusion)

<!-- markdown toc end -->

While tree-sitter works great for parsing the code into an AST to provide highlighting
and basic navigation, it doesn't actually understand the code. It knows that your code
has the correct grammar but it doesn't know that you are referring to a variable
that doesn't exist, or if a function you are calling has the correct visibility, etc...

Thus we need something more to provide IDE like capabilities to Emacs.

## What Are Language Servers

Language servers are independent programs designed to provide semantic understanding 
and advanced editing capabilities for programming languages. They can provide
diagnostic messages, cross referencing to locate related code, provide smart
completion options, refactoring utilities, etc...

Since each language server tends to specialize in one specific programming language,
it knows the actual semantic meaning of the code and how it relates to other code
and packages being referenced.

As the name implies, each language server acts as an independent process outside of
the client which is utilizing it. This means that any editor can use the same
language server.

LSPs unlock a significant amount of power for code-editing, and is critical for me
to act as a usable development environment.

The Language Server Protocol (LSP) is the standard interface for editors to communicate
and interact with language servers. 

So our goal here is to set Emacs up as an LSP client, so that it can automatically invoke
functionality from the correct language server at the right time.

## Language Server Installation

Editors act as clients for the language servers. Some editors may helpfully install
language servers themselves but ultimately they are third-party servers.

Most LSP-integration packages in Emacs will assume you have the language servers
installed already, instead of installing them for you. Each language has its
own (and sometimes multiple exist for the same languages). 

For my personal use case, these are the language servers I have installed and the
installation instructions I use for them:

* bash - `pnpm i -g bash-language server`
* C++ - `clangd` installed via brew or system package manager
* C# - `dotnet tool install --global roslyn-langauge-server --prerelease`
* CMake - `pipx install cmake-language-server`
* docker - `pnpm i -g dockerfile-language-server-nodejs`
* docker compose - `pnpm i -g @microsoft/compose-language-service`
* F# - `dotnet tool install --global fsautocomplete`
* fish shell - `pnpm i -g fish-lsp`
* Lua - `lua-language-server` installed via brew or system package manager
* PytEhon - `pipx install pyright`
* typescript - `pnpm i -g typescript typescript-language-server`
* Verilog - `Verible` manually installed [from Github](https://github.com/chipsalliance/verible)

## What Is Eglot

[Eglot](https://elpa.gnu.org/devel/doc/eglot.html) is the LSP client integration that's natively
built into Emacs. 

Using Eglot is actually easy. Since I already had `clangd` installed I was able to go to my 
`main.cpp` file, run `M-x eglot` and saw the following message:

![eglot active](eglot-activated.png)

Now that Eglot is activated, it will communicate with the clangd process it established a connection with
to provide LSP-based enhancements to the buffer. It will use the existing connection any time I open a
new buffer for a file whose mode is one that it is managed. However Eglot is project based, which means
it will not activate if I open a C++ file outside of the current project. Each project will need it's own
invocation of `M-x eglot`.

Eglot being activated has also enhanced the view of our editor in a number of helpful ways:

![Eglot helping](eglot-eldoc.png)

All function calls now have non-obvious arguments prefixed with the name of the parameter. So we can clearly
see that the string I'm passing into `SDL_CreateWindow()`'s first argument is for the `title` parameter. 
If you don't like these, they can be toggled with `M-x eglot-inlay-hints-mode`. You can also bind
`eglot-momentary-inlay-hints` to allow you to see them only when you hold down a specific key.

I also have the point over one of the arguments into the `SDL_CreateWindow` function. At the bottom of the
screen, the `ElDoc` minor mode is now receiving information from the language server to tell me the signature
of the function, it is bolding the parameter relevant to where the point is currently at, and shows me
that the `SDL_WINDOW_SHOWN` is an enum type. 

## Xref

Xref is a framework built into Emacs and takes an identifier (be it a function, macro, variable, etc...) and
find where that identifier is defined and referenced. Xref is just a frontend for actually picking and
navigating cross references and Eglot is one such backend for it. We used the xref system previously when
doing regular expression searches within a project earlier.

We can utilize Eglot and the language server's xref capabilities by invoking `xref-find-definitions`, bound
to `M-.` by default. Emacs will then immediately go to where the symbol under the point is defined.

![Go to definition](go-to-definition.png)

In this case, clangd knows where all my includes are and which include is relevant for the
`SDL_Window_SHOWN` enumeration. 

By invoking the `xref-go-back` command (bound to `M-,`) we will be brought right back to where we were
before we performed the find definition call.  The xref system will maintain a stack of navigations, and no matter
how many times you `M-.` into something you can always `M-,` back.

We can use `xref-find-references` (bound to `M-?`) to open an xref window that shows us all known references
clangd has for what we had under the pointer. 

![xref references](xref-references.png)

We can navigate these options with `n` and `p` (no control modifier needed) and the non-xref window will automatically
update and load the file at that specific point. This provides us a very quick way to navigate references within
our projects. `M-,` will bring us right back to where we were before the xref request.

I need to do a deep dive to figure out what `apropos` is in Emacs, but there is an `xref-find-apropos` function,
which allows you to specify an arbitrary symbol name and get xref results for them. For example, searching for
`pixels` brings up the following:

![xref apropos](xref-apropos.png)

At first test with this, this seems extremely useful. The search is case insensitive and it uses a fuzzy search.

## Eldoc

What if we want additional documentation for what's under the point? 

Eldoc is Emac's at-point documentation system. It provides a mechanism to show documentation based on
what symbol is underneath the point at any given time and is fully compatible with Eglot.

The Eldoc function doesn't just populate basic information in the minibuffer area, it also has it's own
buffer with more comprehensive documentation fed by the language server. This can be opened by typing `C-h .`.

![eldoc buffer](eglot-eldoc2.png)

Obviously this is cramped because I have my Emacs frame small for demonstration purposes, but there is
adaquate space if you are running Emacs full screen. Without changing changing focus you can scroll
the Eldoc window up and down with `C-M-v` and `C-M-S-v` while maintaining the code as your current
buffer. 

The power of Emacs windowing really comes into play here. If you don't want the window below
but want it to the right, you can do:

* `C-x 1` to maximize the current code window
* `C-x 3` to split the current window to the right
* `C-x 4 b` to switch the other window's buffer to `*eldoc*`.

Now the documentation is on the right side of the screen, and as you move the cursor around to different
parts of the code the documentation window automatically updates. 

![eldoc to the right](eglot-eldoc-right.png)

You can also do `C-x 5 b` to open the `*eldoc*` buffer in a new Emacs frame, allowing you to place it on a 
completely different monitor.

## Imenu

Emacs has a function called `imenu` (bound to `M-g i`) which provides a minibuffer completion menu of top level symbols in the
current buffer that can be navigateid to. So if you are in a file that contains a lot of functions and you
know a keyword from the function you want to navigate to, the imenu is a quick way to jump right to it.

This is quick because it's only root level symbols for the current file and thus doesn't need to look
through the entire project. 

## Flymake

Flymake is the on the fly diagnostic annotation system. Eglot adds its own backend to surface
language server diagnostics through flymake. If we type `window` in the line after
our window `nullptr` check we get the following

![flymake annotations](flymake-annotations.png)

We can now see the `window` we typed has a yellow squigly and an exclamation mark, while
the next line's `SDL_Renderer` clause has a red squiggly and two red exclamation marks. We
can also see the Flymake entry in the mode line shows 1 warning and 1 error.

Flymake messages get surfaced in Eldoc, so if we still have the window split we can see
the errors from our language server right there.

![flymake in eldoc](flymake-in-eldoc.png)

We don't have to manually go to the error to see it though. We can use the
`M-x flymake-goto-next-error` and `flymake-goto-prev-error` to navigate
between them.

We can then fully utilize the language server by running
`M-x flymake-show-buffer-diagnostics` to see a list of diagnostic messages for the
current buffer or `M-x flymake-show-project-diagnostics` to show all diagnostics
for the whole project. 

These will open a window with a list of diagnostics, what file they exist in,
and allow `n` and `p` navigation. While navigating the diagnostic messages you can use `SPC`
to have the main window show the code that the diagnostic message is for, and `RET` to actually focus
the code buffer at the point of the diagnostic.

## Completion At Point

Since we added the corfu package for in-buffer completion popups, we can get auto-complete
style code completion that's supported by the language server. So typing `win<TAB>` produces
the popup. While the popup is active we can use `C-n` and `C-p` to cycle through the options,
and as we select hte options we also get a side popup with documentation for the selected
option:

![code completion example](code-completion.png)

One interesting note is that as you select options, what you currently typed shows a preview
of the selected option **with the full signature**. Obviously, that would not be ideal
in practice because you would have to go back and replace the arguments with your own.

Luckily, you don't have to as once you select and option and press `TAB` or `RET` it will
not include any of the arguments or even the parenthesis for functions.

One thing that tripped me up a few times is that if you type `window` and hit `TAB` it almost
seems like nothing is happening. However if you look closely at the bottom you will see 
`sole match`, so it recognizes that it's at the end of the only option that it could be.

It may be desirable to expand the max width in corfu so that you can see more, if desired.

## Code Actions

Language servers expose refactoring capabilities, and Eglot exposes those.

If we put the point on `window` and do `M-x eglot-rename`, we can now rename the `window`
variable to anything and it will be renamed only for that logical symbol, even if
other functions in the same file have their own `window` local. Of course using
undo `C-/` undoes the whole rename.

There are a variety of code actions that the language server may expose based on what
code the point is at. The code actions available depend on the language server involved.

So if you put your point over a warning or error flymake indicator and perform 
`M-x eglot-code-actions` you'll get a list of actions the language server allows for that
specific spot in the code. 

There are more specific actions that can be performed:
* `eglot-code-action-organilze-imports`
* `eglot-code-action-quickfix`
* `eglot-code-action-extract` 
* `eglot-code-action-inline`
* `eglot-code-action-rewrite`

All of these require support by the language server at the spot the point is currently at, or
the region that has been selected.

`M-x eglot-format-buffer` will reformat/indent the whole file, while `M-x eglot-format` will format
the selected region.

## Other Languages

So C++ was pretty easy. C++ is pretty common (from the perspective of non-IDE editors)
and clangd seems to be the most common C++ language server. It is also installed with
clangd, which is a very common toolchain for C++ development.

So let's try some other languages. Unfortunately, this is where things started breaking
down for me.

### C#

If we open up a C# file and `M-x eglot` we get:

> [eglot] Couldn't guess LSP server for 'csharp-ts-mode'
> Enter program to execute (or <host>:<port>)

So I type `roslyn-language-server` which is the C# language server that seems to be
officially supported and ....

> error in process sentinel: [eglot] -1: Server died

Well that isn't very helpful. Lets open an "other" window with the relevant
eglot events buffer:

![buffer search](eglot-events-buffer1.png)

Make sure to select the right one if you Emacs session has multiple projects
open at the moment. Opening it then shows the event buffer:

![events buffer](eglot-events-buffer2.png)

So the events buffer shows that the server is throwing an exception immediately
because it's expecting an `--stdio` or `--pipe` option. Ok sure, so lets go back
to our C# buffer and when prompted for the language server program to execute
lets do `roslyn-language-server --stdio`.

This appears to work at a first glance (eglot claims it's running on the project)
but we quickly find out it's extremely limited. It seems to be working for
xref and other operations only within the file itself and not in the larger
project. I pulled up the eglot events and found a bunch of `Internal Error`
messages from the client side (not from the language server!). 

So I ran `M-: (setq debug-on-error t)` (the `M-:` allows evaluating arbitrary
elips expressions ad-hoc), then `M-x eglot-reconnect`. This popped up a buffer
with a lisp error on `eglot--glob-parse`. 

To try and not get into the weeds, the
[LSP 3.17 specification](https://microsoft.github.io/language-server-protocol/specifications/lsp/3.17/specification/#globPattern)
added the ability for glob patterns to be either single pattern strings, or a `RelativePattern`
object (with a baseUrl and pattern properties). Unfortunately, it seems like eglot's 
glob parsing code only supports pattern strings, not objects. This appears to have been
fixed in [a commit back in December 2025](https://lists.nongnu.org/archive/html/emacs-diffs/2025-12/msg00219.html)
but since the current Emacs version of 30.2 was released in August 2025 that fix isn't in my
current Emacs build.

I tried installing the ELPA official package for elgot whose 1.24 version was released
back in July 2026, but it didn't work either (the error messages looked a bit different
but were all glob related still).

### Typescript

While I work on Node and Typescript projects at work, I don't do much in my personal time.
So I downloaded some Typescript examples just to see how they worked.

So I downloaded an open source typescript tetris game from Github, opened up the `app.ts` 
file and loaded eglot. Eglot immediately fails with an error about not finding a Typescript
environment. I definitely have both `tsc` and `typescript-language-server` available in my `$PATH`
so I'm not sure what to make of that. 

This error came from the language server and not Eglot, and is just a quirk on typescript, where
you need to run `pnpm install` so that it has a `node_modules` folder with its own typescript
environment. I code in Typescript as part of my day job but I don't manage the infrastructure around
it so this is definitely a me mistake.

What is an Emacs/Eglot quirk (at least for newbies like me) is that the error shows in the minibuffer
and goes away pretty quickly. The Eglot event buffer just showed an initialization request sent
and nothing after that. The `M-x eglot-stderr-buffer` command seemed to only show me a message
in the minibuffer of "No current JSON-RPC" connection and thats it. I did eventually find the 
typescript environment error in the `*Messages*` buffer and it at least showing that Eglot exited
with that error. 

I should check the `*Messages*` buffer more often, but it also has no timestamping or easy way to
correlate when and how a message was received, so it can be hard to parse.

Either way, fixing that issue now has the typescript language server integration fully up and
running!

![typescript working](ts-working.png)


### Verilog

Opening up a verilog file and running `M-x eglot` shows the "Couldn't guess LSP server"
prompt. So I enter `verible-verilog-ls` and eglot shows

> 'Verible Verilog language server.' now managing 'nil' buffers in project

Thats... interesting. Typing `M-.` to go to definition asks me
`Visit tags table (default TAGS)` which I have no idea what it means. Selecting an option
removes all highlighting as it puts it into TAGS major mode. Going back to `verilog-mode`
ends up bringing highlighting back but with a read-only buffer.

Even after refreshing eglot, `M-?` gives me `Sole Completion` responses on everything. I can
not find an events buffer for the verilog mode, nor did the `stderr` for eglot show anything
useful. Unfortunately, that left me stuck at this point.

At some point later I did find an events buffer for this specific sesion, and all I can
figure out is that Eglot gets stuck after sending `workspace/didChangeConfiguration`, and
then eventually the verible process ends. This language server did work with my Neovim
setup, so, I don't think that's it.

I can also verify that the `M-?` and `M-.` commands are not triggering eglot events in this buffer.

## Conclusion

Eglot is not the only language server support in town, so maybe we'll have an easier time
with another one.

I want to stress that this was not wasted effort though. Eglot and it's docs taught me
a lot about xref, imenu, and other Emac native systems that will be relevant to other
packages.
