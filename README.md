# vmtest-action

This action wraps [vmtest][0] with a standard Github Actions interface to make
it easy to plug `vmtest` into your CI.

This action is designed to easily "drop in" to your existing CI workflows by
leveraging `vmtest`'s ability to map the host userspace into the guest VM. In
other words, any dependency wrangling or setup you do on the root host can be
used inside the VM without additional configuration.

## Usage

See [action.yml][1] for full manifest.

### Inputs

The following input parameters map 1:1 with `vmtest` [target fields][2].
Please see the link for full documentation. But for reference, here are the
available inputs again:

* `name`
* `image`
* `uefi`
* `kernel`
* `kernel_args`
* `command`

### Example usage

```yaml
steps:
- uses: actions/checkout@v3
- uses: danobi/vmtest-action@master
  with:
    name: '5.4 kernel'
    kernel: './kernels/bzImage-5.15.0-1022-aws'
    command: "/bin/bash -c 'uname -r | grep -e aws$'"
```

### Example workflow

```yaml
name: vmtest

on:
  push:
    branches: [ "master" ]
  pull_request:
    branches: [ "master" ]

jobs:
  build-test:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@v3

    - name: Run main.sh on "default" kernel
      run: |
        ./main.sh

    - name: Run main.sh in specified kernel but with same userspace
      uses: danobi/vmtest-action@master
      with:
        name: '5.4 kernel'
        kernel: './kernels/bzImage-5.15.0-1022-aws
        command: ${{ github.workspace }}./main.sh
```

[0]: https://github.com/danobi/vmtest
[1]: ./action.yml
[2]: https://github.com/danobi/vmtest#target
