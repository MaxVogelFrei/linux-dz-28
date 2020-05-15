# -*- mode: ruby -*-
# vim: set ft=ruby :

MACHINES = {
  :consul => {
        :box_name => "centos/7",
        :forward => {:guest => 8500, :host => 18500},
        :net => [  
                   {adapter: 2, ip: '192.168.11.100', netmask: "255.255.255.0"}
                ]
  },
  :pg1 => {
        :box_name => "centos/7",
        :forward => {:guest => 5432, :host => 15432},
        :net => [  
                   {adapter: 2, ip: '192.168.11.120', netmask: "255.255.255.0"}
                ]
  },
  :pg2 => {
        :box_name => "centos/7",
        :forward => {:guest => 5432, :host => 25432},
        :net => [  
                   {adapter: 2, ip: '192.168.11.121', netmask: "255.255.255.0"}
                ]
  },
  :pg3 => {
        :box_name => "centos/7",
        :forward => {:guest => 5432, :host => 35432},
        :net => [  
                   {adapter: 2, ip: '192.168.11.122', netmask: "255.255.255.0"}
                ]
  },
  :haproxy => {
        :box_name => "centos/7",
        :forward => {:guest => 7000, :host => 17000},
        :net => [
                   {adapter: 2, ip: '192.168.11.101', netmask: "255.255.255.0"}
                ]
  },
  :provision => {
        :box_name => "centos/7",
        :forward => {:guest => 22, :host => 11022},
        :net => [
                   {adapter: 2, ip: '192.168.11.102', netmask: "255.255.255.0"}
                ]
  }


}

Vagrant.configure("2") do |config|

    MACHINES.each do |boxname, boxconfig|

        config.vm.define boxname do |box|

          box.vm.box = boxconfig[:box_name]
          box.vm.host_name = boxname.to_s
          box.vm.network "forwarded_port", boxconfig[:forward]
          boxconfig[:net].each do |ipconf|
            box.vm.network "private_network", ipconf
          end

          box.vm.provider :virtualbox do |vb|
            vb.customize ["modifyvm", :id, "--memory", "512"]
            # Подключаем дополнительные диски
            #vb.customize ['createhd', '--filename', second_disk, '--format', 'VDI', '--size', 5 * 1024]
            #vb.customize ['storageattach', :id, '--storagectl', 'IDE', '--port', 0, '--device', 1, '--type', 'hdd', '--medium', second_disk]
          end

      #     box.vm.provision "shell", inline: <<-SHELL
      #       mkdir -p ~root/.ssh; cp ~vagrant/.ssh/auth* ~root/.ssh
      #       sed -i '65s/PasswordAuthentication no/PasswordAuthentication yes/g' /etc/ssh/sshd_config
      #       systemctl restart sshd
      #       apt update -y && apt install -y \
      #       vim \
      #       python \
	# 	telnet
      #     SHELL

          case boxname.to_s
          when "provision"
              box.vm.provision "shell", run: "always", inline: <<-SHELL
                yum install epel-release -y
                yum install ansible -y
                cp -r /vagrant/* /root/
                cd /root
                ansible-playbook site.yml
                SHELL
          end
      end
  end
end
