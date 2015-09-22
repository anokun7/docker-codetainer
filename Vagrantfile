Vagrant.configure(2) do |config|
  config.vm.box = "ubuntu/trusty64"
  config.vm.hostname = "codetainer1.docker.demo"
  config.vm.network "private_network", ip: "10.10.10.10"

  config.vm.provider "virtualbox" do |vm|
    vm.memory = 1024
    vm.name = "codetainer_vm"
  end

  config.vm.provision "shell", inline: <<-SHELL
    echo root:docker | sudo chpasswd
    sudo curl -sSL https://get.docker.com/ | sh
    sudo cp /vagrant/docker-ubuntu /etc/default/docker
    sudo service docker restart
    sudo usermod -a -G docker vagrant
    docker pull busybox &
    docker pull ubuntu:14.04 &
    docker pull phusion/baseimage &
    docker pull nginx &
    apt-get install -y git telnet strace nc lynx &
    cd /usr/local
    wget https://storage.googleapis.com/golang/go1.5.1.linux-amd64.tar.gz
    tar -xzf go1.5.1.linux-amd64.tar.gz
    export GOROOT=/usr/local/go
    export PATH=$PATH:$GOROOT/bin
    mkdir -p $HOME/work/{bin,src}
    export GOPATH=$HOME/work
    go get github.com/jteeuwen/go-bindata
    cd $GOPATH/src/github.com/jteeuwen/go-bindata/go-bindata && go build
    cp go-bindata /usr/local/go/bin/
    go get github.com/tools/godep
    cd $GOPATH/src/github.com/tools/godep && go build
    cp godep /usr/local/go/bin/
    go get github.com/codetainerapp/codetainer
    cd $GOPATH/src/github.com/codetainerapp/codetainer && make install
    cp bin/codetainer /usr/local/go/bin/
    mkdir $HOME/.codetainer
    cp /vagrant/config.toml $HOME/.codetainer/
    codetainer image register busybox:latest
    codetainer image register ubuntu:14.04
    codetainer create busybox:latest c1
    codetainer create ubuntu:14.04 c2
    codetainer server &
    codetainer list
  SHELL
end
