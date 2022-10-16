# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|
	config.vm.box = "generic/centos8"
	config.vm.box_check_update = true
	config.vm.hostname = "centos8"
	config.vm.synced_folder ".", "/vagrant"
	config.vm.network "forwarded_port", guest: 80, host: 8888, host_ip: "127.0.0.1",
	  auto_correct: false
	config.vm.provider "virtualbox" do |vb|
  		vb.gui = false
  		vb.memory = "2048"
  		vb.cpus = 2
	end

  # Firewall allow 80 & add Repo
  $script = <<-'SCRIPT'
	echo -e "\e[93mAdd nameserver"
	echo "nameserver 8.8.8.8" >> /etc/resolv.conf
	echo -e "\e[93mfirewall-cmd allow 80"
	firewall-cmd --zone=public --add-service=http
	firewall-cmd --permanent --zone=public --add-service=http
	firewall-cmd --add-port=9090/tcp --permanent
	firewall-cmd --add-port=9100/tcp --permanent
	setsebool -P httpd_can_network_connect=1
	setsebool -P httpd_can_network_connect 1
	echo -e "\e[93madd Repo CentOS-Linux"
    sed -i 's/mirrorlist/#mirrorlist/g' /etc/yum.repos.d/CentOS-Linux-*
	sed -i 's|#baseurl=http://mirror.centos.org|baseurl=http://vault.centos.org|g' /etc/yum.repos.d/CentOS-Linux-*
  SCRIPT

  # Install ansible & dependencies & other
  $script2 = <<-'SCRIPT'
    yum -y update
    yum -y install selinux-policy rpm centos-release dnf-plugins-core systemd
    yum -y update
    yum -y config-manager --set-enabled powertools
    yum -y install \
  	epel-release \
  	initscripts \
  	sudo \
  	nano \
  	which \
  	hostname \
  	libyaml-devel \
  	python3 \
  	python3.9 \
  	python3-pip \
  	python3-pyyaml
	echo -e "\e[93mUpgrade pip"
	pip3.9 install --upgrade pip --no-warn-script-location
	echo -e "\e[93mInstall passlib"
	pip3.9 install passlib --no-warn-script-location
	echo -e "\e[93mInstall ansible"
	pip3.9 install ansible --no-warn-script-location
	pip3.9 install jmespath
	#ansible-galaxy install ansible-thoteam.nexus3-oss
  SCRIPT
  
  config.vm.provision :shell, inline: $script, privileged: true
  config.vm.provision :shell, inline: $script2, privileged: true
  config.ssh.insert_key = true
  config.vm.provision "ansible_local" do |ansible|
    ansible.playbook = "/vagrant/playbook.yml"
  end
end