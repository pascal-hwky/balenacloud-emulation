# balenaCloud emulated build breaks application

 > A [workaround](https://forums.balena.io/t/balenacloud-emulated-build-breaks-application/194951/9?u=pdboef) for this problem is available on balenaForums.

## Problem statement

balenaCloud offers two types of builders for ARM devices:

 1. Native ARM builders
 2. [x86 builders using QEMU emulation](https://www.balena.io/docs/learn/deploy/deployment/#--emulated--e)

This repository shows that there are applications for which these two builders do not produce the same output. In fact, emulated building *produces a broken container*. The sample application provided is extremely simple and consists of a single `Dockerfile` containing the following line:

```
FROM telegraf
```

## Prerequisites

 1. A [balenaCloud](https://dashboard.balena-cloud.com/) account.
 2. Installation of [balena-cli](https://github.com/balena-io/balena-cli).
 3. A balenaCloud application named `balenacloud-emulation` with an ARM device type (such as a Raspberry Pi) and at least one device.

## Steps to reproduce

Run the following commands to download this repository and reproduce the issue:

```
git clone https://github.com/pascal-hwky/balenacloud-emulation.git
cd balenacloud-emulation
balena push balenacloud-emulation --emulated
```

After the deployment is completed, you should see the following device output:

```
standard_init_linux.go:211: exec user process caused "exec format error"
```

This error message indicates that the Docker image contains invalid instructions and was therefore compiled for a different architecture. Repeating the build *without emulation* (by leaving out the `--emulated` flag) does not cause this issue.

The balena-cli [documentation](https://www.balena.io/docs/learn/deploy/deployment/#--emulated--e) mentions the following:

 > The emulated builds will also happen on the rare occasion that the native ARM builder is overloaded or unavailable.

However, as in the above case the emulated build fails, this could cause applications to randomly crash.