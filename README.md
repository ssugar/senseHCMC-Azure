#senseHCMC-ELK#

##Description##
Vagrantfile to deploy the ELK stack [Elasticsearch, Logstash, and Kibana](http://www.elasticsearch.org/overview/) running on Azure for use in the senseHCMC project.

===================================
##Setup to use this##
There are a number of steps you need to complete in order to run this VM on an Azure subscription.

###Install vagrant-azure plugin###
    vagrant plugin install vagrant-azure

###Install openssl###
    chocolatey install openssl.light -y
    
###Set up and Upload Azure Management Certificate###
    openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout az_cert.pem -out az_cert.pem
    openssl x509 -inform pem -in az_cert.pem -outform der -out az_cert.cer

Upload the resultant az_cert.cer file to Azure --> Manage Subcription --> Management Certificate --> Upload
	
###Set up VM Management Certificate###
    openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout vm_cert.key -out vm_cert.pem
  
###Create Azure dummy box###
    vagrant box add azure https://github.com/msopentech/vagrant-azure/raw/master/dummy.box

###Create vagrantconfig.yaml file###
In the same folder as your Vagrantfile, create a file called vagrantconfig.yaml.  That file should look like:

    subscription_id: your_azure_subscription_ID
	password: password_for_the_VMs_vagrant_account
	
###Setup Azure SQL to Receive Data###
**NOTE**: Logstash will not work unless you set up Azure SQL and /etc/logstash/conf.d/logstash.conf, or remove the jdbc section from the outputs of /etc/logstash/conf.d/logstash.conf

Follow along with the gist [here](https://gist.github.com/ssugar/4162eaa1d638ec62051c) in order to create the required SQL table, download and import the required jdbc jar driver, and complete the setup of /etc/logstash/conf.d/logstash.conf

###Bring VM up###
    vagrant up --provider=azure
	
###Open UDP Endpoints###
It looks like the vagrant-azure plugin doesn't let you set UDP endpoints yet.  So log into Azure and add the UDP endpoint for port 5005 to the VM you created.  Or using Azure Powershell:

     $vm = Get-AzureVM -ServiceName sensehcmc -Name sensehcmc
     $vm | Add-AzureEndpoint -Name “sensehcmc” -Protocol udp -LocalPort 5005 -PublicPort 5005
     $vm | Update-AzureVM
	
###Access Web Portal###
    http://sensehcmc.cloudapp.net/kibana

###Send Test Data###
Download the sendData.py file in the root of this project and run it from any machine that has python.  It will resolve sensehcmc.cloudapp.net and then send it a JSON formatted string inside a UDP packet to port 5005 containing 6 random sensor readings.

