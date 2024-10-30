<!--
.. title: Tutorial: Nix Derivations
.. slug: nix-derivations
.. date: 2024-10-20 03:46:28 UTC+02:00
.. tags: engineering, os, nix
.. category: nix
.. link: 
.. description: Introduction of Nix Derivations
.. type: text
-->


In short, a Nix derivation is a **description of a build task** and are used internally by Nix. 
It takes as input an attribute set, the attributes of which specify the inputs to the process. It outputs an attribute set, and produces a (Nix) **store derivation** as a side effect of evaluation [1].

A **store derivation** is a **derivation** representated as a `.drv` file in the store and is the **instantiated** form of a derivation.
This means: With the execution of the `derivation` primitive the output is stored as stored derivation in the Nix store. 
Example: `/nix/store/g946hcz4c8mdvq2g8vxx42z51qb71rvp-git-2.38.1.drv`

## Show a derivation

Let's show the `nixpkgs#hello` derivation:

```bash
nix derivation show nixpkgs#hello
```

which outputs something similar to:

```json
{
  "/nix/store/z0x3vgz97j1az7c13nn1jbpx86qs94b7-hello-2.12.1.drv": {
    "args": [
      "-e",
      "/nix/store/v6x3cs394jgqfbi0a42pam708flxaphh-default-builder.sh"
    ],
    "builder": "/nix/store/0w0hhy7rmn5xisasdr4v4p958x114sj1-bash-5.2p32/bin/bash",
    "env": {
      ...
      "name": "hello-2.12.1",
      "out": "/nix/store/8fpvkfwr8fm91xlzznsgh3g1fcw0hfnh-hello-2.12.1",
      "outputs": "out",
      ...
      "pname": "hello",
      ...
      "src": "/nix/store/pa10z4ngm0g83kx9mssrqzz30s84vq7k-hello-2.12.1.tar.gz",
      "system": "aarch64-darwin",
      "version": "2.12.1"
    },
    "inputDrvs": {
      "/nix/store/57gnk3gw83xzvf5m2hb9n6q62rxqmv3m-hello-2.12.1.tar.gz.drv": {
        "dynamicOutputs": {},
        "outputs": [
          "out"
        ]
      },
      ...
    },
    "inputSrcs": [
      "/nix/store/v6x3cs394jgqfbi0a42pam708flxaphh-default-builder.sh"
    ],
    "name": "hello-2.12.1",
    "outputs": {
      "out": {
        "path": "/nix/store/8fpvkfwr8fm91xlzznsgh3g1fcw0hfnh-hello-2.12.1"
      }
    },
    "system": "aarch64-darwin"
  }
}
```

Note that the first line `/nix/store/z0x3vgz97j1az7c13nn1jbpx86qs94b7-hello-2.12.1.drv` is the location of the stored derivation in the Nix store. 

### JSON serialization of derivation 

The JSON serialization of a derivations is a JSON object with the following fields [2]:

* `name`: The name of the derivation. This is used when calculating the store paths of the derivation's outputs.
* `outputs`: : Information about the output paths of the derivation. This is a JSON object with one member per output, where the key is the output name and the value is a JSON object with the fields `path`, `method`, `hashAlgo` and `hash`
  * Example:
```bash
"outputs": {
  "out": {
    "path": "/nix/store/2543j7c6jn75blc3drf4g5vhb1rhdq29-source",
    "method": "nar",
    "hashAlgo": "sha256",
    "hash": "6fc80dcc62179dbc12fc0b5881275898f93444833d21b89dfe5f7fbcbb1d0d62"
  }
}
```
* `inputSrcs`: A list of store paths on which this derivation depends.
* `inputDrvs`: A JSON object specifying the derivations on which this derivation depends, and what outputs of those derivations.
* `system`: The system type on which this derivation is to be built (e.g. x86_64-linux).
* `builder`: The absolute path of the program to be executed to run the build. Typically this is the bash shell (e.g. `/nix/store/r3j288vpmczbl500w6zz89gyfa4nr0b1-bash-4.4-p23/bin/bash`).
* `args`: The command-line arguments passed to the builder.
* `env`: The environment passed to the builder.

## Derivation output selection

Derivations can have multiple outputs, where each output has a different store path. 
For instance, a package can have a bin output that contains programs, and a dev output that provides development artifacts like C/C++ header files.

The outputs on which nix commands operate are determined via `installable^output1,...,outputN` or `installable^*` for specifying all outputs.

Examples:
```bash
nix build '/nix/store/gzaflydcr6sb3567hap9q6srzx8ggdgg-glibc-2.33-78.drv^dev,static'
nix build --impure -E 'import <nixpkgs> { }' 'glibc^dev,static'
```


## Debug a derivation

For this, we use `nix develop` for our previously generated `hello` flake, which runs a bash shell that provides the build environment of a derivation:

```bash
nix develop
```

Here, we can now run start hacking on our flake. 
Run the following commands and notice that the `hello` binary is produced as part of the last step: 
```bash
 unpackPhase
 cd hello-*
 configurePhase
 buildPhase
 ./hello
# Hello, world!
 installPhase
 ../outputs/out/bin/hello
# Hello, world!
```

## Create your own derivation

**Note: The following are details about Nix derivations for understanding how derivations work. 
Normally the `derivation` function should not be used directly, but instead a higher-level function like `stdenv.mkDerivation` TODO LINK AND QUOTE is used for defining derivations.**

Now begins the fun! :) 

Let's start with a very simple derivation, which just creates a file with the content "Nix is Fun!". 

