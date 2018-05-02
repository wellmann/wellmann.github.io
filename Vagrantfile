# -*- mode: ruby -*-
# vi: set ft=ruby :

# Plugin checks
unless Vagrant.has_plugin?("vagrant-hostmanager")
  raise 'vagrant-hostmanager plugin required!'
end

unless Vagrant.has_plugin?("vagrant-triggers")
  raise 'vagrant-triggers plugin required!'
end

# Vagrant config
Vagrant.configure("2") do |config|

    config.vm.box = "ubuntu/trusty64"
    config.ssh.username = "vagrant"
    config.ssh.password = "vagrant"
    config.vm.network :private_network, ip: "192.168.11.10"
    config.vm.hostname = 'kevin.wellmann.test'

    config.hostmanager.enabled = true
    config.hostmanager.manage_host = true

    config.vm.provision "shell", inline: <<-SHELL
      #update packages
      apt-get -y update

      #add repository for ruby2
      apt-get -y install python-software-properties
      apt-add-repository -y ppa:brightbox/ruby-ng

      #get 2.1 from newly added repository
      apt-get -y update

      #install ruby with build tools so we can use gems
      apt-get -y install build-essential
      apt-get -y install ruby2.1
      apt-get -y install ruby2.1-dev

      #install github pages requirements (jekyll)
      sudo gem update --system
      sudo gem install github-pages

      apt-get -y install nodejs
    SHELL

    config.trigger.after [:up] do
        run_remote "iptables -t nat -A PREROUTING -p tcp --dport 80 -j REDIRECT --to-port 4000"
        run_remote  "cd /vagrant && jekyll serve --watch --force_polling --host=0.0.0.0"
    end

    config.trigger.before [:suspend, :halt] do
        run_remote  "killall -9 jekyll"
    end

end
