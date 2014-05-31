# vagrant-haskell: haskell in a box

`vagrant-haskell` is a minimal tinycore-based docker host running [darinmorrison/docker-haskell](https://github.com/darinmorrison/docker-haskell).

## Why Vagrant?

`vagrant` provides a more convenient interface for interacting with `docker-haskell` as a portable Haskell development environment than using `docker` or `boot2docker` directly.

*   **A familiar VM-style workflow**

        ~ ❯❯❯ vagrant up --provider=docker    # initialize vm, download image, launch container

*   **Convenient SSH access**

        ~ ❯❯❯ vagrant ssh
        ==> default: SSH will be proxied through the Docker virtual machine since we're
        ==> default: not running Docker natively. This is just a notice, and not an error.
        Warning: Permanently added '172.17.0.2' (ECDSA) to the list of known hosts.
        Last login: Sat May 31 00:01:06 2014 from 172.17.42.1
        root@219d34de7386:~# exit
        logout
        Connection to 172.17.0.2 closed.
        Connection to 127.0.0.1 closed.
        ~ ❯❯❯

*   **NFS-based shared folders**

        ~ ❯❯❯ echo 'main = print "hello, docker and vagrant!"' > data/Main.hs
        ~ ❯❯❯ vagrant ssh
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
    ~ ❯❯❯ cd ~/Desktop
    ~ ❯❯❯ git clone https://github.com/darinmorrison/vagrant-haskell
    ```

5.  start `vagrant` (optionally overriding cpu/mem resources):

    ```
    ~ ❯❯❯ cd vagrant-haskell
    ~ ❯❯❯ vagrant up --provider=docker
    ```

    *   After a few moments the images are downloaded and the VM initialized

### Customizing the Environment

Some parameters can be modified by setting environment variables:

| environment variable  | default value               | purpose                                             |
|-----------------------|:---------------------------:|-----------------------------------------------------|
| `VAGRANT_SCA_CPUS`    | total # of logical cores    | number of cores to allocate to vagrant              |
| `VAGRANT_SCA_CPU_CAP` | `85` (out of `100`)         | max percentage of cpu capacity to allocate per core |
| `VAGRANT_SCA_RAM`     | 1/4th total (MB)            | memory to allocate to vagrant                       |

Example:

```
~ ❯❯❯ VAGRANT_SCA_CPUS='8' VAGRANT_SCA_RAM='8192' vagrant up --provider=docker
```
