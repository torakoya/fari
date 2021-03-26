# Fari

Fari is a Unix tool for managing software building procedures. It can:

* Download, build and install software
* Log the console output while building

## Requirements

* wget or curl (for `fetch`)
* tar that can accept -a with -x (for `extract`)

## Getting Started

Place the executable `fari` somewhere in `$PATH`.

Write `~/.farirc`. See `_farirc` for a sample.

Make a directory for a package, `foobar`, and get into it.

Write a recipe, named `recipe`:

    pkgname=foobar
    pkgver=1.2.3
    pkgext=tar.gz
    pkgurlbase=https://www.example.org/foobar/download/
    
    _config () {
        ./configure --prefix="$prefix"
    }

Build it with fari:

    $ fari foobar build

## Command Line Syntax

Fari's command line syntax is:

    $ fari PACKAGE TASK

The tasks is one of these:

* `fetch`: Download the source
* `extract`: Extract the source archive
* `patch`: Modify the source code
* `config`: Prepare the source code for building
* `build`: Build the source code
* `test`: Test the builds
* `install`: Install the builds

The undone tasks are performed automatically in this order.

Those tasks are performed only once. If you want to perform a finished task again, add the prefix `re`:

    $ fari foobar rebuild

If the task is omitted, that means `build`.

When the `recipe` is at the current working directory, you can specify it as `.`:

    $ fari . build

## Recipe Syntax

Fari recipes are just Bourne shell scripts.

See `fari` for the default task functions.

## Reserved Variables

### Defined in farirc

* `archhome`: (Optional) Where the prefetched tarballs are located.
* `cachedir`: (Optional) Where a fetched tarball will be stored. The default is `$XDG_CACHE_HOME/fari` or `~/.cache/fari`.
* `rechome`: Where the recipes are located.
* `workdir`: (Optional) Where the tasks are performed. The default is `$PWD/x`.

### Defined in Recipes

* `pkgname`: The package name.
* `pkgver`: The package version.
* `pkgext`: (Optional) Used for determining `pkgfile`.
* `pkgfile`: (Optional) The locally saved tarball's file name. The default is `pkgname-pkgver.pkgext`.
* `pkgurlbase`: (Optional) Used for determining `pkgurl`.
* `pkgurl`: The URL of the source archive. The default is `{pkgurlbase}{pkgfile}`
* `srcname`: (Optional) The directory name that contains the extracted tarball. The default is `pkgname-pkgver`.
* `srcdir`: (Optional) Where the source code is located. The default is `workdir/srcname`.
* `blddir`: (Optional) Where you perform `config`, `build`, etc. The default is the same as `srcdir`.
