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
    arcext=tar.gz
    arcurlbase=https://www.example.org/foobar/download/
    
    _config () {
        ./configure --prefix="$prefix"
    }

Build it with fari:

    $ fari foobar build

## Command Line Syntax

Fari's command line syntax is:

    $ fari PACKAGE TASK

There are two types of tasks: building tasks and utility tasks.

The building tasks are these:

* `fetch`: Download the source
* `extract`: Extract the source archive
* `patch`: Modify the source code
* `config`: Prepare the source code for building
* `build`: Build the source code
* `test`: Test the builds
* `install`: Install the builds

The undone tasks are performed automatically in this order.

The utility tasks are these:

* `files`: Create the file `files`
* `reg`: Register the installed files
* `del`: Delete the registered files
* `pack`: Create a tarball containing the registered files

Most tasks are performed only once. If you want to perform a finished task again, add the prefix `re`:

    $ fari foobar rebuild

If the task is omitted, that means `build`.

When the `recipe` is at the current working directory, you can specify it as `.`:

    $ fari . build

If you want to specify `$pkgver` instead of getting from the recipe, use `@` following `$pkgname`:

* `foobar@1.2.3`
* `.@1.2.3`
* `@1.2.3` (You can omit `.` followed by `@`)

## Recipe Syntax

Fari recipes are just Bourne shell scripts.

See `fari` for the default task functions.

## Reserved Variables

### Defined in farirc

* `archome`: (Optional) Where the prefetched tarballs are located.
* `cachedir`: (Optional) Where a fetched tarball will be stored. The default is `$XDG_CACHE_HOME/fari` or `~/.cache/fari`.
* `rechome`: Where the recipes are located.
* `workdir`: (Optional) Where the tasks are performed. The default is `$PWD/x`.
* `prefix`: (Optional) Where the software is installed. The default is `/usr/local`.
* `regdir`: (Optional) Where the installed software packing lists are located. The default is `prefix/var/lib/fari`.

### Defined in Recipes

* `pkgname`: The package name.
* `pkgver`: The package version.
* `arcname`: (Optional) The archive's basename. The default is `pkgname`.
* `arcext`: (Optional) Used for determining `arcfile`. The default is `tar.gz`.
* `arcfile`: (Optional) The locally saved tarball's file name. The default is `arcname-pkgver.arcext`.
* `arcurlbase`: (Optional) Used for determining `arcurl`.
* `arcurl`: The URL of the source archive. The default is `{arcurlbase}{arcfile}`
* `srcname`: (Optional) The directory name that contains the extracted tarball. The default is `arcname-pkgver`.
* `srcdir`: (Optional) Where the source code is located. The default is `workdir/srcname`.
* `blddir`: (Optional) Where you perform `config`, `build`, etc. The default is the same as `srcdir`.

## User-defined Tasks

When you want to define your own task, write an `_fr_` prefixed function in the recipe:

    _fr_conffile () {
        echo "set debug=yes" > ~/.foobarrc
    }

And invoke it like this:

    $ fari . conffile

## files

When you want to perform the task `reg`, you must create the file `files` that contains a sorted list of the installed files, symlinks and directories:

    bin/foobar
    include/foobar.h
    lib/libfoobar.so
    lib/libfoobar.so.1
    lib/libfoobar.so.1.2.3
    share/doc/foobar
    share/doc/foobar/LICENCE
    share/doc/foobar/README
    share/man/man1/foobar.1

You can create `files` with the command `files`:

    $ fari . install DESTDIR=$(pwd)/dest
    $ fari . files dest
