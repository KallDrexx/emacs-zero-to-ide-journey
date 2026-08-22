# Debugging

While many developers can work fine with debugging via log files, being able to
place breakpoints and step through your algorithms one function at a time
can really speed things up.

So lets set Emacs up for interactive debugging.

## Dape

Just like there is a protocol for language servers, there is also a protocol for
debugging called the Debug Adapter Protocol (DAP). This allows for non-language
specific editors to interact with language/runtime specific debuggers without
having to explicitely code for each language and runtime.

One package that provides integrations into this [Dape](https://github.com/svaante/dape).

It supports conditional and non-conditional breakpoints, a locals/watch window,
thread viewer, and a lot of the functionality you would see in a vscode or zed
debugging session.

Lets get it enabled by adding the following to our `init.el` file:

```elisp
(use-package dape
  :ensure t
  :hook
  (kill-emacs . dape-breakpoint-save)
  (after-init . dape-breakpoint-load)

  :custom
  (dape-buffer-window-arrangement 'right)
  (dape-breakpoint-global-mode +1) ;; Supposedly allows setting breakpoints with the mouse
  )
```

After evaluating the package should install. 

The primary commands needed to get started I found were
* Breakpoints can be toggled with `C-x C-a b` (`dape-breakpoint-toggle`). 
* Execute to the next line via `C-x C-a n` (`dape-next`)
* Step into the next function via `C-x C-a s` (`dape-step-in`)
* Step out of the current function via `C-x C-a o` (`dape-step-out`)

The step functions (especially step to the next line) can be pretty annoying to hit if you
want to keep executing it over and over again quickly. Emacs has a "repeat last command"
command which is `C-x z`. After you hit that it will repeat the previous command, and
repeat it every time you hit the `z` key again.

So `C-x C-a n C-x z z z z` will have dape execute the next 5 lines of code. That's not just
a dape thing, but it definitely came in handy for it.

### C++

C++ just worked with gdb (which it defaulted to). 

![dape running](cpp-debug.gif)


### 
