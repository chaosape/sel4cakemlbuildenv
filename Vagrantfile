# -*- mode: ruby -*-
# vi: set ft=ruby :

# All Vagrant configuration is done below. The "2" in Vagrant.configure
# configures the configuration version (we support older styles for
# backwards compatibility). Please don't change it unless you know what
# you're doing.
Vagrant.configure("2") do |config|
  # The most common configuration options are documented and commented below.
  # For a complete reference, please see the online documentation at
  # https://docs.vagrantup.com.

  # Every Vagrant development environment requires a box. You can search for
  # boxes at https://vagrantcloud.com/search.
  config.vm.box = "ubuntu/bionic64"

  # Forward ssh key requests to our local agent.
  config.ssh.forward_agent = true

  # Disable automatic box update checking. If you disable this, then
  # boxes will only be checked for updates when the user runs
  # `vagrant box outdated`. This is not recommended.
  # config.vm.box_check_update = false

  # Create a forwarded port mapping which allows access to a specific port
  # within the machine from a port on the host machine. In the example below,
  # accessing "localhost:8080" will access port 80 on the guest machine.
  # NOTE: This will enable public access to the opened port
  # config.vm.network "forwarded_port", guest: 80, host: 8080

  # Create a forwarded port mapping which allows access to a specific port
  # within the machine from a port on the host machine and only allow access
  # via 127.0.0.1 to disable public access
  # config.vm.network "forwarded_port", guest: 80, host: 8080, host_ip: "127.0.0.1"

  # Create a private network, which allows host-only access to the machine
  # using a specific IP.
  # config.vm.network "private_network", ip: "192.168.33.10"

  # Create a public network, which generally matched to bridged network.
  # Bridged networks make the machine appear as another physical device on
  # your network.
  # config.vm.network "public_network"

  # Share an additional folder to the guest VM. The first argument is
  # the path on the host to the actual folder. The second argument is
  # the path on the guest to mount the folder. And the optional third
  # argument is a set of non-required options.
  # config.vm.synced_folder "../data", "/vagrant_data"

  # Provider-specific configuration so you can fine-tune various
  # backing providers for Vagrant. These expose provider-specific options.
  # Example for VirtualBox:
  #
  config.vm.provider "virtualbox" do |vb|
    # Display the VirtualBox GUI when booting the machine
    vb.gui = false

    # Customize the amount of memory on the VM:
    vb.memory = "16384"
  end
  #
  # View the documentation for the provider you are using for more
  # information on available options.

  # Enable provisioning with a shell script. Additional provisioners such as
  # Puppet, Chef, Ansible, Salt, and Docker are also available. Please see the
  # documentation for more information about their specific syntax and use.
  config.vm.provision "shell", inline: <<-SHELL
    apt-get update
    pkgs=(
      repo
      curl
      git
      wget
      polyml
      libpolyml-dev
      build-essential
      ninja-build
      python-six
      python-pip
      python-enum34
      python-pyelftools
      python-jinja2
      python-dev
      python3-dev
      python3-pip
      libxml2-utils
      ncurses-dev
      emacs-nox
      cmake
      ccache
      doxygen
      gcc-arm-linux-gnueabi
      g++-arm-linux-gnueabi
      gcc-aarch64-linux-gnu
      g++-aarch64-linux-gnu
      qemu-system-arm
      qemu-system-x86
      clang
      gdb
      libssl-dev
      libclang-dev
      libcunit1-dev
      libsqlite3-dev
      libwww-perl
      libxml2-dev
      libxslt-dev
      mlton
      qemu-kvm
      texlive-fonts-recommended
      texlive-latex-extra
      texlive-metapost
      texlive-bibtex-extra)
      for i in "${pkgs[@]}"
      do
        apt-get install $i -y
      done
      update-locale LANG=en_US.UTF-8
      curl -sSL https://get.haskellstack.org/ | sh
   SHELL

  config.vm.provision "shell", privileged: false, inline: <<-SHELL
    ssh -T git@github.com -o StrictHostKeyChecking=no
    pip install --user setuptools
    pip install --user sel4-deps
    pip install --user camkes-deps
    ssh -T git@github.com -o StrictHostKeyChecking=no
    mkdir repos
    git clone git@github.com:HOL-Theorem-Prover/HOL.git repos/HOL
    (cd repos/HOL && git checkout 5486751385fa6b1f69feee739547ec7e4ef99d7f)
    git clone git@github.com:CakeML/cakeml.git repos/CakeML
    (cd repos/CakeML && git checkout eade0f2f7ccfe17bb7042cdc67f8379ff7edd270)
    mkdir tarballs
    (cd tarballs && wget --quiet https://cakeml.org/cake-x64-64.tar.gz)
    mkdir dev
    (cd dev && tar -xvzf ../tarballs/cake-x64-64.tar.gz && cd cake-x64-64 && make)
    mkdir repos/camkes-cakeml
    (cd repos/camkes-cakeml && yes | repo init -u ssh://git@github.com/ikuz/camkes-cakeml.git && repo sync)
    (cd repos/HOL && echo 'val polymllibdir = "/usr/lib/x86_64-linux-gnu";' > tools-poly/poly-includes.ML)
    (cd repos/HOL && poly < tools/smart-configure.sml && bin/build)
    echo '' >> ~/.bashrc
    echo 'export PATH=${PATH}:${PWD}/dev/cake-x64-64/:${PWD}/repos/HOL/bin:' >> ~/.bashrc
    echo 'export LC_ALL=en_US.UTF-8' >> ~/.bashrc
  SHELL
end
