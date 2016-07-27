# OctoSend Documentation Project ![License badge][license-img] [![Readthedocs badge][readthedocs-img]][readthedocs-url]

## Overview

OctoSend  provides a  mailing campaign  service. The  user can  use one  or many
sending domains  and is able  to create  campaigns, follow their  evolutions and
retrieve statistics.

https://www.octosend.com/

## Description

This    is   the    repository   of    the   english    documentation   Octosend
https://www.octosend.com/.  It  hosts the  reference documentation that  will be
maintained and augmented regularly with  the new features documentation provided
on each release of the Octosend product.

## Environment

The  documentation is  intended to  be built  in a  readthedocs reStructuredText
environment which means the content are  wrote in the reStructuredText format in
rst  files and  that  the build  take  place in  a python  env  with the  sphinx
documentation tool.

## Local build

While this  documentation is  available online at  http://docs.octosend.com, for
contributing purpose, you  may want to clone  and build it localy.  Here are the
steps to follow to do it.

First, you need to add the dependencies to build the documentation properly. You
have to install sphinx and sphinx-rtd-theme:

```bash
$ pip install sphinx sphinx_rtd_theme
```

Additionally and for convenience, you can install sphinx-autbuild

```bash
$ pip install sphinx-autobuild
```

Then clone the  repository on you computer  (if you want to  contribute, you can
fork the  project, then  clone your fork),  go in the  directory and  launch the
build.

```bash
$ git clone https://github.com/octosend/docs.git
$ cd docs
$ make html
```

You will find your html files in the _build directory.

If  you  have installed  sphinx-autobuild,  you  can  also  launch in  the  same
documentation directory:

```bash
$ sphinx-autobuild . _build_html
```

It will  build the documentation  in the _build_html as  soon as you  update and
save a file.  Further more, it will launch  an http server (the url  is given by
the command, generally  http://127.0.0.8000) that will reload  the content after
each build.

## Conclusion

We want to constantly improve our documentations and products and your feedbacks
are  welcome (needed?!)  to do  it. Please  let us  know what  you think  of our
work. Thanks!

## Development

Feel free to contribute on GitHub.

```
    ╚⊙ ⊙╝
  ╚═(███)═╝
 ╚═(███)═╝
╚═(███)═╝
 ╚═(███)═╝
  ╚═(███)═╝
   ╚═(███)═╝
```

[license-img]: https://img.shields.io/badge/license-ISC-blue.svg
[readthedocs-img]: https://readthedocs.org/projects/pip/badge/?version=latest
[readthedocs-url]: http:/octosend.readthedocs.org/en/latest/