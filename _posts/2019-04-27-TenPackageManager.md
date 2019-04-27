---
layout: post
title:  Ten Package Manager
---

This is a long way off since I'll be starting a new (my first) dev
job pretty soon, so won't have as much time to work on Ten related
stuff.  But I've been thinking of a design for Ten's package management
system; so figured I'd put my thoughts down here for the record.

After considering a few different approaches, I'm thinking I want the
package management system to be mostly transparant.  It should be
possible to package up a project and distribute it without any extra
config; but providing some meta data adds utilitiy and security to
a package, and specific indexes may enforce certain security standards.

Here's what I came up with.

## Packages
Packages will be provided as archives in one of the following formats:

- `.zip`
- `.tar`
- `.tar.gz` (`.tgz`)
- `.tar.xz` (`.txz`)

These needn't contain any meta info, the package name is derived from
the index that refers to it (see below).  However the following optional
files may be provided.  All such files should be markdown formatted or
markdown compatible plain text, though the `.md` extension can be
omitted.

### LICENSE.md
The project license.  This can contain anything, but copying the raw
license text from one of the popular licenses will allow the package
manager interface to identify, summarize, and categorize the license.

### AUTHORS.md
The authors and maintainers of the project.  This may provide arbitrary
text as well as a list of authors.  The author list should be given
as a normal markdown list of names, with optional email address,
and social media or home page references.  For example:

    - Ray Stubbs <stubbs.ray@gmail.com> (reddit:u/raystubbs) (home:www.raystubbs.me)
    - Other Person <operson@hotmail.com>

The list can be stylized with markdown but not HTML tags, and the
package manager interface is free to strip away this styling to
give its own presentation.  For example social media icons might
be displated for known profile types.

As an alternative to a root level list, this file can provide two
separate lists under different headers for the core development
team and less involved contributors.  The keyword `Core` present
in a header indicates that the first top level list in the section
will give the list of core developers, and the keyword `Other`
marks the section for other contributors.  If either of these two
sections are present in the file then the PM won't search for
for the author list elsewhere in the file.

### CHANGELOG.md
This should give an overview of all the versions of the package
that have been released so far.  Ideally this should follow the
format of [https://keepachangelog.com](https://keepachangelog.com)
but any format is acceptable so long as the first 'thing' that
looks like a version number in a header is actually the current
version of the package.  A 'thing' looks like a version number if
it matches the regex `[0-9]+\.[0-9]+(\.[0-9]+)-[a-zA-Z0-9_\-]+`
but the package manager will ignore anything besides the initial
semantic version for any purposes other than display.

If a 'thing' that looks like a date is given in the same header
as the version, then this might also be noted by the PM or interface.

### README.md
This should give a description of the project, the first paragraph
will be taken as a summary of the project if it's short enough.

### DEPENDS.md
This file lists the dependencies required by the project, as well
as optional descriptions or other auxiliary text.  A dependency
list can be given as the first list in encountered in the document,
or under subsections with header keywords `Build` or `Buildtime`
and `Run` or `Runtime`.  If either such subsection is given then
the PM won't search for dependencies elsewhere.  Whether given in
one of these subsections or not a header with the `Optional` or
`Required` keywords indicates whether the dependencies are actually
needed or just prefered.  The nesting order of the `Optional|Required`
headers and `Run|Build` headers doesn't matter, and a combination
of the keywords can be given in a single header.  For example:

    # Dependencies
    This file gives a listing of dependencies for this project.
    
    ## Optional Runtime Dependencies
    - fs#0.1.0
    - gl#1
    - opkg
    
    ## Required Runtime Dependencies
    ...
    
    ## Optional
    
    ### Build Dependencies
    ...
    
    ## Required
    
    ### Build Dependencies
    ...

### FILESYSTEM.md
This file gives a table of system specific file/directory paths
and their mapping to virtual paths accessable via a Ten filesystem
library that enforces this system.  The table should have the form:

    |       Virtual      |      Real      |   Access   |   Type    |
    |--------------------|----------------|------------|-----------|
    | /some/vir/path     | C:\\win\dir    |   rwx      |  user     |
    | /some/vir/path     | /some/unix/file|   rw       |  shared   |

The PM will automatically recognize `C:\\` (or `D:\\` etc.) paths
as Windows paths and `/` paths as unix paths.  Both virtual and real
paths must be given as absolute paths.  Headers can also be used to
specialize the mapping by platform.  The first table under a header
with keyword `Unix` will apply to all Unix-like platforms, and the
first under a header with keyword `DOS` will apply to all DOS-base
systems.  Similarly more specific keywords such as `Mac` or `MacOS`
or `Apple` can be used, and `Linux` or `BSD` and whatnot.  The
architecture can also be mentioned, for example `64bit`, `64 bit`,
`x86`, or `x86_64` and similar keywords will be recognized.  The
PM should give a warning if a DOS path is given in a non-DOS section.

If no path is given in the `Real` column of a row then the virtual
path won't link to the real filessystem and will exist only in the
package's private directory, allocated somewhere in the system
depending on platform.

If the path refers to a directory, or other non-regular file type,
then this type should be indicated in the `Access` column as the
Unix-like type letter, for example `d` for directory.

Though the PM won't implement filesystem operations itself, it'll
make this information available to filesystem modules, which should
enforce the given permissions and mapping.  The package manager only
itself only cares about the `Real` and `Type` values.  The `Type`
will tell the PM how to go about cleanup when the package is removed.
If given as `user` then the file/directory won't be removed with
the package.  If given as `shared` then it's considered to be shared
by all versions of the package, so will only be removed when all
versions have been uninstalled.  The `local` type indicates a file
that should be removed when the package is removed.

### CONFIG.md
This file gives a listing and description of configuration parameters,
which can be configured in the package manager, and can be accessed
by PM utilities within the package.

The PM will recognize headers with keywords such as `String` or
`Strings` as a string parameter, or list of strings.  Similarly
keywords like `Path`, `Option`, `URL`, `Size`, `Count`, `Offset`,
etc. will also be recognized with their plural counterparts.

Depending on the parameter type a unit may also need to be specified,
which the PM will attempt to derive from the description under the
respective header.  It'll look for phrases like `... in bytes` or
`... in meters` or `... in metric units` or `... in imperial units`
etc.

A default value can also be specified in the description as a phrase
like `default is ...`, `default value is ...`, `default of ...`, or
`default = ...`.

## Indices
Indices are also archives of one of the above formats, but these
are used to tell the PM where to find other packages, and what
to call them.

An index can either be registered with the PM as a URL or a local
archive.  If given as a URL then it'll be checked for updates
regularly and re-installed.

An index archive should have a tree of the form:

    + package1/
        - major-minor-patch.tpl
        ...
    + package2/
        ...

Where each subdirectory of the root index gives the name of a package,
and contains Ten Package Link files, signed by a trusted entity whose
key must be registered with the local package manager.

Omitting the signature text, each `.tpl` file should contain
a single like of the form:

    NAME        URL     HFUN    HASH

Where `URL` gives the URL where this version of the package
archive can be found, `HFUN` gives the hash function, such as
`SHA2`, `MD5`, etc. and `HASH` gives the actual plaintext hash.

## Conclusion
This of course describes the system at a conceptual level, there
will be plenty more details to wrestle with in designing an actual
implementation.  But I think the flexibility of the system, the
ability to effortlessly package a project by just zipping it up,
makes it pretty appealing.  The security aspect of things will have
to be handled by individual indices and the standards they enforce,
which really is the way things should be.  Additional tools can
be implemented for project management, for example a tool to find
and add dependencies to the `DEPENDS.md` file, or to install
project dependencies.
