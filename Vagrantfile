Vagrant.configure("2") do |config|
  ## common settings for all machines
  config.vm.box = "centos/7"
  config.vm.provider "virtualbox" do |v|
    v.memory = 1024
    v.cpus = 1
  end
  config.vm.provision "shell", inline: <<-SHELL
  systemctl enable firewalld
  systemctl start firewalld
  yum -y install wget java java-1.8.0-openjdk-devel.x86_64 git
  SHELL
  ## machine specific settings
  # Virtual machine 1
  config.vm.define "vm1" do |vm1|
    vm1.vm.hostname = "jenkins-host"
    vm1.ssh.port = 9122
    vm1.ssh.guest_port = 22
    vm1.vm.network "private_network", ip: "192.168.88.11"
    vm1.vm.network "forwarded_port", guest: 22, host: 9122, id: 'ssh'
    vm1.vm.provision "shell", inline: <<-SHELL
      wget -P /tmp https://pkg.jenkins.io/redhat-stable/jenkins-2.150.1-1.1.noarch.rpm
      wget -P /tmp http://apache.tt.co.kr/maven/maven-3/3.6.0/binaries/apache-maven-3.6.0-bin.tar.gz
      tar -xvf /tmp/apache-maven-3.6.0-bin.tar.gz -C /usr/local
      ln -s /usr/local/apache-maven-3.6.0/bin/mvn /usr/bin/mvn
      rpm -ivh /tmp/jenkins-2.150.1-1.1.noarch.rpm
      systemctl start jenkins
      systemctl enable jenkins
      firewall-cmd --permanent --add-port=8080/tcp --zone=public
      firewall-cmd --reload
    SHELL
    vm1.vm.provision "shell",  privileged: false, inline: <<-SHELL
      echo "ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQCoK2IhBjQDpXyqk3tqJtGwBzMQpkVS5AMHc+suoq8M3DsUB6E+2ltC+WPArhKjc5hrptL3B8CPgO5BvBPlnk9alOpaGdKeNB3hX1guHlBOIHshjERhwe9UyyKNrtfppA9cKSGw9nEZropPwq8Zr/rpoxqDgifk+ctejvKBN4nPPjQVyltNvl/bz3yAXq+ONIkDwNXzao0Pj0kX1V0L7uJGrOODmPG2n4Nbk7LhNcaTLsy3fgdkQpRzmdBfHOn99WXwWV2CMLREym5Yh09SKgUhqpS4S3BmGJqjaNitnFCN809fV9P+VTz4UwqRbgUQ6mfEz4w0Zt3ZCGAI7g9vjC3L vagrant" >> ~/.ssh/authorized_keys
    SHELL
  end
  # Virtual machine 2
  config.vm.define "vm2" do |vm2|
    vm2.vm.hostname = "jenkins-target"
    vm2.ssh.port = 9222
    vm2.ssh.guest_port = 22
    vm2.vm.network "private_network", ip: "192.168.88.12"
    vm2.vm.network "forwarded_port", guest: 22, host: 9222, id: 'ssh'
    vm2.vm.provision "shell",  inline: <<-SHELL
      firewall-cmd --permanent --add-port=9909/tcp --zone=public
      firewall-cmd --reload
    SHELL
    vm2.vm.provision "shell",  privileged: false, inline: <<-SHELL
      echo "ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQCoK2IhBjQDpXyqk3tqJtGwBzMQpkVS5AMHc+suoq8M3DsUB6E+2ltC+WPArhKjc5hrptL3B8CPgO5BvBPlnk9alOpaGdKeNB3hX1guHlBOIHshjERhwe9UyyKNrtfppA9cKSGw9nEZropPwq8Zr/rpoxqDgifk+ctejvKBN4nPPjQVyltNvl/bz3yAXq+ONIkDwNXzao0Pj0kX1V0L7uJGrOODmPG2n4Nbk7LhNcaTLsy3fgdkQpRzmdBfHOn99WXwWV2CMLREym5Yh09SKgUhqpS4S3BmGJqjaNitnFCN809fV9P+VTz4UwqRbgUQ6mfEz4w0Zt3ZCGAI7g9vjC3L vagrant" >> ~/.ssh/authorized_keys
    SHELL
  end
end
