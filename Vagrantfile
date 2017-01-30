# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure(2) do |config|
  config.vm.box = "bento/ubuntu-16.04"

  config.vm.define "server" do |c|
    c.vm.hostname = "server.example.com"
    c.vm.network "private_network", ip: "192.168.40.10"

    c.vm.provider "virtualbox" do |vb|
      vb.cpus = 2
      vb.memory = "4096"
    end

    c.vm.provision "shell", inline: <<-SHELL
      wget -q https://sensu.global.ssl.fastly.net/apt/pubkey.gpg -O- | sudo apt-key add -
      echo "deb     https://sensu.global.ssl.fastly.net/apt sensu main" | sudo tee /etc/apt/sources.list.d/sensu.list

      sudo apt-get update

      sudo apt-get install -y redis-server

      sudo cp /vagrant/etc/redis/redis.conf /etc/redis/redis.conf
      sudo systemctl restart redis-server

      sudo apt-get install -u sensu
      sudo cp /vagrant/etc/sensu/conf.d/api.json /etc/sensu/conf.d/api.json
      sudo cp /vagrant/etc/sensu/conf.d/redis.json /etc/sensu/conf.d/redis.json
      sudo cp /vagrant/etc/sensu/conf.d/transport.json /etc/sensu/conf.d/transport.json
      sudo cp /vagrant/etc/sensu/conf.d/client.json /etc/sensu/conf.d/client.json

      sudo systemctl enable sensu-server
      sudo systemctl restart sensu-server
      sudo systemctl enable sensu-api
      sudo systemctl restart sensu-api
      sudo systemctl enable sensu-client
      sudo systemctl restart sensu-client

      sudo apt-get install uchiwa
      sudo cp /vagrant/etc/sensu/uchiwa.json /etc/sensu/uchiwa.json
      sudo systemctl enable uchiwa
      sudo systemctl restart uchiwa
    SHELL
  end


  (1..2).each do |i|
    config.vm.define "client#{i}" do |c|
      c.vm.hostname = "client#{i}.example.com"
      c.vm.network "private_network", ip: "192.168.40.1#{i}"

      c.vm.provider "virtualbox" do |vb|
        vb.cpus = 2
        vb.memory = "2048"
      end

      c.vm.provision "shell", inline: <<-SHELL
        wget -q https://sensu.global.ssl.fastly.net/apt/pubkey.gpg -O- | sudo apt-key add -
        echo "deb     https://sensu.global.ssl.fastly.net/apt sensu main" | sudo tee /etc/apt/sources.list.d/sensu.list

        sudo apt-get update

        sudo apt-get install -u sensu
        sudo cp /vagrant/etc/sensu/conf.d/redis.json /etc/sensu/conf.d/redis.json
        sudo cp /vagrant/etc/sensu/conf.d/transport.json /etc/sensu/conf.d/transport.json
        sudo cp /vagrant/etc/sensu/conf.d/client.json /etc/sensu/conf.d/client.json

        sudo systemctl enable sensu-client
        sudo systemctl restart sensu-client
      SHELL
    end
  end
end
