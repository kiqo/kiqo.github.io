<!--
.. title: Tutorial: Nix Derivations Builder
.. slug: nix-derivations-builder
.. date: 2024-10-23 03:46:28 UTC+02:00
.. tags: engineering, os, nix
.. category: nix
.. link: 
.. description: How the Derivations Builder Works
.. type: text
-->


In short, TODO a Nix derivation builder

## Details about `derivation` [1]

There are more [Advanced Attributes](https://nix.dev/manual/nix/2.24/language/advanced-attributes) that can be passed to the `derivation` function. 

**Every other attribute is passed as an environment variable to the builder.** 
Attribute values are translated to environment variables as follows:

* Strings are passed unchanged.
* Integral numbers are converted to decimal notation.
* Floating point numbers are converted to simple decimal or scientific notation with a preset precision.
* A path (e.g., `../foo/sources.tar`) causes the referenced file to be copied to the store; its location in the store is put in the environment variable. The idea is that all sources should reside in the Nix store, since all inputs to a derivation should reside in the Nix store.
* A derivation causes that derivation to be built prior to the present derivation. The environment variable is set to the store path of the derivation's default output.
* Lists of the previous types are also allowed. They are simply concatenated, separated by spaces.
* `true` is passed as the string `1`, `false` and `null` are passed as an empty string.


## Sources

[[1](https://nix.dev/manual/nix/2.24/language/derivations#builder-execution)] Nix derivations builder execution documentation