# vagrant-haskell: haskell in a box

`vagrant-haskell` is a minimal tinycore-based docker host running [darinmorrison/docker-haskell](https://github.com/darinmorrison/docker-haskell).

## Why Vagrant?

`vagrant` provides a more convenient interface for interacting with `docker-haskell` as a portable Haskell development environment than using `docker` or `boot2docker` directly.

*   **A familiar VM-style workflow**

        ~/vagrant-haskell ❯❯❯ vagrant up --provider=docker    # initialize vm, download image, launch container

*   **Convenient SSH access**

        ~/vagrant-haskell ❯❯❯ vagrant ssh
        ==> default: SSH will be proxied through the Docker virtual machine since we're
        ==> default: not running Docker natively. This is just a notice, and not an error.
        Warning: Permanently added '172.17.0.2' (ECDSA) to the list of known hosts.
        Last login: Sat May 31 00:01:06 2014 from 172.17.42.1
        root@219d34de7386:~# exit
        logout
        Connection to 172.17.0.2 closed.
        Connection to 127.0.0.1 closed.
        ~/vagrant-haskell ❯❯❯

*   **NFS-based shared folders**

        ~/vagrant-haskell ❯❯❯ echo 'main = print "hello, docker and vagrant!"' > data/Main.hs
        ~/vagrant-haskell ❯❯❯ vagrant ssh
        [...]
        root@219d34de7386:~# cd data && ghc --make Main && ./Main
        [1 of 1] Compiling Main             ( Main.hs, Main.o )
        Linking Main ...
        "hello, docker and vagrant!"

## How do I use this?

1.  install [virtualbox](https://www.virtualbox.org)

2.  install [vagrant](http://www.vagrantup.com)

3.  check out the repository:

    ```
    ~ ❯❯❯ git clone https://github.com/darinmorrison/vagrant-haskell
    ```

5.  start `vagrant` (optionally overriding cpu/mem resources):

    ```
    ~ ❯❯❯ cd vagrant-haskell
    ~/vagrant-haskell ❯❯❯ vagrant up --provider=docker
    ```

    *   After a few moments the images are downloaded and the VM initialized

#### Halting Vagrant

When using the `docker` provider, `vagrant halt` will stop the `docker` container but not the host. The host is kept running so that multiple containers can be started quickly without spinning up a new VM.

If you want to halt the `docker` host along with the container you will need to get the VM id from `vagrant global-status` and call `vagrant halt <id>`.

The following session illustrates how to accomplish this:

```
~/vagrant-haskell ❯❯❯ vagrant status
Current machine states:

default                   running (docker)

The container is created and running. You can stop it using
`vagrant halt`, see logs with `vagrant docker-logs`, and
kill/destroy it with `vagrant destroy`.

~/vagrant-haskell ❯❯❯ vagrant global-status
id       name    provider   state   directory
-----------------------------------------------------------------------------------------------
cbada68  default virtualbox running ~/vagrant-haskell/.cfg/host
2fe2386  default docker     running ~/vagrant-haskell

The above shows information about all known Vagrant environments
on this machine. This data is cached and may not be completely
up-to-date. To interact with any of the machines, you can go to
that directory and run Vagrant, or you can use the ID directly
with Vagrant commands from any directory. For example:
"vagrant destroy 1a2b3c4d"

~/vagrant-haskell ❯❯❯ vagrant halt
==> default: Stopping container...

~/vagrant-haskell ❯❯❯ vagrant status
Current machine states:

default                   stopped (docker)

The container is created but not running. You can run it again
with `vagrant up`. If the container always goes to "stopped"
right away after being started, it is because the command being
run exits and doesn't keep running.

~/vagrant-haskell ❯❯❯ vagrant global-status
id       name    provider   state   directory
-----------------------------------------------------------------------------------------------
cbada68  default virtualbox running ~/vagrant-haskell/.cfg/host
2fe2386  default docker     stopped ~/vagrant-haskell

The above shows information about all known Vagrant environments
on this machine. This data is cached and may not be completely
up-to-date. To interact with any of the machines, you can go to
that directory and run Vagrant, or you can use the ID directly
with Vagrant commands from any directory. For example:
"vagrant destroy 1a2b3c4d"

~/vagrant-haskell ❯❯❯ vagrant halt cbada68
==> default: Attempting graceful shutdown of VM...
~/vagrant-haskell/Vagrantfile:4: warning: already initialized constant VAGRANTFILE_API_VERSION
~/vagrant-haskell/.cfg/host/Vagrantfile:36: warning: previous definition of VAGRANTFILE_API_VERSION was here

~/vagrant-haskell ❯❯❯ vagrant global-status
id       name    provider   state    directory
------------------------------------------------------------------------------------------------
cbada68  default virtualbox poweroff ~/vagrant-haskell/.cfg/host
2fe2386  default docker     stopped  ~/vagrant-haskell

The above shows information about all known Vagrant environments
on this machine. This data is cached and may not be completely
up-to-date. To interact with any of the machines, you can go to
that directory and run Vagrant, or you can use the ID directly
with Vagrant commands from any directory. For example:
"vagrant destroy 1a2b3c4d"
```

### Customizing the Environment

Some parameters can be modified by setting environment variables:

| environment variable     | default value                   | purpose                                                       |
|--------------------------|:-------------------------------:|---------------------------------------------------------------|
| `VAGRANT_B2D_CPUS`       | total # of logical cores        | number of cores to allocate to vagrant                        |
| `VAGRANT_B2D_CPU_CAP`    | `85` (out of `100`)             | max percentage of cpu capacity to allocate per core           |
| `VAGRANT_B2D_GUI`        | `false`                         | controls whether docker host VM is run headless or not        |
| `VAGRANT_B2D_RAM`        | 1/4th total (MB)                | memory to allocate to vagrant                                 |
| `VAGRANT_B2D_SHARE_NAME` | `data`                          | name of shared folder                                         |
| `VAGRANT_B2D_SHARE_ROOT` | `.`                             | parent of shared folder relative to `vagrant-haskell`         |

Example:

```
~/vagrant-haskell ❯❯❯ VAGRANT_B2D_CPUS='8' VAGRANT_B2D_RAM='8192' vagrant up --provider=docker
```
