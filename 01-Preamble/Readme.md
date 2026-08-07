# Preamble

<!-- markdown toc start -->
**Table of Contents**

  - [Why DE-IDE](#why-de-ide)
  - [Why Not Neovim?](#why-not-neovim)
  - [Why Vanilla Emacs](#why-vanilla-emacs)

<!-- markdown toc end -->

This post reflects on the personal motivations for going on this Emacs journey, and why
this document even exists. It's mostly designed around giving some optional context to
the framing of the articles that will follow. 

Feel free to skip.

## Why DE-IDE

I have been programming for over 30-ish years now, first as a hobby and then later as a career. 
While I don't remember all the tooling I used when I was young, most of that time has been inside
of graphical IDEs, such as JetBrains' collection and Visual Studio. 

Even during my high school days when I was using Linux, I was using graphical IDEs whose names
I can no longer remember (maybe they no longer exist).

I am a proficient VIM user and have used VIM keyboard emulation for such a long time. 
However I have never actually done much coding in VIM itself. 

Graphical IDEs were always worth it to me because of the extra tooling they provided, and I
got a lot of value from them.

These days though, most IDEs are starting to frustrate me. They all have AI offerings that I don't
want, settings that never sync properly between machines or installs, lack of customizations
as my dev workflows change, lack of flexibility as the dev landscape evolves, and other paper 
cuts that keep building up over time. 

Even modern but leaner editors like Zed have strict opinions of how they want you to operate, and
only allow customization through a heavily opinionated plugin API. Want vertical tabs? Well that
goes against their design goals and thus it's not possible without source modifications. Want an
in-editor SQL row editor? That also requires source modifications.

## Why Not Neovim?

I started with Neovim due to my familiarity with VIM keybindings. I read
[Duy NG's amazing blog](https://tduyng.com/blog/neovim-basic-setup/) and ended up
getting a completely custom Neovim setup in no time that was a good starting point.

It has full LSP support, Overseer for Makefile/task execution, lazygit for a good
git UI right inside, good file tree and file search/grep search interfaces, etc...
It doesn't have everything I need yet, but I got this up and running in a few days.

If I hadn't looked at Emacs I probably would have just stuck with
Neovim. Now that I've looked at Emacs though, I can't turn off the tinkerer part of
my mind and want to give it a real chance.

There are a few reasons why Emacs is compelling to me over Neovim.

One big reason is that Emacs has a native GUI version. This means that variable font
sizes, svg rendering, and native inline image support works. This means I can have
a really nice markdown editing experience just as one example.

As a GUI native application I can have multiple windows on different monitors that 
share and pass buffers to each another.

Emacs is very scriptable without a predefined plugin API, which makes the
whole application introspective and trivially modifiable, not just in the ways that
the authors intended you to do. 

These capabilities would make it easy for
me to customize output of one window to interact with another (such as having
a flame graph be clickable right into my buffer with code, or highlighting code
in a buffer to make the relevant parts of a flame graph more prominent.

The built in `:term` terminal in Neovim feels extremely laggy and lackluster, at
least on my Mac OS work machine. There are many times I need a terminal and it would 
be nice to not have to go outside of my main editing environment
(especially at work where Mac OS doesn't have as flexible tiling as my Linux setup).

I also have not found a good way to manage multiple projects simultaneously in Neovim.
I need to have multiple projects with different workspace layouts open at the same time,
so that I can quickly switch between different projects (some of which may actually
be talking to each other through APIs). 

I have not found a good solution for that in native Neovim. I have gotten
close by using 1 tmux session per project. It's not a panacea though as it created two
layers of modality and complexity with window/pane management. 

None of these are insurmountable. I've thought many times that I should just work towards
perfecting my Neovim setup. However, Emacs keeps being there in the back of my mind and for
whatever reason I am keen to give it a fair try. 

If my Emacs journey is a failure then maybe I'll end up back in Neovim world anyway, but
at least I would have fully explored my options.

## Why Vanilla Emacs

A lot of recommendations seem to push new emacs users towards a distribution, such as
[Doom Emacs](https://github.com/doomemacs). These distributions are well thought out, but
they are opinionated by design. I was unsure when to use Doom's custom
configuration vs using standard elisp. I also did not feel the documentation was great for 
someone who was not fully familiar with emacs. 

I tried a much lighter configuration such as [Emacs-Kick](https://github.com/LionyxML/emacs-kick).
This was closer to what I wanted with a very well documented pure elisp configuration that 
(in theory) showed how to go from vanilla to customized. However there are a lot of customizations
that makes understanding what capabilities I even had (let alone how they worked) not trivial for me. 

It already had the kitchen sink activated and expected me to know how to operate it.

So I decided I wanted to start from first principals. What capabilities does Emacs have, can I explore
them one at a time, and how can I add packages as I understand the need for them.

And thus, this series was born.


