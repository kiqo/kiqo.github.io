<!--
.. title: Tutorial: Nix Installables
.. slug: nix-installables
.. date: 2024-10-20 03:46:28 UTC+02:00
.. tags: engineering, os, nix
.. category: nix
.. link: 
.. description: Introduction of Nix Installables
.. type: text
-->

In short, Nix Installables are on what most `nix` cli commands operate on and represent something that can be stored in the Nix store [1]. 

### Installables

The following types of **installables** are supported by most commands:
* Flake output attribute (experimental) (default)
* Store path
   * This is assumed if the argument is a Nix store path or a symlink to a Nix store path
* Nix file, optionally qualified by an attribute path
   * Specified with `--file/-f`
* Nix expression, optionally qualified by an attribute path
   * Specified with `--expr/-E`

For most commands, if no installable is specified, `.` is assumed. That is, Nix will operate on the default flake output attribute of the flake in the current directory.

#### Flake Output Attribute

Note: TODO - unclear: why is it here with #? 
**Flake references** when used via the `nix`-cli are of the form `flakeref[#attrpath]` where `flakeref` is one of the above flake references and `attrpath` is an optional attribute path.

For example, the following would select the `nixpkgs` flake, and print the output path. 
```bash
nix build --print-out-paths 'nixpkgs#hello'
# /nix/store/8fpvkfwr8fm91xlzznsgh3g1fcw0hfnh-hello-2.12.1
```

#### Store path

These are paths inside the Nix store, or symlinks that resolve to a path in the Nix store.
Examples:
```bash
/nix/store/v5sv61sszx301i0x6xysaqzla09nksnd-hello-2.10
/nix/store/p7gp6lxdg32h4ka1q398wd9r2zkbbz2v-hello-2.10.drv  # store derivation
/nix/store/p7gp6lxdg32h4ka1q398wd9r2zkbbz2v-hello-2.10.drv^out # refer to the `out` output path of the derivation
/nix/store/p7gp6lxdg32h4ka1q398wd9r2zkbbz2v-hello-2.10.drv^* # all outputs can be referred to at once with the special syntax ^*.
```

Example:
```bash
ls /nix/store/*hello*.drv # find a path to a derivation
nix derivation show '/nix/store/161hd6k485dvr9cx02ccnc1x4n9fzix6-hello-2.12.1.drv^*'
```

#### Nix file

With the option: `-f / --file path [attrpath...]`, installables are interpreted as the value of the expression in the Nix file at `path`.
If attribute paths are provided, commands will operate on the corresponding values accessible at these paths. 

The Nix expression in that file, or any selected attribute, must evaluate to a **derivatio** (in the next guide, we will learn what derivations are in more details).

Example: TODO: `--file /path/to/nixpkgs hello`
TODO: test if this works
```bash
nix build --file ./flake.nix
```

#### Nix expression

With the option: `--expr expression [attrpath...]`, installables are interpreted as the value of the of the Nix expression. 
Same as for Nix files: If attribute paths are provided, commands will operate on the corresponding attribute paths and the Nix expression, or any selected attribute, must evaluate to a derivation.

Example: 
```bash
nix build --print-out-paths --impure --expr 'import <nixpkgs> {}' hello # or
nix run --impure --expr 'import <nixpkgs> {}' hello
```
You may need to specify `--impure` if the expression references impure inputs (such as <nixpkgs>) - see more about impurity here (TODO).

#### Extra: Default outputs

If no desired output are specified, but the derivation has an attribute `meta.outputsToInstall`, Nix will use those outputs. 

For example, since the package `nixpkgs#libxml2` has this attribute:
```bash
nix eval 'nixpkgs#libxml2.meta.outputsToInstall'
# [ "bin" "man" ]
```
a command like `nix shell nixpkgs#libxml2` will provide only those two outputs by default.

Otherwise, Nix will use all outputs of the derivation.

## Sources

* [[1](https://nix.dev/manual/nix/2.24/command-ref/new-cli/nix)] Nix experimental high-level documentation
