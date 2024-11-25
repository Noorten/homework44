# -*- mode: ruby -*-
# vim: set ft=ruby :

MACHINES = {
  :master => {
        :box_name => "centos/7",
        :ip_addr => '192.168.50.10'
  },
  :slave => {
        :box_name => "centos/7",
        :ip_addr => '192.168.50.20'
  }
}

Vagrant.configure("2") do |config|
  MACHINES.each do |boxname, boxconfig|
      config.vm.define boxname do |box|
          box.vm.box = boxconfig[:box_name]
          box.vm.host_name = boxname.to_s

          box.vm.network "private_network", ip: boxconfig[:ip_addr]

          box.vm.provider :virtualbox do |vb|
            vb.name = boxname.to_s
            vb.customize ["modifyvm", :id, "--memory", "1024"]
          end

          box.vm.provision "shell", inline: <<-SHELL
            mkdir -p ~root/.ssh
            cp ~vagrant/.ssh/auth* ~root/.ssh
            sed -i.bak 's/mirrorlist/#mirrorlist/g' /etc/yum.repos.d/CentOS-*
            sed -i.bak 's|#baseurl=http://mirror.centos.org|baseurl=http://vault.centos.org|g' /etc/yum.repos.d/CentOS-*
            sed -i 's/^#PasswordAuthentication.*$/PasswordAuthentication yes/' /etc/ssh/sshd_config
            sed -i 's/PasswordAuthentication no/PasswordAuthentication yes/g' /etc/ssh/sshd_config
            systemctl restart sshd
          SHELL

          box.vm.provision "ansible" do |ansible|
            ansible.compatibility_mode = "2.0"
            #ansible.verbose = "vvv"
            ansible.playbook = "playbook.yml"
            ansible.become = "true"
          end


          # box.vm.provision "shell", inline: <<-SHELL
          #   mkdir -p ~root/.ssh
          #   cp ~vagrant/.ssh/auth* ~root/.ssh
          #   timedatectl set-timezone Europe/Moscow
          #   sed -i 's/PasswordAuthentication no/PasswordAuthentication yes/g' /etc/ssh/sshd_config
          #   systemctl restart sshd

          #   # Install Percona Repo & Server
          #   yum install -y https://repo.percona.com/yum/percona-release-latest.noarch.rpm epel-release 
          #   yum install -y Percona-Server-server-57

          #   cp /vagrant/conf/conf.d/* /etc/my.cnf.d/ 
          #   systemctl start mysql

          #   # Generate MySQL new root password
          #   mysql_root_pass=$(cat /var/log/mysqld.log | grep 'root@localhost:' | awk '{print $11}')
          #   mysql -uroot -p$mysql_root_pass -e "ALTER USER USER() IDENTIFIED BY 'Otus#Linux2021';" --connect-expired-password
          #   # Create .my.cf file
          #   echo -e "[client]\n\ruser=\'root\'\n\rpassword=\'Otus#Linux2021\'" > /root/.my.cnf

          #   SHELL

          # case boxname.to_s  

          # when "master"
          # box.vm.provision "shell", run: "always", inline: <<-SHELL 
          #   # Create database and user for replication
          #   mysql --defaults-file=/root/.my.cnf -e "CREATE DATABASE bet;"
          #   mysql --defaults-file=/root/.my.cnf -D bet < /vagrant/conf/bet.dmp;
          #   mysql --defaults-file=/root/.my.cnf  -e  "CREATE USER 'repl'@'%' IDENTIFIED BY 'Otus#Linux2021';" 
          #   mysql --defaults-file=/root/.my.cnf  -e  "GRANT REPLICATION SLAVE ON *.* TO 'repl'@'%' IDENTIFIED BY 'Otus#Linux2021';"
            
          #   # Dump databases
          #   mysqldump --defaults-file=/root/.my.cnf --all-databases --triggers --routines --master-data --ignore-table=bet.events_on_demand --ignore-table=bet.v_same_event  > /root/master.sql
          # SHELL

          # when "slave"
          # box.vm.provision "shell", run: "always", inline: <<-SHELL
          #   # Copy Database dump to Slave 
          #   yum install -y sshpass  
          #   sshpass -p "vagrant" scp -o StrictHostKeyChecking=no -o UserKnownHostsFile=/dev/null vagrant@192.168.50.10:/root/master.sql /root

          #   # Config Slave for replication
          #   sed -i 's/server-id = 1/server-id = 2/g' /etc/my.cnf.d/01-base.cnf
          #   sed -i 's/#replicate-ignore-table=bet.events_on_demand/replicate-ignore-table=bet.events_on_demand/g' /etc/my.cnf.d/05-binlog.cnf
          #   sed -i 's/#replicate-ignore-table=bet.v_same_event/replicate-ignore-table=bet.v_same_event/g' /etc/my.cnf.d/05-binlog.cnf  

          #   systemctl restart mysql        

          #   # Create Database and import 
          #   mysql --defaults-file=/root/.my.cnf -e "CREATE DATABASE bet;"
          #   mysql --defaults-file=/root/.my.cnf -e "reset master;"
          #   mysql --defaults-file=/root/.my.cnf -D bet < /root/master.sql
            
          #   # Start slave  
          #   mysql --defaults-file=/root/.my.cnf -e "CHANGE MASTER TO MASTER_HOST = '192.168.50.10', MASTER_PORT = 3306, MASTER_USER = 'repl', MASTER_PASSWORD = 'Otus#Linux2021', MASTER_AUTO_POSITION = 1;"
          #   mysql --defaults-file=/root/.my.cnf -e "START SLAVE;"

          # SHELL
   
          # end
      end
  end
end
