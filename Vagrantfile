# -*- mode: ruby -*-
# vi: set ft=ruby :

$install_bcc = <<EOF
  apt-get -qq update
  apt-get install -y bpfcc-tools linux-headers-$(uname -r)
EOF

$install_bpftrace = <<EOF
  apt-get -qq update
  apt-get install -y bpftrace
EOF

# Binaries installed via apt are stripped - so a bunch of stuff is broken and we
# need to install debug symbols
#
# Context:
# https://github.com/iovisor/bpftrace/issues/954
# https://bugs.launchpad.net/ubuntu/+source/bpftrace/+bug/1969625
#
# Fix from:
# https://wiki.ubuntu.com/Debug%20Symbol%20Packages
$install_debug_symbols = <<EOF
  echo "deb http://ddebs.ubuntu.com $(lsb_release -cs) main restricted universe multiverse
  deb http://ddebs.ubuntu.com $(lsb_release -cs)-updates main restricted universe multiverse
  deb http://ddebs.ubuntu.com $(lsb_release -cs)-proposed main restricted universe multiverse" | \
  tee -a /etc/apt/sources.list.d/ddebs.list
  apt install ubuntu-dbgsym-keyring
  apt-key adv --keyserver keyserver.ubuntu.com --recv-keys F2EDC64DC5AEE1F6B9C621F0C8CAB6595FDFF622
  apt-get -qq update
  apt-get install -y bpftrace-dbgsym
EOF

Vagrant.configure("2") do |config|
  config.vm.define "ubuntu-22.10" do |box|
    box.vm.box = "ubuntu/kinetic64"
    box.vm.provider "virtualbox" do |vb|
      vb.memory = "2048"
      vb.cpus = 2
    end
    box.vm.provision :shell, inline: $install_bcc
    box.vm.provision :shell, inline: $install_bpftrace
    box.vm.provision :shell, inline: $install_debug_symbols
  end
  config.vm.post_up_message = <<-HEREDOC
###
Test tools work:
sudo opensnoop-bpfcc
sudo bpftrace -e 'BEGIN { printf("hello world\n"); }'
###
  HEREDOC
end
