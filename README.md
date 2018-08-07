# deb-creator

creates `*.deb` files from known folders.

it doesn't require any fancy library to work, just standard tools.

## Requirements:

- `bash`
- `mktmp`
- `ar`
- `tar`
- `gzip`

## Usage:

```
$ ./deb-creator <project-folder>
```

## Required File Structure:

- `project/` directory:
  - `data/` (**required**)
  - `control.install` (**require**)
  - `postinstall.script` (_not required_)
  - `postremove.script` (_not required_)
  - `preinstall.script` (_not required_)
  - `preremove.script` (_not required_)
  - `triggers` (_not required_)

### Folder data/

this folder will contain a sort of `chrooted` environment.

Example:
```
$ cd data
$ find .
./include/
./include/libCoolThis/
./include/libCoolThis/libCoolThis.h
./lib/
./lib/libCoolThis/
./lib/libCoolThis/libCoolThis.a
./lib/libCoolThis/libCoolThis.so
./usr/
./usr/share/
./usr/share/doc/
./usr/share/doc/libCoolThis/
./usr/share/doc/libCoolThis/copyright
```
### control.install

control.install requires the following data fields:
- PACKAGE
- VERSION
- SECTION
- ARCHITECTURE
- MAINTAINER_NAME
- MAINTAINER_EMAIL
- DESCRIPTION
- DEPENDENCIES
- HOMEPAGE

Example of `control.install` file:

```
PACKAGE="your_package_name"
VERSION="1.00"
SECTION="user/hidden"
ARCHITECTURE="amd64"
DEPENDENCIES="libc6 (>= 2.17), adduser"
MAINTAINER_NAME="maintainer full name"
MAINTAINER_EMAIL="maintainer@email.example.com"
HOMEPAGE="https://my.example.com/coolthis-lib.html"
DESCRIPTION="my project description\nthat can contain new lines."
```

Specifically here is described the required format:
- Version ([documentation](https://www.debian.org/doc/debian-policy/ch-controlfields.html#version))
    - Requires to follow the following syntax `[epoch:]upstream_version[-debian_revision]`

- Section ([documentation](https://www.debian.org/doc/debian-policy/ch-archive.html#s-subsections))

- Architecture ([documentation](https://www.debian.org/doc/debian-policy/ch-controlfields.html#architecture)):
    - `amd64`		64-bit x86 (x86_64)
    - `arm64`		64-bit ARM (AArch64)
    - `armel`		EABI ARM
    - `armhf`		Hard Float ABI ARM
    - `i386`		32-bit x86 (i386)
    - `mips`		MIPS (big-endian mode)
    - `mipsel`		MIPS (little-endian mode)
    - `mips64el`	MIPS (64-bit little-endian mode)
    - `ppc64el`		Power7+, Power8
    - `s390x`		System z

- Dependencies:

Dependencies are separated by a `, ` (`comma<space>`).

Example:

```
libacl1 (>= 2.2.51-8), libapparmor1 (>= 2.9.0-3+exp2), libaudit1 (>= 1:2.2.1), libblkid1 (>= 2.19.1), libc6 (>= 2.17), libcap2 (>= 1:2.10), libcryptsetup4 (>= 2:1.4.3), libgcrypt20 (>= 1.7.0), libgpg-error0 (>= 1.14), libidn11 (>= 1.13), libip4tc0 (>= 1.6.0+snapshot20161117), libkmod2 (>= 5~), liblz4-1 (>= 0.0~r127), liblzma5 (>= 5.1.1alpha+20120614), libmount1 (>= 2.26.2), libpam0g (>= 0.99.7.1), libseccomp2 (>= 2.3.1), libselinux1 (>= 2.1.9), libsystemd0 (= 232-25+deb9u4), util-linux (>= 2.27.1), mount (>= 2.26), adduser, procps
```

### triggers

A dpkg trigger is a facility that allows events caused by one package
but of interest to another package to be recorded and aggregated, and
processed later by the interested package.  This feature simplifies
various registration and system-update tasks and reduces duplication
of processing.

[documentation](https://lists.debian.org/debian-dpkg/2007/04/msg00076.html)

Example:

```
interest-noawait /etc/init.d

```

### `*.install`

These scripts `postinstall.script`, `postremove.script`, `preinstall.script`, `prerm.script` will be executed if added.

Suggested shebang command: `#!/bin/sh -e`
