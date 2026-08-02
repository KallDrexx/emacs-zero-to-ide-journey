# Packages 

<!-- markdown toc start -->
**Table of Contents**

  - [Package Management](#package-management)
  - [Package Archives](#package-archives)
  - [Use-Package Macro](#use-package-macro)

<!-- markdown toc end -->


Packages are how Emacs gets additional functionality added in. 

## Package Management

Some packages are built-in while others are provided by third-parties and 
need to be added via a package manager. 

There are several main package managers that I have come across

* `package.el` - The built-in package manager
* [straight.el](https://github.com/radian-software/straight.el)
* [elpaca.el](https://github.com/progfolio/elpaca)

From what I can tell, `elpaca.el`'s benefits are its asynchronous nature. Installing
new packages usually blocks Emacs operations while with `elpaca.el` you can continue
using most of Emacs functionality while the package installs in the background. 

I can definitely see how this would be useful for more advanced users or users
who are always modifying what packages are installed or packages themselves on
a regular basis. I tried it briefly and the asynchronous nature threw me off as
a new Emacs user. I tried to use functionality and random things did not work as
expected (because the packages have yet to install). Then "randomly" things would
start working as I expect.

`straight.el` is one that's heavily recommended because it downloads packages
as source repositories instead of tarballs, which in theory make it easier
to version and modify locally. 

For now I'm going to stick with the built-in `package.el` until I find myself
with a good reason to change.

## Package Archives

Emacs pulls in packages from what it calls a package archive. The default package
archive is called [ELPA (Emacs List Package Archive)](https://elpa.gnu.org/) and is
run by the GNU organization. While it has a sizable collection of packages, many
packages I have come across are not on there but instead on a package archive
called [MELPA](https://melpa.org/#/).

You can add support for pulling packages down from MELPA by adding the following
to your `init.el`:

```elisp
(require 'package)
(add-to-list 'package-archives '("melpa" . "https://melpa.org/packages/") t)
```

The `(require 'package)` is important, as it ensures that the package management
system is enabled. If it isn't, then there is no `package-archives` list and an
error will occur.

## Use-Package Macro

To actually install a package, the `use-package` macro is used (the same macro
we used to configure emacs settings in the last post). Packages are then
installed an managed via a syntax like:

```elisp
(use-package <package-name>
  :ensure t
  :after <other-package-name>
  :init
  (code-to-run-before-loading)
  :config
  (code-to-run-after-loading)
  :bind (<keys-to-bind>)
  :hook (<event> . <command>)
```

There are other macro sections but these are the main ones I see used.

* `:ensure` tells the package manager to load the package. This seems
  to always be `t` for third party packages, while `:ensure nil` is
  used for internal Emacs packages.
* `:init` is used to run some code before the package is loaded. This seems
  to be done in order to have variables set and ready to go so the package's
  loading code can read and utilize them.
* `:custom` is used to provide a list of variables that should have 
  `customize-set-variable` called for.
* `:config` is used to specify code that should be run after it is loaded. This
  is usually done to set configuration values for the package.
* `:bind` allows binding keys for the package after it has been loaded
* `hook` allows running code after an event fires. I see this used a lot to run
  a specific command once a Major mode is loaded. One example of this is to make
  it so if a programming mode is activated on a buffer then show line numbers.

We'll see our first practical use of this macro in the next section to setup a theme.

