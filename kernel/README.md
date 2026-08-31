# Linux CIFS Hang On Disconnect: Build the Kernel

The kernel itself is a submodule at `kernel/linux/`. The branches I used are:

- `baseline/lkml/linux-rdma/20260908154454.10966-1-ammrat13@gmail.com`: the
  baseline kernel I used; the "bad" kernel
- `lkml/linux-rdma/20260908154454.10966-1-ammrat13@gmail.com`: the "bad" kernel
  with my patch applied; the "good" kernel

## Build Environment

A docker environment to build the kernel in is provided.

_Action Items:_

- Build the docker environment with

  ```sh
  docker build -t ghcr.io/ammrat13/linux-cifs-build:latest ./kernel/
  ```

## Configuration

The kernel configurations are in `kernel/config/`. Three are given:

- `config-7.0.0-30-generic`: the kernel configuration taken from the Ubuntu
  Server 26.04.1 image, used as a baseline

- `config-7.3.0-rc1`: like previous, after being passed through
  `make olddefconfig` and setting

  ```txt
  CONFIG_SYSTEM_TRUSTED_KEYS=""
  CONFIG_SYSTEM_REVOCATION_KEYS=""
  ```

- `config-7.3.0-rc1-cifs`: like previous, after setting

  ```txt
  CONFIG_CIFS_SMB_DIRECT=y
  ```

I used the `config-7.3.0-rc1-cifs` configuration for all my testing.

## Build

Before building, you might want to clean the build directory by running
`make mrproper` in `kernel/linux/`.

There is a build script to help automate building the kernel. It takes three
positional arguments:

- the branch to build; the script will `git checkout` this branch in the
  submodule
- the config to use; relative to `kernel/config/`
- the directory to place the built packages in

_Action Items:_

- Enter the docker environment with

  ```sh
  docker run --rm -it \
    --user $(id -u):$(id -g) --security-opt label=type:spc_t \
    -v .:/app/ \
    ghcr.io/ammrat13/linux-cifs-build:latest
  ```

- Build the required kernel configurations. For example

  ```sh
  ./kernel/kernel-build.sh \
    baseline/lkml/linux-rdma/20260908154454.10966-1-ammrat13@gmail.com \
    config-7.3.0-rc1-cifs \
    results/rxe/bad/deb/
  ```
