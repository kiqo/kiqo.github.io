<!--
.. title: Tutorial: Nix Flakes
.. slug: nix-flakes
.. date: 2024-10-20 03:46:28 UTC+02:00
.. tags: engineering, os, nix
.. category: nix
.. link: 
.. description: Introduction of Nix Flakes
.. type: text
-->


In short, Nix Flakes are a way to create a an output package. 
They are the unit for packaging Nix code in a reproducible and discoverable way by specifying dependencies as inputs.  

## Pre-requisite

[Nix Installation](https://nix.dev/install-nix#install-nix)

## Get Started

Technically, a **Nix Flake** is a file system tree that contains a file named `flake.nix` in its root directory.

For creating the most basic flake run: 
```bash
mkdir nix-tutorial && cd nix-tutorial/ && git init
nix flake init
```

which will create the `flake.nix` file similar to the following:

```nix
{
  description = "A very basic flake";

  inputs = {
    nixpkgs.url = "github:nixos/nixpkgs?ref=nixos-unstable";
  };

  outputs = { self, nixpkgs }: {

    packages.x86_64-linux.hello = nixpkgs.legacyPackages.x86_64-linux.hello;

    packages.x86_64-linux.default = self.packages.x86_64-linux.hello;

  };
}
```

Note that this is simply an `attrSet`, which is required to follow a certain format and specifies the **location of the Flake** [1].

## Inputs of the Flake

The inputs of this file hereby reference other **flakes**. These are so called **flake references**. 

They can be specified in the following two ways:

1. Attribute set representation OR
```
{
  type = "github";
  owner = "NixOS";
  repo = "nixpkgs";
}
```
1. URL-like Syntax OR
```
inputs.nixpkgs.url = "github:NixOS/nixpkgs";
```

Other possible Examples for URL-like Syntax:
```
nixpkgs/a3a3dda3bacf61e8a39258a0ed9c924eeca8e293 # The nixpkgs entry in the flake registry, with its Git revision overridden to a specific value.
github:NixOS/nixpkgs/a3a3dda3bacf61e8a39258a0ed9c924eeca8e293: A specific revision of the nixpkgs repository.
git+https://github.com/NixOS/patchelf?ref=master&rev=f34751b88bd07d7f44f5cd3200fb4122bf916c7e # A specific branch and revision of a Git repository. 
```
1. Path-like Syntax
```
/absolute/path/to/the/flake # or 
./relative/path/to/the/flake
```

## Outputs of the Flake

The above `flake.nix` provides a single package (i.e. an installable **derivation**). 
**Derivation**'s are covered in more detail in the next section of the guide, but for now let's just think of them as an installable, i.e. something that can be installed.

For defining your own output, this might look like the following:

```
{
  description = "A flake for demonstrating outputs";

  # The `nixpkgs` input from a Github repository specified via attribute set representation
  inputs.nixpkgs.url = "github:NixOS/nixpkgs/nixos-20.03";

  # The `import-cargo` input from a Github repository specified via attribute set representation
  inputs.import-cargo = {
    type = "github";
    owner = "edolstra";
    repo = "import-cargo";
  };
  
  outputs = { self, nixpkgs }: {

    packages.x86_64-linux.default =
      # Notice the reference to nixpkgs here.
      with import nixpkgs { system = "x86_64-linux"; };
      stdenv.mkDerivation {
        name = "hello";
        src = self;
        buildPhase = "gcc -o hello ./hello.c";
        installPhase = "mkdir -p $out/bin; install -t $out/bin hello";
      };

  };
}
```

Notice here the reference to `nixpkgs`, which is defined as one of the inputs. 

Each input is fetched, evaluated and passed to the outputs function as a set of attributes with the same name as the corresponding input. The special input named `self` refers to the outputs and source tree of this flake. Thus, a typical outputs function looks like this:

```
outputs = { self, nixpkgs, cargo-import }: {
  ... outputs ...
};
```

## Build the Flake

We now "build" the Flake by running: 
```nix
nix build
```


Note: In case you are not on `x86_64-linux` platform, add a line like the following to the `outputs` section to avoid any errors:
```
packages.aarch64-darwin.default = nixpkgs.legacyPackages.aarch64-darwin.hello;
```

The "building" here means that 

1. A `flake.lock` file is created.
2. The `outputs` are created and copied to the Nix store

### The `flake.lock` file

This file is similar to a npm-`package-lock.json` corresponding to `package.json`. The `lock`-file is describing the "locked" set of dependencies, so that **any other user with this `flake.lock` file and running `nix build` will get the same outputs when run on the same platform**.

You can try that out by running the following: 
```
cd .. && mkdir nix-tutorial-dummy
cp nix-tutorial/flake* nix-tutorial-dummy/
cd nix-tutorial-dummy/ && nix build
```

### Create `outputs` in Nix store

The `outputs` are created in the `result` dir, which is a symlink to the **Nix store**. 
The Nix store is located under `/nix/store` and contains all packages that are ever produced (for example via `nix build` or downloaded as input).

The Nix Store functions as a cache: For example, if the corresponding version of `nixpkgs` is already in the Nix Store, it would not download it again, speeding up output generation processes. 

You can see that via: 

```bash
ls -lah
```
which shows something like:
```
lrwxr-xr-x@  1 myuser  staff    56B Oct 21 20:47 result -> /nix/store/8fpvkfwr8fm91xlzznsgh3g1fcw0hfnh-hello-2.12.1
```


You can now run `./result/bin/hello` or simply `nix run` to run your built Flake 🥳


## Summary

Flakes are...
* A way to create a (platform-specific) reproducible output which is stored in the Nix Store by running `nix build`. 
* A file system with a `flake.nix` file that is an `attrSet` that follows a certain structure
* Specifying dependencies in the `inputs` section similar and are functioning similar as to `package.json` files in other languages. This means a corresponding `lock`-file is created for reproducibility.


## Extra - Flake Details

* Repositories that don't contain a `flake.nix` can also be used as inputs, by setting `flake = false`: 

```nix
inputs.grcov = {
  type = "github";
  owner = "mozilla";
  repo = "grcov";
  flake = false;
};
```
* Without `inputs.nixpkgs` attribute the following default is assumed
```nix
# This is the default
# inputs.nixpkgs = {
#  type = "indirect";
#  id = "nixpkgs";
# };

# nixpkgs can here be used even if not specified as inputs
outputs = { self, nixpkgs }: {
  packages.x86_64-linux.grcov = stdenv.mkDerivation {
    ...
  };
};
```

* Transitive inputs can be overridden, i.e. to override `nixpkgs` from `nixops`:
```nix
inputs.nixops.inputs.nixpkgs = {
  type = "github";
  owner = "my-org";
  repo = "nixpkgs";
};
```

* It is possible to specify to use the same version of a package, as another package with the `follows` keyword:
```
inputs.nixpkgs.follows = "dwarffs/nixpkgs";
```


## Sources

* [[1](https://nix.dev/manual/nix/2.18/command-ref/new-cli/nix3-flake.html#flake-format)] `nix flake` command
* [[2](https://nix.dev/manual/nix/2.24/command-ref/new-cli/nix)] Nix experimental high-level documentation