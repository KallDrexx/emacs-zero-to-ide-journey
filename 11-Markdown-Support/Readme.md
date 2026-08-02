# Markdown Support

So far I have been writing these posts as plain text files and validating
they render as expected outside of Emacs. It's time to fix that.

The bottom bar in Emacs is called the `mode line`. This contains all different
types of information relevant to the current/active buffer.

So for example, I see the following when looking at my `init.el` file:

![init.el mode line](init-mode-line.png)

There is a section on the mode line that's in parenthesis, these are the modes that
are active in the buffer. A mode is a set of functionality that defines the editing
behavior of the buffer while that buffer is the current one. Each buffer can have
one major mode and additional minor modes to enhance the capabilities.

The screenshot above shows that I am in the `Elisp` major mode, with a `WK` (which-key)
and `Eldoc` minor modes. The `Elisp` major mode is what gives us syntax highlighting,
completion-at-point support, etc...

If we go to the markdown file I am currently writing, we see:



![md mode line](md-mode-line.png)

We can see this shows the `Fundamental` major mode and the `WK` minor mode. The 
`Fundamental` mode is the most basic text editing major mode that Emacs has.

In order to make Emacs more functional for writing markdown, we need to enable a markdown
specific major mode. Unfortunatly, as of Emacs 30 (the current released version) there is
nothing built in. Emacs 31 will have a native markdown mode, but that is only in very
early preview.

So we need to reach out to third party packages. The two main packages are:

1. [markdown-mode](https://jblevins.org/projects/markdown-mode/)
2. [markdown-ts-mode](https://github.com/LionyxML/markdown-ts-mode)

`markdown-ts-mode` is what was enhanced and merged into Emacs 31. So at first it seems
like I should use that one. However from a quick glance it appears slightly less
feature-full, less documented, and requires third party tree-sitter support. I will need
tree-sitter, but I will have to dedicate some time to properly tackle that.

From what I have seen the Emacs 31 native markdown-ts-mode might be a better choice once
that's released. It will be easy to switch once that occurs.

To activate `markdown-mode`, I just had to add the following to my to my `init.el`:

```elisp
(use-package markdown-mode
  :ensure t
  :mode ("README\\.md\\'" . gfm-mode)
  :init (setq markdown-command "multimarkdown")
  :bind (:map markdown-mode-map
         ("C-c C-e" . markdown-do)))
```

And now I have highlighting that makes it easy to scan my document. More importantly,
I can now use `C-c C-x C-i` to toggle inline images, and now can see the images while
editing.

There are a lot of options and commands available in this mode, and maybe I will explore
them a bit more in due time. I can explore a lot of different options using `C-c` and
letting `which-key` pop up available options. 

It seems that `C-c` tends to be used by different modes to expose custom functionality.
