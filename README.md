Open webOS
==========

Introduction
------------

This directory and the repository in which it resides hold upper level code used to aggregate the various [OpenEmbedded](http://openembedded.org) layers, into webOS.  It does this using Git submodules which are handled transparently only for the initial build.

Set-up
-------------

Build the webos-image by cloning the git repository:

     git clone https://github.com/openwebos/build-webos.git


Note: If you download the build-webos component from GitHub as an archive, tar.gz, or zip file, then you will not be able to build the image and will get the following error:

     fatal: Not a git repository (or any parent up to mount parent). Stopping at filesystem boundary (GIT_DISCOVERY_ACROSS_FILESYTEM not set).


Prerequisites
-------------

Before you can build, you will need some tools.  If you try to build
without them, bitbake will fail a sanity check and tell you
what's missing, but not really how to get the missing pieces.  You can
force all of the missing pieces to be installed using:

    $ sudo scripts/prerequisites.sh

This has been tested on Ubuntu 11.04 and 12.04 32-bit.

Note: Builds on 64-bit machines are not currently supported.


Building
--------

To configure the build for the `qemux86` emulator and to fetch the Git submodule sources:

    $ ./mcf -p 0 -b 0 qemux86

The `-p` and `-b` options set the make and bitbake parallelism values to the number of CPU cores found on your computer.

To build the bitbake component, type:

    $ make <componentName>
 
To kick off a full build of Open webOS, type the following, (which may take two hours on a reasonably fast workstation, or many more hours on a slower laptop or VM):

    $ make webos-image

Running
-------

To run the resulting build in the `qemux86` emulator, type:

    $ sudo ./runqemux86
    
Cleaning
--------
To blow away everything and do a clean build, you can remove the build folder and run `./mcf` again to create a new one:

    $ rm -rf BUILD-qemux86
    $ ./mcf -p 0 -b 0 qemux86

Note that the steps above are more efficient than blowing away the entire webOS tree, as that would also purge your `downloads` and `sstate-cache` folders.

Using the ARM emulator
----------------------
To build for the ARM emulator, specify `qemuarm` to `mcf` instead of `qemux86`. To run the resulting build, type:

    $ sudo ./runqemuarm

Images
------

The following images have been tested and should build: 

`core-image-minimal`: This is inherited verbatim from `openembedded-core.`

`webos-image`: This is an aggregator for webOS-specific components.

	
Adding a new package to webOS
-----------------------------

The procedure to add new packages to the webOS image depends on the build procedure for each individual package.

1. For a CMake-based build, you will need to inherit from the `webos_cmake` class. An example recipe can be found in [`librolegen.bb`](https://github.com/openwebos/meta-webos/blob/master/recipes-webos/librolegen/librolegen.bb).

1. For autoconfig/automake based build procedure, you will need to inherit from the `autotools` class. An example recipe can be found in [`c-ares_1.7.4.bb`](https://github.com/openwebos/meta-webos/blob/master/recipes-upstreamable/c-ares/c-ares_1.7.4.bb).

1. If the package source is fetched from Github, you will need to add the following to your `local.conf` file. You must not set `SRCREV` in the package recipe directly.

        $ vi BUILD-<machine>/conf/local.conf
        SRCREV_pn-<pkg name> ?= "commit-id" or "${AUTOREV}"
		
1. To include the new package in the image, add the following to your `local.conf`. Once the package is functional, let us know and we'll add it permanently to the image.

        $ vi BUILD-<machine>/conf/local.conf
        IMAGE_INSTALL_append = " <pkg name>"  # Note the space before the name

1. Package checksum is calculated using the Message-Digest algorithm. Use `md5sum` to generate the MD5 hash used for `LIC_FILES_CHKSUM`.

Build times
-----------

Typical build times on modern servers are running anywhere from 8 - 20 hours for single threaded builds. These times, and their variations, are primarily due to the cost of downloading source over the internet. Local mirrors are expected to cut these times by at least an order of magnitude as well as to enable sharing of prebuilt binaries. With preseeded downloads and very high parallelism, (48-core machines of which only about 16 - 24 are utilized), the build can be brought down to under an hour.

# Copyright and License Information

Unless otherwise specified, all content, including all source code files and
documentation files in this repository are:

Copyright (c) 2008 - 2012 Hewlett-Packard Development Company, L.P.

Unless otherwise specified or set forth in the NOTICE file, all content,
including all source code files and documentation files in this repository are:
Licensed under the Apache License, Version 2.0 (the "License");
you may not use this content except in compliance with the License.
You may obtain a copy of the License at

http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.
