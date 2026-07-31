# Enhancing The Minibuffer

## Default Functionality

When you activate a command in the minibuffer (such as `M-x` to run
an interactive function), it's a bit basic and not very discoverable.

![bare minibuffer](bare-mb.gif)

Pressing `<Tab>` will provide what Emacs calls `completing-read`, which is just
its term for auto-complete in the minibuffer. Pressing `<Tab>` once will complete
the function name up until the most common known prefix. So typing `eva<Tab>`
causes the minibuffer to complete to `eval-`, since multiple functions exist
that start with `eval-`.

Pressing `<Tab>` a second time pops up a list of options, but those options
don't actually update as you continue typing.

So let's improve this experience!

## Immediate Feedback

The biggest improvement we can get is having an immediate list of options
as we start interacting with the minibuffer.

Emacs has a solution to this built-in, called `fido-vertical-mode`. This
expands the minibuffer to provide contextual options as you type. You can
experiment with it by calling the `fido-vertical-mode` interactive function
with `M-x`. 

![fido-vertical-mode demo](fido-mode.gif)

This can be activated by default by adding `(fido-vertical-mode)` to the
`:init` section of the `use-package emacs` declaration.

## Adding More Info

Most of the Emacs interactive function have comments that tell you what
each function does. `fido-vertical-mode` provided a list of options, but
if you don't know what each function does then that limits its helpfulness.

This requires a third party package, [Marginalia](https://github.com/minad/marginalia).
We can add it by adding the following to our `init.el`:

```elisp
(use-package marginalia
  :ensure t ;; always install
  :init
  (marginalia-mode))
```

Save this and re-evaluate the buffer, and now you'll see some helpful documentation
along-side the available interactive functions.

![marginalia m-x example](marginalia-1.png)

It also adds useful info to other minibuffer completions, such as the find file
results.

![marginalia find file](marginalia-2.png)





