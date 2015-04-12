# -*- mode: ruby -*-
# vi: set ft=ruby :

require "yaml"

_config = YAML.load(File.open(File.join(File.dirname(__FILE__), "vagrantconfig.yaml"), File::RDONLY).read)

CONF = _config

# Vagrantfile API/syntax version. Don't touch unless you know what you're doing!
VAGRANTFILE_API_VERSION = "2"

$script = <<SCRIPT
sudo su
#echo "deb http://mirror-fpt-telecom.fpt.net/ubuntu/ precise main restricted universe" > /etc/apt/sources.list
#echo "deb http://mirror-fpt-telecom.fpt.net/ubuntu/ precise-updates main restricted universe" >> /etc/apt/sources.list
#echo "deb http://mirror-fpt-telecom.fpt.net/ubuntu/ precise-security main restricted universe" >> /etc/apt/sources.list
echo "Asia/Ho_Chi_Minh" > /etc/timezone
dpkg-reconfigure --frontend noninteractive tzdata
apt-get update
apt-get install git -y
apt-get install nginx -y
apt-get install openjdk-7-jdk -y
cd /home/vagrant
curl -XGET https://raw.githubusercontent.com/ssugar/senseHCMC-ELK/master/localELK/elasticsearch-1.4.2.deb > elasticsearch-1.4.2.deb
dpkg -i elasticsearch-1.4.2.deb
update-rc.d elasticsearch defaults 95 10
curl -XGET https://raw.githubusercontent.com/ssugar/senseHCMC-ELK/master/cookbooks/ss_kibana/files/default/elasticsearch.yml > elasticsearch.yml
cp /home/vagrant/elasticsearch.yml /etc/elasticsearch/elasticsearch.yml
service elasticsearch start
curl -XGET https://raw.githubusercontent.com/ssugar/senseHCMC-ELK/master/localELK/logstash-1.4.2-1.deb > logstash-1.4.2-1.deb
dpkg -i logstash-1.4.2-1.deb
service logstash restart
curl -XGET https://raw.githubusercontent.com/ssugar/senseHCMC-ELK/master/localELK/kibana-latest.tar.gz > kibana-latest.tar.gz
tar -xvzf kibana-latest.tar.gz
cp -R kibana-latest /usr/share/nginx/www/kibana
curl -XGET https://raw.githubusercontent.com/ssugar/senseHCMC-ELK/master/cookbooks/ss_logstash/files/default/logstash.conf > logstash.conf
cp /home/vagrant/logstash.conf /etc/logstash/conf.d/logstash.conf
curl -XGET https://raw.githubusercontent.com/ssugar/senseHCMC-ELK/master/cookbooks/ss_kibana/files/default/senseHCMCDashboard.json > senseHCMCDashboard.json
cp /home/vagrant/senseHCMCDashboard.json /usr/share/nginx/www/kibana/app/dashboards/default.json
service nginx restart
service logstash restart
curl -XGET https://raw.githubusercontent.com/ssugar/senseHCMC-ELK/master/updatedEsMappings.sh > updatedEsMappings.sh
sh updatedEsMappings.sh
curl -XGET https://raw.githubusercontent.com/ssugar/senseHCMC-Azure/master/nginx.conf > nginx.conf
cp /home/vagrant/nginx.conf /etc/nginx/sites-available/default
service nginx restart
curl -XGET https://raw.githubusercontent.com/ssugar/senseHCMC-ELK/master/testing/sendData.py > sendData.py
SCRIPT

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
   #Set the virtual machine 'box' to use
   config.vm.box = "azure"

   config.vm.provider :azure do |azure, override|
     override.vm.synced_folder ".", "/vagrant", disabled: true
     azure.mgmt_certificate = "az_cert.pem"
	 azure.mgmt_endpoint = "https://management.core.windows.net"
 	 azure.subscription_id = CONF['subscription_id']
	 azure.vm_image = "b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-12_04_5-LTS-amd64-server-20150204-en-us-30GB"
	 azure.vm_user = "vagrant"
	 azure.vm_password = CONF['password']
	 azure.vm_name = "senseHCMC"
	 azure.cloud_service_name = "senseHCMC"
	 azure.vm_location = "West US"
	 azure.private_key_file = "vm_cert.key"
	 azure.certificate_file = "vm_cert.pem"
	 azure.ssh_port = "22"
	 azure.tcp_endpoints = "80"
   end

   config.ssh.username = "vagrant"
   config.ssh.password = CONF['password']
   config.vm.provision "shell", inline: $script

end
