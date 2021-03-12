$script_mysql = <<-SCRIPT
  apt-get update && \
  apt-get install -y mysql-server-5.7 && \
  mysql -e "create user 'phpuser'@'%' identified by 'pass';"
  cat /configs/mysqld.cnf > /etc/mysql/mysql.conf.d/mysqld.cnf
  service mysql restart
SCRIPT

$script_puppet = <<-SCRIPT
  apt-get update && \
  apt-get install -y puppet
SCRIPT

Vagrant.configure("2") do |config|

  config.vm.box = "ubuntu/bionic64"

  config.vm.define "mysqldb" do |mysql|
    mysql.vm.network "public_network", ip: "192.168.15.12"

    mysql.vm.synced_folder "./configs", "/configs"
    mysql.vm.synced_folder ".", "/vagrant", disabled: true

    mysql.vm.provision "shell", inline: "cat /configs/private_public_keys.pub >> .ssh/authorized_keys"
    mysql.vm.provision "shell", inline: $script_mysql
  end

  config.vm.define "phpweb" do |php|
    php.vm.network "public_network", ip: "192.168.15.13"
    php.vm.network "forwarded_port", guest: 8888, host: 8888

    php.vm.provision "shell", inline: $script_puppet

    php.vm.provision "puppet" do |puppet|
      puppet.manifests_path = "./configs/manifests"
      puppet.manifest_file = "phpweb.pp"
    end
  end
end
