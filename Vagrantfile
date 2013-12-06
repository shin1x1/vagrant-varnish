# -*- mode: ruby -*-
# vi: set ft=ruby :

# Vagrantfile API/syntax version. Don't touch unless you know what you're doing!
VAGRANTFILE_API_VERSION = "2"

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
  config.vm.box = "centos64_ja"
  config.vm.box_url = "https://dl.dropboxusercontent.com/u/3657281/centos64_ja.box"
  config.vm.hostname = "varnish-monit"

  config.vm.network :private_network, ip: "192.168.33.50"

  config.vm.provider :virtualbox do |vb|
    vb.name = config.vm.hostname
    vb.customize ["modifyvm", :id, "--natdnshostresolver1", "on"]
    vb.customize ["modifyvm", :id, "--natdnsproxy1", "on"]
  end

  config.vm.provision :shell, :inline => <<-EOT
    #
    # iptables off
    #
    /sbin/iptables -F
    /sbin/service iptables stop
    /sbin/chkconfig iptables off
    #
    # yum repository
    #
    rpm -ivh http://ftp.riken.jp/Linux/fedora/epel/6/i386/epel-release-6-8.noarch.rpm
    #yum -y update
    #
    # ntp
    #
    yum -y install ntp
    /sbin/service ntpd start
    /sbin/chkconfig ntpd on
    #
    # Varnish
    #
    rpm --nosignature -ivh http://repo.varnish-cache.org/redhat/varnish-3.0/el6/noarch/varnish-release/varnish-release-3.0-1.el6.noarch.rpm
    yum -y install varnish
    #
    # monit
    #
    yum -y install monit
    chkconfig monit on
    cp -a /vagrant/monit_varnish /etc/monit.d/varnish
    chown root:root /etc/monit.d/varnish
    service monit start
    #
    # Apache - PHP
    #
    yum -y install php
    /sbin/service httpd start
    /sbin/chkconfig httpd on
    echo "<?php echo date('Y/m/d H:i:s');" > /var/www/html/sample.php
  EOT
end
