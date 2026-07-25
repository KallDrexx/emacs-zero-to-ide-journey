# Installation

Emacs installation was pretty trivial on Linux. 

On MacOS it was somewhat less straight-forward. There seems to be three main distribution of 
Emacs for OSX
* [EmacsForMacOSX.com](https://emacsformacosx.com/)
* [emacs-mac](https://bitbucket.org/mituharu/emacs-mac/src/master/)
* [emacs-plus](https://github.com/d12frosted/homebrew-emacs-plus)
* [homebrew emacs](https://formulae.brew.sh/formula/emacs)

From what I can tell, `emacsformacosx.com` is basic Emacs and might have some compatibility problems 
(or at least Doom's documentation discourages it for that reason). 

The normal homebrew `emacs` works fine but does not provide an `Emacs.app` for spotlight support.

`emacs-mac` and `emacs-plus` both seem to have good Mac OS integrations, native compilation of elisp,
and seem to be well regarded. Either one probably works fine. 

They both also have an `Emacs.App` and `Emacs Client.App` that can be symlinked into your `/Applications`
folder, so they can be found (though this will need to be done manually).

