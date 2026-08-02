# Keybinds, Chords, and Modality

As someone who has always installed Vim emulation in every editor I have tried to use
(and refuse to use ones that don't support it)  it's time to tackle the elephant in the
room: should I stick with Emacs bindings and non-modal editing, set up a Vim emulation layer.

## Initial Opinion Of Emacs Defaults

Everyone has strong opinions about keybindings and text editing styles. That is even less
surprising in the Vim and Emacs world as a reason for adoption is customizing each to
their own likings. 

Everyone will claim that their preferences are the most productive way ever and be confused
when other people just don't get it. 

The reality is that almost all editors default to the way Emacs does editing. It is a (mostly) non-modal
editor, which means it only has one editing mode where each characer press equals an character
being inserted into the text area. If you wnat extra functionality you have to use a hotkey,
usually involving a modifier key.

As much as I have loved Vi's modal editing system from the the last decade I have used it, the 
reality of the situation is that I have had several decades prior that I was fine with non-modal editing in my
text editors and IDEs. 

I have spent the last few weeks tinkering and learning Emacs, trying to learn the Emacs
way, This way if I end up using a VI emulation layer than it's not just because I am afraid of change. At
some point I had to dive in and learn how to get good with Vi bindings in the same way.

My conclusion is a bit nuanced. 

I actually don't mind the default Emacs experience, except for all the chording (having to constantly
hold control, meta, and/or shift to perform an action). For the most part I can see the logic around
the categories of the default keybinds.

I did change my Linux and Mac configurations to have Caps Lock mapped to control and that certainly helped
but didn't totally solve the issue. Holding my pinky off to the side and using my middle finger down to 
hit `C-x` is just not natural for me. Then trying to do `C-x C-s` to save my current buffer requires significant thinking. 

There are also a lot of key bindings in Emacs, and that means you can't have every action be a single `C-` action.
Thus you end up with some significant chains where you have to keep thinking about whether the next character
requires a modifier key or not. For example, `C-x C-f` is completely different functionality than `C-x f`, and the
latter is easy to do accidentally by being on autopilot and removing your pinky too much.

I also just feel like `Ctrl` as a modifier is hard and awkward to hold with other keys on my keyboards. A
lot of advice seems to revolve around getting a better split ergonomic keyboard, and I can see getting one
could help. However I am not always at my desk and frequently use my laptops as a laptop, and in those cases
I do not have a say in what the keyboard layout is. Even with Caps Lock as Control, I end up accidentally
hitting shift at times instead of Control.

The other frustration I have with the Emacs defaults is the constant switching between Control and Meta.
For example, navigating forward by a two words then back by two characters involves `M-f M-f C-b C-b`.
The mental switching between when I need to hit Meta vs Control for quick navigating is something I am
finding difficult, especially since it's more ergonomic for me to do control via my left pinky but alt
via my right thumb. 

I'm sure I can works towards getting comfortable with these, but I also feel like I should consider
alternative options.

## What Options Are There?

From what I can tell there are 5 main options for me to get comfortable with Emacs

1. Use the VI emulation layer
2. Use Meow for modal editing
3. Use the God Mode package
4. Use OS sticky keys so I can use chorded hotkeys without holding them down
5. Just keep at vanilla experience until I get better at it