For this, we make use of the built-in Nix function `derivation` (which you might notice has similar has mandatory fields as in the JSON serialization ;)). 

We start simple by creating just a bash shell derivation: 

``` { .bash }
nix repl # Drop into nix-repl
nix-repl> derivation {name = "nixisfun"; builder="/bin/bash"; system=builtins.currentSystem; }
# «derivation /nix/store/x0pz98jwh6lrj9xrvy53syhis1hx84f7-nixisfun.drv»
```
Note that this created our first derivation at the path `<Nix store path>/<hash>-<name of derivation>.drv`: 

In a new shell run: 
```bash
cat /nix/store/x0pz98jwh6lrj9xrvy53syhis1hx84f7-nixisfun.drv
# Derive([("out","/nix/store/py73ih6j9x1v2xnzmqaa592fxkzpki4s-nixisfun","","")],[],[],"aarch64-darwin","/bin/bash",[],[("builder","/bin/bash"),("name","nixisfun"),("out","/nix/store/py73ih6j9x1v2xnzmqaa592fxkzpki4s-nixisfun"),("system","aarch64-darwin")])%
```
Note that the `out` path doesn't exist yet. 
For this we need to build (aka instantiate) it first:
```bash 
nix build --verbose '/nix/store/x0pz98jwh6lrj9xrvy53syhis1hx84f7-nixisfun.drv^*'
```

However this fails by default with an error:
```bash
error: builder for '/nix/store/x0pz98jwh6lrj9xrvy53syhis1hx84f7-nixisfun.drv' failed to produce output path for output 'out' at '/nix/store/py73ih6j9x1v2xnzmqaa592fxkzpki4s-nixisfun'
```

Why? By default, if no `outputs` argument is provided to `derivation` function, the `outputs` is set to `[ "out" ]`.
Each output name is passed to the builder executable as an environment variable with its value set to the corresponding store path.
This means: The builder is supposed to write output to the path by reading the environment variable `$out`. 

For example, if you specify:
```bash
derivation {
  name = "example";
  outputs = [ "lib" "dev" "doc" "out" ];
  # ...
}
```

The store derivation path will be `/nix/store/<hash>-example.drv`. The output paths will be

```
/nix/store/<hash>-example-lib
/nix/store/<hash>-example-dev
/nix/store/<hash>-example-doc
/nix/store/<hash>-example  # for `out` there is no suffix appended
```

You can then refer to each output of a derivation by selecting it as an attribute. The first element of outputs determines the **default output** and ends up at the top-level.

Example: For selecting an output by attribute name:
```nix
let
  myPackage = derivation {
    name = "example";
    outputs = [ "lib" "dev" "doc" "out" ];
    # ...
  };
in myPackage.dev
```
Since `lib` is the first output, `myPackage` is equivalent to `myPackage.lib`.

Now returning to the example, let's write a file to `$out`: 

```bash
nix repl # Drop into nix-repl
nix-repl> derivation {name = "nixisfun"; builder="/bin/bash"; system=builtins.currentSystem; args=[ "-c" "echo Nix is Fun! > $out" ]
# «derivation /nix/store/vwc2d8vkyaj92z2wgars6c2qa3bs8hfs-nixisfun.drv»
```

We now build the derivation again: 
```bash
nix build --debug --print-out-paths /nix/store/vwc2d8vkyaj92z2wgars6c2qa3bs8hfs-nixisfun.drv^out
# outputs debugs logs and as last line the path to the nix store, i.e. /nix/store/3c5v7slddq2lpydlkzhc83ngwqnhwl4s-nixisfun
```
and check that the `out` path was created successfully:
```bash
cat /nix/store/3c5v7slddq2lpydlkzhc83ngwqnhwl4s-nixisfun
# Nix is Fun!
```

Horray, we created our first working derivation! :) 

## Extra: Create a flake with this derivation

```bash
nix flake new nixisfun
```

We then edit the created file located at `./nix-tutorial/nixisfun/flake.nix`:
```nix
{
  description = "A Flake which creates a Nix is Fun! output file";

  inputs = {
    nixpkgs.url = "github:nixos/nixpkgs?ref=nixos-unstable";
  };

  outputs = { self, nixpkgs }: {
    packages.aarch64-darwin.default = 
    derivation { name = "nixisfun"; builder="/bin/bash"; system="aarch64-darwin"; args=[ "-c" "echo Nix is Fun! > $out" ]; };

    # TODO: figure out a way to not duplicate this ;)
    packages.x86_64-linux.default = 
    derivation { name = "nixisfun"; builder="/bin/bash"; system="x86_64-linux"; args=[ "-c" "echo Nix is Fun! > $out" ]; };
  };
}
```
Note: In this case we actually can remove the `inputs` section as we are not using `nixpkgs`. 

We then can git commit and build the flake: 
```
git add .
git commit -m "Create nixisfun flake"
nix build --print-out-paths
# prints /nix/store/3c5v7slddq2lpydlkzhc83ngwqnhwl4s-nixisfun
cat /nix/store/3c5v7slddq2lpydlkzhc83ngwqnhwl4s-nixisfun
# Nix is Fun!
```

Note that `nix run` would however fail as we do not create a `./bin/nixisfun` output. Feel free to add that to your flake!

## Sources

* [[1](https://nix.dev/manual/nix/2.24/language/derivations)] Nix derivations documentation
* [[2]](https://nix.dev/manual/nix/2.24/command-ref/new-cli/nix3-derivation-add) Nix Derivations Add Cli