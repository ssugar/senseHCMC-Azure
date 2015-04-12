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
    
###Set up Azure Management Certificate###
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

###Bring VM up###
    vagrant up --provider=azure
	
###Open UDP Endpoints###
It looks like vagrant-azure plugin doesn't let you set UDP endpoints yet.  So log into Azure and add the UDP endpoint for port 5005 to the VM you created
	
###Access Web Portal###
    http://<<cloud_service_name>>.cloudapp.net/kibana


