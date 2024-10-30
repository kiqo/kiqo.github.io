<!--
.. title: Nix Cheatsheet & Gotchas
.. slug: nix-cheatsheet-and-gotchas
.. date: 2024-10-20 03:46:28 UTC+02:00
.. tags: engineering, os, nix
.. category: nix
.. link: 
.. description: Nix Cheatsheet & Gotchas
.. type: text
-->

## Nix Gotchas

* Any changes to `*.nix` files need to be git-commited by default, else they won't be picked up by `nix`

## New Nix Cli vs. Old Cli [1]

Remark:  
The new cli usually starts with `nix command` instead of `nix-command`. 

Main commands:
* `nix build [--print-out-paths]` - build a derivation or fetch a store path
* `nix develop` - run a bash shell that provides the build environment of a derivation
* `nix flake` - manage Nix flakes
* `nix profile` - manage Nix profiles
* `nix run` - run a Nix application
* `nix search` - search for packages
* `nix repl` - start an interactive environment for evaluating Nix expressions
* `nix fmt` - for running an auto-format on all `*.nix` files

Infrequent commands:
* `nix path-info` - query information about Nix store paths (for installable or derivations)
* `nix eval` - evaluate a Nix expression

## Nix Language

* `if ... then ... else` syntax
* Check for name `x` in attribute set `as = { a: 1 , x: 2}` is `as ? "x"` and accessing the value `2` is `as.x`. If the variable `y = "x"` exists, then this corresponds to `as ? ${y}` and `as.${y}`

## Sources

* [1](https://nix.dev/manual/nix/2.24/command-ref/new-cli/nix.html) New Nix Cli vs. Old Cli