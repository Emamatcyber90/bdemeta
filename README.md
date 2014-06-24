##bde-meta - build and test BDE-style code

### SYNOPSIS

`bde-meta [--root ROOT] [--cflag NAME:FLAG] [--ldflag NAME:FLAG] [--dependency NAME:DEPENDENCY] MODE ...`<br/>

Where `MODE` is one of:

`bde-meta walk GROUP [GROUP ...]`<br/>
`bde-meta cflags GROUP [GROUP ...]`<br/>
`bde-meta ldflags GROUP [GROUP ...]`<br/>
`bde-meta ninja [-cc CC] [--cxx CXX] [--ar AR] GROUP [GROUP ...]`<br/>
`bde-meta runtests [TEST ...]`:

### DESCRIPTION

`bde-meta` is a set of basic tools to assist building and testing [BDE-style
source trees](https://github.com/bloomberg/bde).  It can generate [ninja build
files](https://github.com/martine/ninja) for a particular package group,
provide `cflags`/`ldflags` (`-I`/`-L/-l` rules respectively by default, along
with any user-supplied ones) when building applications that depend on such
package groups.  It can also run all the unit tests for a particular package
group.

`bde-meta` supports finding package groups across [disconnected
directory structures](#roots), [arbitrary flags](#flags) for any given
dependency, and [dependencies that are not actually package groups](#units).

The contents of `~/.bdemetarc` and `.bdemetarc` will be considered as
command line flags in addition to those specified at invocation.

### INSTALLATION

Install using `pip`:

    $ pip install git+https://github.com/frutiger/bde-meta

**Note**: the script name has changed from `bde-meta` to `bdemeta`.

### OPTIONS

`bde-meta` takes any number of the following options:

  * `--root ROOT`
    Add the specified `ROOT` to the package group search path

  * `--cflag NAME:FLAG`
    Append the specified `FLAG` when generating cflags for the dependency
    with the specified `NAME`.

  * `--ldflag NAME:FLAG`
    Append the specified `FLAG` when generating ldflags for the dependency
    with the specified `NAME`.

  * `--dependency NAME:DEPENDENCY`
    Consider the specified `NAME` to have the specified `DEPENDENCY`.

`bde-meta` runs in one of five modes as given by the first positional argument:

  * `walk GROUP [GROUP ...]`:
    Walk and topologically sort dependencies

  * `cflags GROUP [GROUP ...]`:
    Produce flags for compiling dependents

  * `ldflags GROUP [GROUP ...]`:
    Produce ldflags for linking dependents

  * `ninja [--cc CC] [--cxx CXX] [--ar AR] GROUP [GROUP ...]`:
    Generate a ninja build file

  * `runtests [TEST ...]`:
    Run BDE-style unit tests

### ROOTS
<a name="roots"></a>

`bde-meta` will look for package groups in directories specified by (possibly
multiple) `--root` arguments.  This makes it easy to build code across multiple
BDE-style repositories, including your own.

### FLAGS
<a name="flags"></a>

Compiler and linker flags may be specified in addition to the ones generated by
the structure of the package group.  These are specified by supplying
`--cflag NAME:FLAG` or `--ldflag NAME:FLAG` where `NAME` specifies the name of
the dependency, and `FLAG` the appropriate cflag/ldflag.  For example,
specifying `--cflag BSL:-DBDE_BUILT_TARGET_EXC` will provide that as a flag for
`bsl` and every dependent of `bsl`.

### UNITS
<a name="units"></a>

`bde-meta` supports dependencies that are not package groups (i.e. 'units').
This can be useful when depending on headers and libraries provided by the
system.  By default, such dependencies introduce no new flags unless such a
flag has been specified with a `--cflag NAME:FLAG` or `--ldflag NAME:FLAG`.  In
order to ensure that link lines are correctly topologically sorted, `bde-meta`
will require dependency information that can be specified with `--dependency
NAME:DEPENDENCY`.

### EXAMPLES

To generate the ninja build file for `bdl` and all its transitive dependencies
(i.e. `bsl`) into `build.ninja`:

    $ bde-meta ninja bdl > build.ninja

To build a static library for just `bsl` into `out/libs`:

    $ ninja bsl

To build a static library for `bdl` and each of its transitive dependencies:

    $ ninja bdl

To build tests for `bdl` and the tests for all its transitive dependencies:

    $ ninja tests

To build a specific test driver:

    $ ninja bsls_platform.t

To run all of the previously built tests:

    $ bde-meta runtests

To build `m.cpp` with `bdl` as a dependency and link it with all its
dependencies:

    $ c++ $(bde-meta cflags bdl) m.cpp $(bde-meta ldflags bdl)

To produce a set of cflags for third-party tools such as 'YouCompleteMe':

    $ bde-meta cflags bdl

### LICENSE

Copyright (C) 2013 Masud Rahman

Permission is hereby granted, free of charge, to any person obtaining a copy of
this software and associated documentation files (the "Software"), to deal in
the Software without restriction, including without limitation the rights to
use, copy, modify, merge, publish, distribute, sublicense, and/or sell copies
of the Software, and to permit persons to whom the Software is furnished to do
so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.

