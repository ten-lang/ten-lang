---
layout: default
title:  Getting Started
---
Getting Ten installed on your machine is pretty straightforward for Unix
based systems.  Unfortunately, while the core language implementation is
portable, the Ten Command Line Interface and Ten Module Loader implementations
currently depend on POSIX utilities.

There are three items that need to be installed in order to have a working
Ten CLI.

- [libten](https://github.com/ten-lang/libten)

  This is the core language implementation, it's implemented as a library
  rather than an executable since the language is expected to be embedded
  in other applications.

- [libtml](https://github.com/ten-lang/libtml)

  This implements the Ten Module Loader, which extends the core Ten runtime
  to allow for loading modules from file.

- [ten](https://github.com/ten-lang/ten)

  This is the actual Ten Command Line Interface, which can execute script
  files and implements a simple REPL interface.

## Linux Packages
If you're on one of the more popular Linux systems, or something derived
form one of these, then the simplest way to install all of these will be
through the system package manager.  We maintain both an
[RPM](https://github.com/ten-lang/rpm-repo)
and a
[DEB](https://github.com/ten-lang/deb-repo)
package repository for distributing pre-built binary packages.

### RPM
On `yum` based systems just add the repo to your package manager with:

    wget -O - https://git.io/fjOZI | sudo sh

Then install the CLI with:

    sudo yum install ten

This will automatically install the other two units as well.

### DEB
Similarly for `apt` based systems add Ten's package repo with:

    wget -O - https://git.io/fj25i | sudo sh

And install the CLI with:

    sudo apt install ten

The other packages will be installed automatically.


## Source Code
If not on a Linux machine, or not using one of the supported package
managers, then building from source is also pretty straightforward, but
you'll need to have GNU make, a C compiler, and binutils installed to
for the makefiles to work.

First build and install `libten` with:

    git clone https://github.com/ten-lang/libten.git
    cd libten
    make
    sudo make install

Next do `libtml` with:

    git clone https://github.com/ten-lang/libtml.git
    cd libtml
    make
    sudo make install

An finally, the CLI itself:

    git clone https://github.com/ten-lang/ten.git
    cd ten
    make
    sudo make install

Note that these install to the `/usr/local/` directory subtree by default,
so you may need to add the respective `lib`, `lib64`, and `bin` directories
to your library and executable path.  For Linux and most Unix variants the
library paths are kept in the `LD_LIBRARY_PATH` environment variable while
executable paths are in `PATH`.  MacOS doesn't use `LD_LIBRARY_PATH`, instead
both library and executable directories are put in `PATH`.

The install prefix can be changed for each installation by adding
`PREFIX=...` to the `make install` command line.  And a debug build
can be installed by putting `PROFILE=debug`  in both make commands.
The debug build of `libten` is particularly important for embedders
since it includes assertions that make sure the API is being used
properly.

If the required build tools aren't available for your system, then
a manual build can also be done.  The `libten` code base is implemented
in standard C, with no dependencies outside the standard library; and
`libtml` and `ten` should be portable across most Unix based systems.

For `libten` just do something like:

    gcc src/*.c -D ten_LIBM -l m -fpic -shared -o libten.so

And `libtml` only has one source file:

    gcc tml.c
    ar rcs libtml.a tml.o

The `ten` package also only has one source file, but needs
to be linked against `libten`, `libtml`, and `libdl`.

    gcc ten.c -l ten -l tml -l dl -o ten

## Launching a REPL
An interactive Read-Eval-Print-Loop mode of the Ten interpreter can be
launched with:

    ten

This should present you with a prompt where a Ten expression can be
entered, it'll be evaluated and the result displayed automatically.

    $ 1 + 2
    : 3
    $ show( "Hello, World!", N )
    Hello, World!
    : ()

## Executing Scripts
Ten scripts can be executed with:

    ten script.ten

Where `script.ten` is your script file.  Alternatively, since Ten ignores
shebang lines, the line:

    #!/usr/bin/env ten

To the start of the script, make it executable, then just do:

    ./script.ten

## Next Steps
Ten is a pretty immature language, so at this point there aren't many
learning resources available.  But if you have experience with other
languages then it shouldn't be too difficult to dive right in to the
[Ten Manual](https://github.com/ten-lang/libten/tree/master/docs/manual).

Also if you have any questions, suggestions, bug reports, etc. then
feel free to contact me or submit an issue on Github.  I can be
reached:

- by email:  [stubbs.ray@gmail.com](mailto:stubbs.ray@gmail.com)
- on reddit: [u/raystubbs](https://www.reddit.com/user/raystubbs)
