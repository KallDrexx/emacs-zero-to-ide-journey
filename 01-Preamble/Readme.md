# Preamble

This post reflects on the personal motivations for going on this Emacs journey, and why
this document even exists. It's mostly designed around giving some optional context to
the framing of the articles that will follow. 

As a word of warning it may be kind of rambly, more-so than most of the other posts
in this series.

## Why DE-IDE

I have been programming for over 30-ish years now, first as a hobby and then later as a career. 
While I don't remember all the tooling I used when I was young, most of that time has been inside
of graphical IDEs, such as JetBrains' collection and Visual Studio. 

Even during my high school days when I was using Linux, I was using graphical IDEs whose names
I can no longer remember (maybe they no longer exist).

I am a proficient VIM user and have used VIM keyboard emulation for such a long time. 
However I have never actually done much coding in VIM itself. Some of the blame of that
probably relies on my early career doing a lot of .NET development (even with all my
Linux experience). 

I have always just felt that the slowness of graphical IDEs was worth it to me 
because of the extra tooling they provided me. Things like 
* smart autocomplete
* go to definition that's context aware unlike grep
* go to implementations
* seamless integration with decompilers
* easy to use debuggers and profiling tools
* all type of refactoring tools
* etc...

I personally got a lot of value from these tools.

The advent of Language Server Protocols (and for better or worse, VS Code) changed the
calculus of and power of tools like Vim, Emacs, Sublime Text, and other to not require
each editor to have it's own bespoke tooling. 

Yet I am a creature of habit, and already (happily) paid a subscription to JetBrains
to be able to use their tools.

However, the burden of JetBrains' IDEs are starting to become heavy. 

At my day job we have large code bases in Typescript for node applications, C#, Go all in
different repositories. This has me jumping between multiple instances of Rider and Webstorm,
as well as Datagrip for database management. I will probably need their Go IDE soon for some
Go code that seems to be expanding. For personal projects I'm jumping between Rust, C, C++, 
C#, and Verilog. 

Some of these tools can be combined with limitations, but some can't. JetBrains in 
particular seems to be defaulting to advertising a lot of AI integrations
that I do not want (if I want to use LLMs I'll load up a terminal with a proper agent UI).
Every time I install I have to re-fix settings that don't sync properly. It all just
starting to be a pain to deal with.

The final straw was a FPGA project that requires Verilog and C++ simulation. The long,
not-so interesting detail is that CLion forces a project view that's cmake dependent, making
it frustratingly hard to get Verilog entries to show up in the project view. Likewise, all
the Verilog plugins are expensive or not great, and it has no LSP integrations for me to use
the quality open-source projects that are available.

So I finally decided that JetBrains IDEs are no longer for me.

If I was going to spend the time to de-IDE myself, I wanted something that was flexible
enough for me to truly customize to my liking and workflows. 

While VS Code and Zed are alright, if I'm going through the trouble of upending my workflows
and going deep into customizations I might as well go for the truly open-source options and
give them a fair shake. 

So Neovim and Emacs became my main targets. These two options seemed like they had 
the best potential for me to make my own personal IDE.

## Why Not Neovim?

I started with Neovim due to my familiarity with VIM keybindings. I started with
[Duy NG's amazing blog](https://tduyng.com/blog/neovim-basic-setup/) and ended up
getting a completely custom Neovim setup in no time that has a good chunk of what
I need for day to day development. 

It has full LSP support, Overseer for Makefile/task execution, lazygit for a good
git UI right inside, good file tree and file search/grep search interfaces, etc...
It doesn't have everything I need yet, but I got this up and running in a few days
that takes care of a big chunk of what I need day to day.

If I never started looking at Emacs I probably would have no qualms sticking with
Neovim. Now that I've looked at Emacs though, I can't turn off my tinkerer mind 
and need to at least give it a real chance. There are a few  reasons why Emacs is 
theoretically compelling to me over Neovim.

The first is that Emacs is a native GUI application. This has several advantages 
(again, in theory) that Neovim doesn't have. 

Variable size fonts means I can actually have a Markdown editor that has 
different visible sizes for different headers. 

GUI displays mean I can have images displayed with text. It also means I can  display 
a profiling flame-graph in section of my IDE. 

Emacs extreme scriptability and introspection capabilities would make it easy for
me to customize output of one window to interact with another (such as having
a flame graph be clickable right into my buffer with code, or highlighting code
in a buffer to make the relevant parts of a flame graph more prominent.

The scriptability also seems like it will make it easier to customize functionality
that plugins add without modifying the plugins themselves.

The built in `:term` terminal in Neovim feels extremely laggy and lackluster, at
least on my Mac OS work machine. There are many times I need a terminal and it would 
be nice to not have to go outside of my main editing environment
(especially at work where Mac OS doesn't have as flexible tiling as my Linux setup).
I have also had the 

I also have not found a good way to manage multiple projects simultaneously in Neovim.
I need to have multiple projects with different workspace layouts open at the same time,
so that I can quickly switch between different projects (some of which may actually
be talking to each other through APIs). Each project may have different windowing
concerns, and their buffers would ideally be completely independent of each other
(so I can't open a buffer for one project in the scope of another).

I have not found a good solution for the latter two in native Neovim. I have gotten
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
they are opinionated by design. I was unsure how to customize it against Doom's custom
configuration vs using standard elisp. I also did not feel the documentation was great for 
someone who was not fully familiar with emacs. 

I tried a much lighter configuration such as [Emacs-Kick](https://github.com/LionyxML/emacs-kick).
This was closer to what I wanted with a very well documented pure elisp configuration that 
in theory showed how to go from vanilla to customized. However, there are a lot of customizations
it makes and understanding what capabilities I even had (let alone how they worked) wasn't easy for me. It
already had the kitchen sink activated.

So I decided I wanted to start from first principals. What capabilities does Emacs have, can I explore
them one at a time, and how can I add packages as I understand the need for them.

And thus, this series was born.


