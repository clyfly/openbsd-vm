# Run GitHub CI in OpenBSD ![Test](https://github.com/clyfly/openbsd-vm/workflows/Test/badge.svg)

Use this action to run your CI in OpenBSD.

The github workflow only supports Ubuntu, Windows and MacOS. But what if you need to use OpenBSD?

This action is to support OpenBSD.


Sample workflow `test.yml`:

```yml

name: Test

on: [push]

jobs:
  test:
    runs-on: ubuntu-latest
    name: A job to run test in OpenBSD
    env:
      MYTOKEN : ${{ secrets.MYTOKEN }}
      MYTOKEN2: "value2"
    steps:
    - uses: actions/checkout@v4
    - name: Test in OpenBSD
      id: test
      uses: clyfly/openbsd-vm@
      with:
        envs: 'MYTOKEN MYTOKEN2'
        usesh: true
        prepare: |
          pkg_add curl

        run: |

          pwd
          ls -lah
          whoami
          env
          
          




```


The latest major version is: ``, which is the most recommended to use. (You can also use the latest full version: ``)  


If you are migrating from the previous `v0`, please change the `runs-on: ` to `runs-on: ubuntu-latest`


The `envs: 'MYTOKEN MYTOKEN2'` is the env names that you want to pass into the vm.

The `run: xxxxx`  is the command you want to run in the vm.

The env variables are all copied into the VM, and the source code and directory are all synchronized into the VM.

The working dir for `run` in the VM is the same as in the Host machine.

All the source code tree in the Host machine are mounted into the VM.

All the `GITHUB_*` as well as `CI=true` env variables are passed into the VM.

So, you will have the same directory and same default env variables when you `run` the CI script.

The default shell in OpenBSD is `ksh`, if you want to use `sh` to execute the `run` script, please set `usesh` to `true`.

The code is shared from the host to the VM via `rsync` by default, you can choose to use to `sshfs` share code instead.


```

...

    steps:
    - uses: actions/checkout@v4
    - name: Test
      id: test
      uses: clyfly/openbsd-vm@
      with:
        envs: 'MYTOKEN MYTOKEN2'
        usesh: true
        sync: sshfs
        prepare: |
          pkg_add curl



...


```

You can also set `sync: no`, so the files will not be synced to the  VM.


When using `rsync`,  you can define `copyback: false` to not copy files back from the VM in to the host.


```

...

    steps:
    - uses: actions/checkout@v4
    - name: Test
      id: test
      uses: clyfly/openbsd-vm@
      with:
        envs: 'MYTOKEN MYTOKEN2'
        usesh: true
        sync: rsync
        copyback: false
        prepare: |
          pkg_add curl



...


```



You can add NAT port between the host and the VM.

```
...
    steps:
    - uses: actions/checkout@v4
    - name: Test
      id: test
      uses: clyfly/openbsd-vm@
      with:
        envs: 'MYTOKEN MYTOKEN2'
        usesh: true
        nat: |
          "8080": "80"
          "8443": "443"
          udp:"8081": "80"
...
```


The default memory of the VM is 6144MB, you can use `mem` option to set the memory size:

```
...
    steps:
    - uses: actions/checkout@v4
    - name: Test
      id: test
      uses: clyfly/openbsd-vm@
      with:
        envs: 'MYTOKEN MYTOKEN2'
        usesh: true
        mem: 4096
...
```



It uses [the OpenBSD 7.5](conf/default.release.conf) by default, you can use `release` option to use another version of OpenBSD:

```
...
    steps:
    - uses: actions/checkout@v4
    - name: Test
      id: test
      uses: clyfly/openbsd-vm@
      with:
        release: "7.3"
...
```

All the supported releases are here: OpenBSD  7.2, 7.3, 7.4, 7.5, test.releases [See all here](conf)


# Under the hood

We use Qemu and Libvirt to run the OpenBSD VM.




# Upcoming features:

1. Runs on MacOS to use cpu accelaration.
2. Support ARM and other architecture.




