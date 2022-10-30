<image width="100px" src="https://raw.githubusercontent.com/sminez/penrose/develop/icon.svg" align="left"></image>
penrose - a tiling window manager library
=========================================

[![Build](https://github.com/sminez/penrose/workflows/Build/badge.svg?branch=develop)](https://github.com/sminez/penrose/actions?query=workflow%3ABuild) [![crates.io version](https://img.shields.io/crates/v/penrose)](https://crates.io/crates/penrose) [![docs.rs](https://img.shields.io/docsrs/penrose?logo=rust)](https://docs.rs/penrose) [![Book Build](https://github.com/sminez/penrose/actions/workflows/book.yml/badge.svg)](https://github.com/sminez/penrose/actions/workflows/book.yml)

### Read the book

The docs for penrose are published to GitHub Pages [here](https://sminez.github.io/penrose).


# :warning: The `rewrite` branch has been merged to the `develop` :warning:
**Users pulling this repo directly from the mainline branch will see this as a breaking change**

The original API of penrose was written fairly quickly(!) and has a number of issues that make future development difficult:
- Basing the original design on the `dwm` code base lead to a very C like API
- State management is overly complicated
- Hooks are overly complicated
- Testing core window manager operations is a lot harder than it should be
- Far too many macros (I wrote penrose originally in part to learn how to write proc-macros...)
- Default impls used the rust-XCB crate rather than the wonderful x11rb from @psychon

A near total rewrite for `v0.3` is reaching a usable state which moves over to an API that is more suited for ongoing
development and extension by users of the crate. In particular the APIs are now designed with composition in mind as
a rich set of primatives and a clear split between "pure" logical operations on the internal state and code that then
updates things on the X side based on diffs of that pure state.

# What does this mean for me?

## I like things the way they are!
:label: Please pin your usage of penrose to the [v0.2-end-of-life](https://github.com/sminez/penrose/tree/v0.2-end-of-life)
tag if you are intending on making use of the original penrose API but be aware that there will be no more development
taking place on this version of the code.

## How do I start making use of the new APIs?
The `rewrite` branch is being merged to mainline following the creation of this EOL release and is now in a usable
state. I've been using it as my daily driver for a few weeks now and it is relatively stable to live in so long as
you are only making use of the top level APIs shown in the examples directory.

:bug: That said, there are bugs. There are some issues around tracking mouse based focus changes in a multi-monitor
set up and I suspect that it will be easier than it should be to shoot yourself in the foot if you try something
clever beyond what is shown in the current examples.

I am planning on writing up the docs and more examples for the API now that it is stablising but for now please be
aware that I am making frequent changes to APIs in order to address issues as they crop up. If you are interested
in taking things for a spin and providing feedback then please do! Just be aware that I am not yet aiming for
stability.

## When will the new APIs be stable?
There are a couple of final high profile bugs / behaviours that I want to correct before pushing this live to
crates.io. Once those are addressed I'll be publishing a `0.3.0` version to crates which will mark the new
stable API.


------


# `Penrose` is a modular library for configuring your own X11 window manager in Rust.

This means that, unlike most other tiling window managers, `Penrose` is not a
binary that you install on your system. Instead, you use it like a normal
dependency in your own crate for writing your own window manager. Don't worry,
the top level API is well documented and a lot of things will work out of the
box, and if you fancy digging deeper you'll find lots of opportunities to
customise things to your liking.

![screenshot](https://raw.githubusercontent.com/sminez/penrose/develop/screenshot.png)

<br>


### Project Goals

#### Understandable code

`Penrose` was born out of my failed attempts to refactor the [dwm][6] codebase into
something that I could more easily understand and hack on. While I very much
admire and aim for minimalism in code, it becomes a problem when your complex
code base starts playing code golf to keep things under an arbitrary line limit.

I won't claim that `Penrose` has the cleanest code base you've ever seen, but it
_should_ be readible in addition to being fast. If something is confusing or
unclear, then I count that as a bug (and please raise it as such!)


#### Simple to configure

I've also tried my hand at [Xmonad][7] in the past. I love the set-ups people can
achive with it ([this one][8] is a personal favourite) but doing everything in
Haskell was a deal breaker for me. I'm sure many people will say the same thing
about Rust but then at least I'm giving you some more options!

With `Penrose`, a simple config can be written in about 5 minutes and roughly 50
lines of code. It will be pretty minimal but each additional feature (such as a
status bar, scratch-pads, custom layouts, dynamic menus...) can be added in as
little as a single line. If the functionality you want isn't available however
then that leads us on to...


#### Easy to extend

[dwm][6] patches, [qtile][9] lazy APIs, [i3][10] IPC configuration; all of these
definitely work but they are not what I'm after. Again, the [Xmonad][7] model of
companion libraries that you bring in and use as part of writing your own window
manager has always felt like the right model for me for extending the window
manager. (Though, again, while Haskell is great fun for tinkering I've never
felt productive in it)

`Penrose` provides a set of Rust traits for defining the various ways you can
interact with the main `WindowManager` struct. You are free to write your own
implementations, write code that manipulates them and extend them however you
see fit. If you want to check out some examples of what is possible, take a look
in the [contrib][11] directory.

Want to run some particular logic every time you connect external monitors?
Write a [hook][12] that listens for randr triggers.

Want to scatter your windows at random over the screen? Write a custom
[layout][13] and make use of all of the helper methods on [regions][14].

Have an idea that you can't currently implement? Raise an issue and suggest an
extension to the API!

<br>

### Project Non-goals

#### An external config file

Parsing a config file and dynamically switching behaviour on the contents adds a
huge amount of complexity to the code: not to mention the need for _validating_
the config file! By default, `Penrose` is configured statically in your
**main.rs** and compiled each time you want to make changes (similar to
[Xmonad][7] and [dwm][6]).

That said, the extensibility of `Penrose` means that
you are free to define your own config file format and parse that as part of
your startup, if that is something you want. You could read from `xresources`,
or a stand alone file of your design.

The choice is yours!


#### IPC / relying on external programs

There are several places where `Penrose` makes use of external programs for
utility functionality (reading the user's key-map or spawning a program launcher
for example), but core window manager functionality is always going to stay
internal.

As you might expect, you can definitely write your own extensions that provide
some sort of IPC or client/server style mechanism if you want to mirror the
kinds of things possible in other window managers such as `i3` or `bspwm`, but
that is not going to be included at the expense of statically defined control in
your binary as a default.


  [0]: https://github.com/sminez/penrose/tree/develop/docs/faq.md
  [1]: https://github.com/sminez/penrose/tree/develop/docs/getting_started.md
  [2]: https://github.com/sminez/penrose/tree/develop/examples
  [3]: https://github.com/sminez/my-penrose-config
  [4]: https://docs.rs/penrose
  [5]: https://www.youtube.com/channel/UC04N-5DxEWH4ioK0bvZmF_Q
  [6]: https://dwm.suckless.org/
  [7]: https://xmonad.org/
  [8]: https://www.youtube.com/watch?v=70IxjLEmomg
  [9]: http://www.qtile.org/
  [10]: https://i3wm.org/
  [11]: https://github.com/sminez/penrose/tree/develop/src/contrib
  [12]: https://docs.rs/penrose/0.2.0/penrose/core/hooks/index.html
  [13]: https://docs.rs/penrose/0.2.0/penrose/core/layout/index.html
  [14]: https://docs.rs/penrose/0.2.0/penrose/core/data_types/struct.Region.html
