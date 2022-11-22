# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure(2) do |config|
  config.vm.box = "centos/7"

  config.vm.provider "virtualbox" do |v|
    v.memory = 512
    v.cpus = 1
  end

  config.vm.define "nfss" do |nfss|
    nfss.vm.network "private_network", ip: "192.168.56.10", virtualbox__intnet: "net1"
    nfss.vm.hostname = "nfss"

 	  nfss.vm.provision "shell", inline: <<-SHELL
	      yum install -y nfs-utils
		systemctl enable firewalld --now
		firewall-cmd --add-service="nfs3" --add-service="rpc-bind" --add-service="mountd" --permanent
		firewall-cmd --reload
		systemctl enable nfs --now
		mkdir -p /srv/share/upload
		chown -R nfsnobody:nfsnobody /srv/share
		chmod 0777 /srv/share/upload
		echo "/srv/share 192.168.56.11/32(rw,sync,root_squash)" > /etc/exports
		exportfs -r
  	  SHELL


#    nfss.vm.provision "shell", path: "nfss_script.sh"
  end

  config.vm.define "nfsc" do |nfsc|
    nfsc.vm.network "private_network", ip: "192.168.56.11", virtualbox__intnet: "net1"
    nfsc.vm.hostname = "nfsc"

 	  nfsc.vm.provision "shell", inline: <<-SHELL
	      yum install -y nfs-utils
		systemctl enable firewalld --now
		echo "192.168.56.10:/srv/share/ /mnt nfs vers=3,proto=udp,noauto,x-systemd.automount 0 0" >> /etc/fstab
		systemctl daemon-reload
		systemctl restart remote-fs.target
  	  SHELL

#    nfsc.vm.provision "shell", path: "nfsc_script.sh"
  end

end

