# Emacs Variables

There are quite a few variables built into emacs that controls it's
behavior. 

Emacs contains a lot of documentation. Most variables can have its documentation
shown by typing `M+x describe-variable`.

## Customization UI

Emacs has a customization user interface, which allows changing behavior of
emacs through a graphical interface rather than programmatically. In theory this
lets aspects of Emacs changes to be more discoverable.

When changes through the customization system are made, it is saved by writing
the equivalent elisp code. It's not just manual customizations that can trigger
this code generation though, some `init.el` configuration code can cause
the code generation to occur as well.

By default, this generated code goes into your initialization file (e.g. 
`init.el`). This can be a bit messy, but you can add code to your emacs
initialization to keep auto-generated customization code in a separate file.
This is done by adding the following elisp:

```elisp
(setq custom-file (locate-user-emacs-file "custom-vars.el"))
(load custom-file 'noerror 'nomessage)
```

The name doesn't have to be `custom-vars.el`, it can be anything you wish.
The `locate-user-emacs-file` function will ensure its in the same directory
as the `init.el` file.

## Menu bar

If you don't like the menu bar at the top (File, Edit, etc...) then that can
be disabled with `(menu-bar-mode -1)`. That being said I find the menu bar
does not take much space and is helpful for remembering key binds and 
major mode functionality. Therefore, I leave it enabled with `(menu-bar-mode 1)`.

## Disabling the Scroll Bar

If you do not want to see the scroll bar, it can be disabled via 

```elisp
(when scroll-bar-mode
  (scroll-bar-mode -1))
```

## Fonts

Since I come from a JetBrains background, I am used to using the `JetBrains Mono` font, not
only in my IDE but also in my terminal. I also use the
[Nerd Font](https://www.nerdfonts.com/) variation for the extra unicode icon support.

Regardless of what font you wish to use, you can get Emacs to use them in the GUI via

```elisp
(set-face-attribute 'default nil :family "JetBrainsMono Nerd Font" :height 100)
```


## Tab Indentation

The tab key does not work the same way as it does in most other editors. In most text
editors indentation isn't a real concept, but instead is purely a visual idea we have
for text alignment. While many editors have commands for changing the amount of white-space
at the beginning of a line, pressing the `<Tab>` key will insert either a tab code or
spaces where your cursor is.

Emacs does not do this, and the `<Tab>` key will only manage the indentation level of
the current line of text. In most cases it will only *fix* the current line's indentation
level based on the settings in the buffer's major mode (the active driver controlling
language and text settings in the current buffer).

Tab width and tabs vs spaces can be configured with the following settings:

```elisp
(setq tab-width 4)             ;; Set the tab width to 4 spaces.
(setq indent-tabs-mode nil)    ;; Disable the use of tabs for indentation (use spaces instead).
```

## Misc Variables

Most of the following variables I have taken from the 
[Emacs-Kick init.el](https://github.com/LionyxML/emacs-kick/blob/master/init.el#L213)
configuration file, as they are defaults that make sense for me.

```elisp
(setq auto-save-default nil)                         ;; Disable automatic saving of buffers.
(setq column-number-mode t)                          ;; Display the column number in the mode line.
(setq create-lockfiles nil)                          ;; Prevent the creation of lock files when editing.
(setq delete-by-moving-to-trash t)                   ;; Move deleted files to the trash instead of permanently deleting them.
(setq delete-selection-mode 1)                       ;; Enable replacing selected text with typed text.
(setq display-line-numbers-type 'relative)           ;; Use relative line numbering in programming modes.
(setq global-auto-revert-non-file-buffers t)         ;; Automatically refresh non-file buffers.
(setq history-length 25)                             ;; Set the length of the command history.
(setq inhibit-startup-message t)                     ;; Disable the startup message when Emacs launches.
(setq initial-scratch-message "")                    ;; Clear the initial message in the *scratch* buffer.
(setq ispell-dictionary "en_US")                     ;; Set the default dictionary for spell checking.
(setq make-backup-files nil)                         ;; Disable creation of backup files.
(setq pixel-scroll-precision-mode t)                 ;; Enable precise pixel scrolling.
(setq pixel-scroll-precision-use-momentum nil)       ;; Disable momentum scrolling for pixel precision.
(setq ring-bell-function 'ignore)                    ;; Disable the audible bell.
(setq split-width-threshold 300)                     ;; Prevent automatic window splitting if the window width exceeds 300 pixels.
(setq switch-to-buffer-obey-display-actions t)       ;; Make buffer switching respect display actions.
(setq tab-always-indent 'complete)                   ;; Make the TAB key complete text instead of just indenting.
(setq treesit-font-lock-level 4)                     ;; Use advanced font locking for Treesit mode.
(setq truncate-lines t)                              ;; Enable line truncation to avoid wrapping long lines.
(setq use-dialog-box nil)                            ;; Disable dialog boxes in favor of minibuffer prompts.
(setq use-short-answers t)                           ;; Use short answers in prompts for quicker responses (y instead of yes)
(setq warning-minimum-level :emergency)              ;; Set the minimum level of warnings to display.
```


