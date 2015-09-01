# -*- mode: ruby -*-
# vi: set ft=ruby :

# Vagrantfile API/syntax version. Don't touch unless you know what you're doing!
VAGRANTFILE_API_VERSION = '2'
PRIVATE_NETWORK_IP = ENV['CASSIE_VAGRANT_PRIVATE_NETWORK_IP'] || '192.168.77.102'
VM_NAME = ENV['CASSIE_VAGRANT_VM_NAME'] || 'cassandra'

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
  config.vm.provider :virtualbox do |vb, override|
    override.vm.box = 'bitmotive/CentOS-6.6-64bit-BASE'

    vb.name = VM_NAME
    vb.customize ["modifyvm", :id, "--memory", "4096"]
  end

  config.vm.provider :parallels do |vm, override|
    override.vm.box = 'parallels/centos-6.6'
    vm.memory = 4096
  end

  config.vm.network :private_network, ip: PRIVATE_NETWORK_IP
  config.vm.provision :shell, inline: <<-SCRIPT
if [ ! -f jre-7u79-linux-x64.rpm ]; then
  echo "Downloading Java 7 ..."
  wget --no-cookies --no-check-certificate -nv --header "Cookie: gpw_e24=http%3A%2F%2Fwww.oracle.com%2F; oraclelicense=accept-securebackup-cookie" "http://download.oracle.com/otn-pub/java/jdk/7u79-b15/jre-7u79-linux-x64.rpm"
  echo "Installing Java 7 ..."
  rpm -ivh jre-7u79-linux-x64.rpm
fi

if [ ! -f dsc.tar.gz ]; then
  if [ ! -d /usr/cassandra ]; then
    mkdir /usr/cassandra
  fi

  if [ ! -d dsc ]; then
    mkdir dsc
  fi
  echo "Downloading Cassandra DB ..."
  wget -nv http://downloads.datastax.com/community/dsc.tar.gz
  tar xfz dsc.tar.gz --strip-component=1 -C dsc
  mv dsc/ /usr/cassandra
fi

if [ ! -f /etc/yum.repos.d/datastax.repo ]; then
  cat > /etc/yum.repos.d/datastax.repo <<TEXT
[datastax]
name = DataStax Repo for Apache Cassandra
baseurl = http://rpm.datastax.com/community
enabled = 1
gpgcheck = 0
TEXT
  yum install -y dsc21
  yum install -y cassandra21-tools
fi

echo "Updating Cassandra configuration to use IP: #{ PRIVATE_NETWORK_IP } ..."
sed -i -- 's/\\(listen_address\\|rpc_address\\):\\(.*\\)/\\1: #{ PRIVATE_NETWORK_IP }/g' /etc/cassandra/conf/cassandra.yaml
sed -i -- 's/seeds:\\(.*\\)/seeds: "#{ PRIVATE_NETWORK_IP }"/g' /etc/cassandra/conf/cassandra.yaml
chkconfig --add cassandra
service cassandra start
SCRIPT
end
