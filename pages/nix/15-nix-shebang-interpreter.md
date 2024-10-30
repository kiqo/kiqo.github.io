<!--
.. title: Tutorial: Nix Shebang Interpreter
.. slug: nix-shebang-interpreter
.. date: 2024-10-23 03:46:28 UTC+02:00
.. tags: engineering, os, nix
.. category: nix
.. link: 
.. description: The Nix Shebang Interpreter
.. type: text
-->


In short, the Nix Shebang Interpreter can be used to run multiple Nix commands, interleaved with short program code (TODO???)

## Example with Flake

We want to run our `hello` program and provide it as an executable. 

Create a file `./myhello` with the following content: 
```
#!/usr/bin/env nix
#! nix shell --file ``<nixpkgs>`` hello --command bash
hello
```

Then run:
```bash
chmod a+x ./myhello
./myhello
# Hello, world!
```

This creates a bash shell which has the `hello/bin`-store path in its `$PATH` so that the `hello` binary is available: 

```bash
nix shell --file "<nixpkgs>" hello --command bash
echo $PATH
# /nix/store/rvb0s7vzinanr88calnlibrvgsgly63b-hello-2.12.1/bin:...
```
Note that in the above file double ticks `` are used instead of "", this is for specifying verbatim strings. 
Also be aware that **sequences of n backticks of 3 or longer are parsed as n-1 literal backticks.** 
A single space befornix shell nixpkgs#rustce the closing `` is ignored if present.

Afterwards, the `hello` program is run, so that `Hello, world!` is the output.


## Example with Expression

We want to provide the `terraform` command.

Create a file `./myterraform` with the following contents: 

TODO: check if this works
```bash
#! /usr/bin/env nix
#! nix shell --impure --expr ``
#! nix with (import (builtins.getFlake "nixpkgs") {});
#! nix terraform.withPlugins (plugins: [ plugins.openstack ])
#! nix ``
#! nix --command bash

terraform "$@"
```

Then run: 
```bash
chmod a+x ./myterraform
```

## Example with cascading interperters

TODO.

## Sources

* [[1](https://nix.dev/manual/nix/2.24/command-ref/new-cli/nix#shebang-interpreter)] Nix General Documentation
