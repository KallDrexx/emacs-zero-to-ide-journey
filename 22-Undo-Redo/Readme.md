# Undo and Redo

Undo and redo in Emacs works differently than in most editors, and it can be confusing.

## How Undo Works

Pressing `C-/` (or `M-x undo`) will undo the last change you made, and repeated presses 
will keep undoing the actions before that. That's pretty standard. 

How Emacs is different is by what happens next. If you type
a command that's not a an undo command (like `C-f` to move forward) everything you
undid now gets added to the undo chain as individual redos. Pressing `C-/` will end up doing
a redo, and subsequent presses will do the redos, until you run out of redo operations
and then undos start occurring.

![undo](undo.gif)

You can use `M-x undo-only` to not cause undo to do a redo. You can also `M-x undo-redo` which
will undo the last undo command (so it will force a redo).

It can get pretty confusing and I've definitely gotten myself into weird undo/redo loops.

## Visualing Undo

The undo system makes a lot more sense when you can visualize it. One way this can be done
is with the [vundo package](https://github.com/casouri/vundo). It can be enabled with

```elisp
(use-package vundo
  :ensure t)
```

When activated with `M-x vundo` it will bring up a graph of all the different undo states
that Emacs has recorded.

![vundo](vundo1.png)

The yellow `x` is the state you are currently and the green `o` is what state has been
saved to the disk. You can then navigate the tree with arrow keys or `f/b/p/n`, and as you
move around the graph the current buffer will preview what it looks like with those changes.

Pressing `d` will show the diff of what changed in that specific undo state, however that is
not very useful since Emacs seems to create new undo steps pretty often and these diffs will
be granular.

This can be made useful though by pressing the `m` key to mark one of the states. This makes that
undo state a blue `x`. Now when you move to another state you can press `d` and see the difference
between the two states.

![diff](vundo-single-line-diff.png)

This also works across branches to see what changed

![branch diff](vundo-branch-diff.png)

You can also utilize the diff state and press `?` in the diff window to get helpful actions. This 
can allow you to take content you wrote previously and re-apply it to the buffer without going
all the way back to the branch point.

While navigating through the undo states, you can then either select a state and press `RET` to change the buffer to the selected state, or
`C-g` to exit vundo.

As you type, if you bring vundo back you will start seeing it add new branches.

![branches](vundo-new-branch.png)

Running `M-x vundo-popup-mode` will enable vundo to pop up so you can see the current tree
when you execute an undo command.

## Increasing Undo State Storage

One thing that the `vundo` package made obvious is that the Emacs garbage collector is pruning undo
states pretty aggressively. You'll be writing some code/text/etc... and upon trying to undo something
you will notice the graph being much smaller and with less branches than you expect. It will also display
a message that the Emacs garbage collector has removed undo states.

[According to the Emacs manual](https://www.gnu.org/software/emacs/manual/html_node/emacs/Undo.html) this
is controlled by three settings (all measured in bytes of data)

* `undo-limit` - Soft limit of how much undo data is kept
* `undo-strong-limit` - Hard limit to keep undo data below
* `undo-outer-limit` - Single command undo size limit

From what I can tell if your total undo limit goes beyond `undo-limit`, the next garbage collection
will remove the oldest undo commands in the collection while keeping the most recent undo states. This
means your total undo data can still remain larger than `undo-limit` if some of your most recent
undo commands have a large amount of data.

If the `undo-limit` culling doesn't lower memory (due to large recent undo states) and the total
memory usage reaches `undo-string-limit` at the next garbage collection, then it will no longer
care if undo states are recent and cut them (it may leave the last undo state though).

The `undo-outer-limit` is a limit that's immediately evaluated (not at garbage collection). If a
single undo operation is greater than that size, it will not be saved as an undo state at all.
This is a safety mechanism to prevent out of memory issues due to a really large allocation
that could fail.

The default values for all of these are extremely small, 160,000 bytes for `undo-limit`,
240,000 bytes for `undo-strong-limit`, and 24,000,000 bytes for `undo-outer-limit`. Just like
in the garbage collection threshold we set below, Emacs' defaults are built for a different
era with the assumption of much lower memory than we have today.

Depending on how much you value memory usage, you can use `setq` at the beginning of your
`init.el` to set these values to what you want. I have mine set as:

```elisp
(setq undo-limit (* 10 1024 1024))          ;; 10 MB
(setq undo-strong-limit (* 100 1024 1024))  ;; 100 MB
(setq undo-outer-limit (* 1024 1024 1024))  ;; 1GB
```

This should keep the undo tree from pruning too much while having quite a lot of headroom
on my computers with 32GB of RAM.

## Persisting Undo State

Sometimes it can be helpful to maintain undo states even after you kill a buffer or restart
Emacs. This can be done with the `undo-fu-session` package, added via:

```elisp
(use-package undo-fu-session
  :ensure t
  :custom
  (undo-fu-session-linear t)
  :config
  (undo-fu-session-global-mode)
  )
```

This will save undo states in the emacs folder with gzipped compression, and will load them back
as needed. The `undo-fu-session-linear` just makes it so it doesn't save branches (since those
usually represent changes that were then undone).

Once applied, you can make some changes, restart emacs, then use `M-x vundo` to see what you did
in your previous sessions.

## Conclusion

JetBrains IDEs have a [Local History feature](https://www.jetbrains.com/help/idea/local-history.html) which
I find very useful at times, since it provides different views of a file in between VC commits. I don't use
it a ton, but when I need to reference something that I added then removed it becomes extremely useful.

Emac's undo tree visualized with `vundo` and persisted across sessions comes really close to this for my
purposes.
