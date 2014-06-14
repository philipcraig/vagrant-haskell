# -*- mode: ruby -*-
# vi: set ft=ruby :

VAGRANTFILE_API_VERSION      = '2'
Vagrant.require_version    '>=  1.6.3'

Vagrant.configure( VAGRANTFILE_API_VERSION ) do |cfg|

  ## needed to avoid 'No Host IP' error
  cfg.vm.synced_folder '.', '/vagrant', disabled: true

  cfg.vm.provider 'docker' do |dkr, ovr|

    ## customize the docker host
    dkr.vagrant_vagrantfile  = '.cfg/host/Vagrantfile'

    ## use the haskell docker image
    dkr.image                = 'darinmorrison/haskell:0.1.0'

    ## configure ssh according to phusion/baseimage
    ovr.ssh.username         = 'root'
    ovr.ssh.private_key_path = '.cfg/insecure_key'

    ## start the container
    dkr.cmd                  = ['/sbin/my_init', '--enable-insecure-key']
    dkr.name                 = 'container-haskell-docker'
    dkr.volumes              = ['/vagrant/data:/root/data']

  end

end
