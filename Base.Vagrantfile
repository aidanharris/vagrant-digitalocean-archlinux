# -*- mode: ruby -*-
# vi: set ft=ruby :

DEFAULT_PROCESSOR_COUNT = 4
DEFAULT_RAM_AMOUNT = "8024"
PKG_CACHE = '.pkgcache'

Dir.mkdir(PKG_CACHE) unless File.exists?(PKG_CACHE)

# Used to get the number of CPU cores - Credit: https://stackoverflow.com/a/6250054
module System
  extend self
  def cpu_count
    return Java::Java.lang.Runtime.getRuntime.availableProcessors if defined? Java::Java
    return File.read('/proc/cpuinfo').scan(/^processor\s*:/).size if File.exist? '/proc/cpuinfo'
    return DEFAULT_PROCESSOR_COUNT
  end
end

# All Vagrant configuration is done below. The "2" in Vagrant.configure
# configures the configuration version (we support older styles for
# backwards compatibility). Please don't change it unless you know what
# you're doing.
Vagrant.configure('2') do |config|
  # The most common configuration options are documented and commented below.
  # For a complete reference, please see the online documentation at
  # https://docs.vagrantup.com.

  # Every Vagrant development environment requires a box. You can search for
  # boxes at https://atlas.hashicorp.com/search.
  config.vm.box = 'terrywang/archlinux'

  # Disable automatic box update checking. If you disable this, then
  # boxes will only be checked for updates when the user runs
  # `vagrant box outdated`. This is not recommended.
  # config.vm.box_check_update = false

  # Create a forwarded port mapping which allows access to a specific port
  # within the machine from a port on the host machine. In the example below,
  # accessing "localhost:8080" will access port 80 on the guest machine.
  # config.vm.network "forwarded_port", guest: 80, host: 8080

  # Create a private network, which allows host-only access to the machine
  # using a specific IP.
  #config.vm.network "private_network", ip: "192.168.33.10"

  # Create a public network, which generally matched to bridged network.
  # Bridged networks make the machine appear as another physical device on
  # your network.
  #config.vm.network "public_network", type: "dhcp"
  config.vm.network "private_network", type: "dhcp"

  config.vm.hostname = "aidanharris-archlinux"

  # Share an additional folder to the guest VM. The first argument is
  # the path on the host to the actual folder. The second argument is
  # the path on the guest to mount the folder. And the optional third
  # argument is a set of non-required options.
  config.vm.synced_folder ".", "/vagrant", disabled: true

  # Instead of downloading the VirtualBox Guest additions from the Internet a
  # local file path can be specified instead. Uses the vagrant-vbguest plugin
  # https://github.com/dotless-de/vagrant-vbguest
  #config.vbguest.iso_path = "/usr/share/virtualbox/VBoxGuestAdditions.iso"

  # Set this to false if you have not downloaded the virtualbox guest additions
  # to the appropriate location (See: https://github.com/dotless-de/vagrant-vbguest#iso-autodetection)
  #config.vbguest.no_remote = true

  # Provider-specific configuration so you can fine-tune various
  # backing providers for Vagrant. These expose provider-specific options.
  # Example for VirtualBox:
  #
  config.vm.provider "virtualbox" do |vb|
    # Display the VirtualBox GUI when booting the machine
    vb.gui = false
    # Number of CPU cores / 2 e.g a system with 8 cores will allocate 4
    # of those cores for the VM.
    vb.cpus = "#{((System.cpu_count/2)<=0) ? 1 : System.cpu_count / 2}"
    # Customize the amount of memory on the VM:
    vb.memory = DEFAULT_RAM_AMOUNT
  end

  # Deploy to DigitalOcean (https://github.com/devopsgroup-io/vagrant-digitalocean)
  config.vm.provider :digital_ocean do |digitalocean, override|
    override.nfs.functional = false # Let vagrant know NFS is disabled to force it to use rsync instead
    override.ssh.private_key_path = '~/.ssh/id_rsa'
    override.vm.box = 'digtal_ocean'
    override.vm.box_url = 'https://github.com/devopsgroup-io/vagrant-digitalocean/raw/master/box/digital_ocean.box'

    # Put the api token in the 'DIGITALOCEAN_TOKEN' environment variable or replace the string
    # below with a string containing the API token to use.
    digitalocean.token = "#{ENV['DIGITALOCEAN_TOKEN']}"
    digitalocean.image = 'debian-8-x64'
    digitalocean.region = 'lon1'
    digitalocean.size = '1gb'

    # Ensure we don't override any existing droplets by concatonating the current time onto the end of the hostname
    # After testing the hostname should probably be set to something more permanent...
    override.vm.hostname = "aidanharris-archlinux-#{Time.now.to_i}"
  end

  #
  # View the documentation for the provider you are using for more
  # information on available options.

  # Define a Vagrant Push strategy for pushing to Atlas. Other push strategies
  # such as FTP and Heroku are also available. See the documentation at
  # https://docs.vagrantup.com/v2/push/atlas.html for more information.
  # config.push.define "atlas" do |push|
  #   push.app = "YOUR_ATLAS_USERNAME/YOUR_APPLICATION_NAME"
  # end

  # Enable provisioning with a shell script. Additional provisioners such as
  # Puppet, Chef, Ansible, Salt, and Docker are also available. Please see the
  # documentation for more information about their specific syntax and use.

  # Use my CentOS Mirror if available since this is infinitely faster in my LAN
  config.vm.provision "shell", path: "digitalocean-debian-to-arch/install.sh"
end
