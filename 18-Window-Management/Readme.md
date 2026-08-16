# Window Management

So far I have been bumbling my way around managing windows in Emacs, so it's time
to actually make it fit my workflows.

## Reverting Window Mistakes

One problem I keep bumping into is a window opens that I didn't expect and it
ends up hiding a buffer I actually wanted there. A good example that keeps coming up
is buffer selection.

`C-x b` allows you to select a buffer to open, but buffer selection occurs in the minibuffer
with the `fido-vertical-mode` selection. This is ideal for me in most scenarios because I
want to select a buffer and move on, I rarely need the active buffer list to stay around.

`C-x C-b` will open a full buffer in the other window that allows me to make a buffer
selection in the main window. The buffer list window does not gain focus, meaning I
can't type a buffer name to make a selection, I have to manually `C-x o` to focus
on it, and it changes the buffer for the other window (not necessarily the window I 
made the `C-x C-b` command in).

So if I `C-x g` to open magic in the other window, and then want to change the buffer
of my current window, if I hit control on the second letter I'm now in a frustrating
state.

![buffer window](buffer_window.gif)

This is flow breaking and requires some thought to get things back to how they were.

Luckily, Emacs has a solution to this called `winner-mode`. This is a global mode that 
tracks changes in window configuration and allows them to be undone. If you make a
mistake you can undo that mistake with `C-c <left>` or redo with `C-c <right>`.

![winner mode example](winner-mode.gif)

With winner mode active, if I accidentally do `C-x C-b` I can just do `C-c <left>`
and be right back in my previous state.

We can make this permanent by adding the following to the `use-package emacs` `:config`
section:

```elisp
(winner-mode 1)
```

