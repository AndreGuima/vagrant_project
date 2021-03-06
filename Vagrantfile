# $script_mysql = <<-SCRIPT
#   apt-get update && \
#   apt-get install -y mysql-server-5.7 && \
#   mysql -e "create user 'phpuser'@'%' identified by 'pass';"
#   service mysql restart
# SCRIPT

Vagrant.configure("2") do |config|
  config.vm.box = "ubuntu/bionic64"

  # config.vm.define "mysqldb" do |mysql|
  #   mysql.vm.network "public_network", ip: "192.168.15.12"
  #
  #   mysql.vm.synced_folder "./configs", "/configs"
  #   mysql.vm.synced_folder ".", "/vagrant", disabled: true
  #
  #   mysql.vm.provision "shell", inline: "cat /configs/private_public_keys.pub >> .ssh/authorized_keys"
  #   mysql.vm.provision "shell", inline: "cat /configs/mysqld.cnf > /etc/mysql/mysql.conf.d/mysqld.cnf"
  #   mysql.vm.provision "shell", inline: $script_mysql
  # end

  config.vm.define "phpweb" do |php|
    php.vm.network "public_network", ip: "192.168.15.13"
    php.vm.network "forwarded_port", guest: 8888, host: 8888

    php.vm.provision "shell",
      inline: "apt-get update && apt-get install -y puppet"

    php.vm.provision "puppet" do |puppet|
      puppet.manifests_path = "./configs/manifests"
      puppet.manifest_file = "phpweb.pp"
    end
  end

  config.vm.define "mysqlserver" do |mysqlserver|
    mysqlserver.vm.network "public_network", ip: "192.168.15.14"

    mysqlserver.vm.provision "shell",
      inline: "cat /vagrant/configs/id_bionic.pub >> .ssh/authorized_keys"
  end

  config.vm.define "ansible" do |ansible|
    ansible.vm.network "public_network", ip: "192.168.15.15"

    ansible.vm.provision "shell",
      inline: "cp /vagrant/id_bionic /home/vagrant && \
               chmod 600 /home/vagrant/id_bionic && \
               chown vagrant:vagrant /home/vagrant/id_bionic"

    ansible.vm.provision "shell",
      inline: "apt update && \
               apt install -y software-properties-common && \
               apt-add-repository --yes --update ppa:ansible/ansible && \
               apt install -y ansible"

    ansible.vm.provision "shell",
      inline: "ansible-playbook -i /vagrant/configs/ansibleplaybook/hosts \
              /vagrant/configs/ansibleplaybook/playbook.yml"
  end
end
