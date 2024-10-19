<!--
.. title: How to Learn Nix
.. slug: how-to-learn-nix
.. date: 2024-10-14 03:46:28 UTC+02:00
.. tags: engineering, os, nix
.. category: nix
.. link: 
.. description: Learning resources, basic terms and how to get started to learn Nix
.. type: text
-->


## What is Nix? 

The super short and simplified answer: A tool, so that under certain conditions you can be 100% assured that a package installed on my machine will also work on your machine. 

## The Long Answer... Let's Learn Nix First!

Before deep-diving into how to learn Nix, let's start with what the term `Nix` can actually refer to. 

Here are the most important terms around the Nix ecosystem: 
- **Nix** is a configuration language, the [[Nix language]]
- **Nix** is a package manager for strict sandboxing and system isolation
- **Nixpgs** is a package collection 
- **NixOS** is a Linux Distribution  (that uses the configuration and package manager)

Moreover, even more terms around the Nix ecosystem that you will stumble upon:

- ## Distinctions (explained [here]())
	- [flakes](https://nixos.wiki/wiki/Flakes) is an experimental feature for dependency management and powers most of the experimental nix CLI ("nix-command"). Any repository/directory with a `flake.nix` is a nix flake.
	- (command) [nix-shell](https://nixos.org/manual/nix/unstable/command-ref/new-cli/nix3-shell.html) is a command that works with flakes, which adds the specified packages to your environment (by updating `$PATH` and start a new shell)
	- [home-manager](https://github.com/nix-community/home-manager) is a tool to manage your home with modules. It is like NixOS but for your home. It doesn't really have much to do with flakes except that you can use it with flakes.
	- (command) `nix-env` is a way to manage packages in a traditional, imperative fashion. It's a tool to build the derivation and link the things to your profile and can be seen as an alternative to `home-manager`, `nix-shell` or `NixOS configuration`. It is recommended to switch to one of the others tools, see [2].

## Nix Learning Resources

An incomplete list (write me if any resource is missing):

* (official) https://nixos.org/learn/
* (official) https://nixos.org/guides/nix-pills/
* (inofficial) [NixOS & Flakes Book](https://github.com/ryan4yin/nixos-and-flakes-book) © 2023 by Ryan Yin 
* (inofficial) https://tonyfinn.com/blog/nix-from-first-principles-flake-edition/
* (inofficial?) https://github.com/spacekookie/learning-nix

One small piece to be aware of: 
In the official documentation, a Tutorial contains learning resources whereas a Guide contains only steps to follow [3].

## Sources

[[1](https://www.reddit.com/r/NixOS/comments/14cxklk/i_have_a_few_beginner_question_what_is_the/)] Distinctions between flakes / nix shell / home-manager
[[2](https://stop-using-nix-env.privatevoid.net/)] Stop using nix-env and alternatives 
[[3](https://nix.dev/contributing/documentation/diataxis#guides-vs-tutorials)] Guides vs. Tutorial in Nix.dev Documentation