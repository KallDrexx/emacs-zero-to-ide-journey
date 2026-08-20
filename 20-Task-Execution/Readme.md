# Task Execution
<!-- markdown toc start -->
**Table of Contents**

  - [Ad-Hoc Commands](#ad-hoc-commands)
    - [Compilations](#compilations)
  - [Per Directory Local Variables](#per-directory-local-variables)

<!-- markdown toc end -->

It is a common need to run commands to perform different actions against your project
from within your editor. VSCode and Zed both use `task.json` files to specify how
they are run, and JetBrains IDEs have run configurations. Lets see how we can
do the same in Emacs!

## Ad-Hoc Commands

One way to execute arbitrary commands is with the `M-!` key binding. This will ask you
to specify what shell command to run and it will run. Once it is finished it will open
a buffer with the results.

**This is run synchronously**, which means if you run a command that takes a while (like
a fresh compile or a test run) your Emacs instance will be frozen until it has finished
(or until you `C-g` spam enough times to interrupt it).

Instead you can use `M-&` to run a shell command asynchronously. This will open an
`*Async Shell Command*` buffer with the results, and the results will stream into
the buffer as they are executed. This is usually more ideal than the synchronous
version unless you are absolutely sure the shell command won't block.

### Compilations

If you want to compile your code, then `M-x compile` is a better command than `M-&`. When
you run it you will be prompted for the command to run to compile the project. It will default
to `make` but you can change it to anything (such as `cargo build`, `npm build`, `dotnet build`, etc...). 
It will open it up in a compilations buffer but it will be run asynchronously and not block anything.

This explicit compilation command has several advantages than just running `M-&` alone:

1. It shows the compilation status and exit code in the mode line
2. It remembers the last compilation command, so another `M-x compile` defaults to it
   or a `M-x recompile` immediately re-executes it.
3. It uses regular expressions to find errors. It highlights any errors it finds and makes
   them clickable directly to the buffer for the file and placing the cursor directly on the
   line that caused the error.
4. You can use `M-g M-n` and `M-g M-p` to navigate between the errors in the compilation buffer
   (well in theory, for some reason this didn't work for me in my tests).
   
![compilation log](compilation.png)

![compilation log](compilation2.png)

![compilation log](compilation3.png)

You can also execute this from the project root via `C-x p c`. This is required if you are
currently viewing a file and need to run a script that's not in that same file's directory.

It seems like error detection works for many languages but not rust. The supported languages can
be found in by describing the `compilation-error-regexp-alist` variable. At a quick glance I wasn't
able to find a quick solution to getting rust errors highlighting. Most other languages I tried
seemed to work though.

The fact that this remembers the last compilation command is a blessing and a curse. It does
not remember the last command on a project by project basis but globally, so if you need to
swap between projects you will constantly have to change the command.

## Per Directory Local Variables

When opening files and projects Emacs looks for a `.dir-locals.el` file, and if it exists it
will load the variables from that file. This can be used to force major modes, indentations,
etc... 

However it can also be used for project wide settings. For example, adding the following
to the `.dir-locals.el` in a rust's project root:

```elisp
((nil . ((compile-command . "cargo build"))))
```

Now opening the project with `C-x p p` and then running `C-x p c` to compile the project will
automatically set the default compilation target to `cargo build`. Unfortunately, once you change
this it doesn't stick so that limits its usefulness a bit.

Another option is to specify a list of lambdas contained within a variable that could be
executed. So in my video rust project I have the following in my `.dir-locals.el`

```elisp
((nil . ((my-project-commands
          . (("Build" . (lambda () (compile "cargo build")))
             ("Test"  . (lambda () (compile "cargo test")))
             ("Run Video Relay" . (lambda ()
                                     (let ((default-directory (project-root (project-current t))))
                                       (async-shell-command "cargo run --bin video-relay")))))))))
```

By default this does nothing because nothing knows about a `my-project-commands` variable. However,
we can add the following into our `init.el` to run it:

```elisp
;; Allow executing project commands
(defvar-local my-project-commands nil
  "Alist of (LABEL . THUNK) commands, normally set via `.dir-locals.el'.")

(defun my/project-run-command ()
  "Interactively select and run one of the buffer's `my-project-commands'."
  (interactive)
  (unless my-project-commands
    (user-error "No `my-project-commands' defined for this buffer"))
  (let* ((choice (completing-read "Run command: "
                                   (mapcar #'car my-project-commands)
                                   nil t))
         (thunk (cdr (assoc choice my-project-commands))))
    (if (functionp thunk)
        (funcall thunk)
      (user-error "`%s' is not callable" choice))))

(global-set-key (kbd "C-c p r") #'my/project-run-command)
```

After evaluating this and re-opening our project we can now use `M-x my/project-run-command` and get
a list of available commands for that project:

![task selection](task-select.png)

This works but has some quirks. The first quirk is that Emacs doesn't automatically notice an update to
`.dir-locals.el`, and so the easiest way I have found to make it take effect is to kill all project
buffers and then reload the project `C-x p k C-x p p`. Since editing this file probably isn't an everyday
occurrance that's probably fine.

The bigger issue is that having lambdas in a `.dir-locals.el` file triggers a warning from Emacs
*every time you open a file in this project*. That's because if a malicious actor adds code into a
variable it can get code execution. You can mark this specific variable as safe by adding the following
in the `init.el`.

```elisp
(put 'my-project-commands 'safe-local-variable #'listp)
```

You just have to be aware that if you download a project with that same variable name wiht malicious
code, it could cause execution of code you didn't intend. You will need to evaluate that risk on
your own.
