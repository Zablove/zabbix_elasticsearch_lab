# Zabbix Elasticsearch Lab

This repo will provide a basic setup for testing Zabbix 6.4.latest in combination with Elasticsearch 8.13.0. You need docker-compose for this lab, this is however beyond the scope of this lab.

Disclaimer: This is a very basic lab to test the Zabbix Elasticsearch Mediatype. This lab is not for production purposes, it uses default configurations and lacks security. Local storage is not configured in the docker-compose file, so after stopping the lab, all configuration will be lost unless you apply changes to the docker-compose file.

## Start docker-compose
You can start the lab by executing the command ```docker-compose up``` from whithin this direcory. 

## Configure Elasticsearch
After the startup of the docker containers you will be able to browse to Kibana http://localhost:5601

As you can see, no password is required, this means security is not enabled in Elasticsearch and a creation of an API key for the Zabbix Mediatype is not needed in this situation. 

1. Create an Index template in Elasticsearch to ensure your Zabbix data will be correctly indexed:
   - In Kibana, go to `Management` -> `Dev Tools` and paste the content from the file [elasticsearch_index.txt](./elasticsearch_index.txt). Then execute the code

![Apply Index Template](./screenshots/apply_index_template.png)

2. Import the Mediatype:
   - Browse to the Zabbix webinterface en login with default credentials Admin/zabbix
   - In Zabbix go to `Alerts` -> `Media types` and click Import, browse to the xml file and click Import
   - Open the Mediatype and make sure these parameters match the values:
     - elastic_url: http://elasticsearch:9200/
     - elastic_index: zabbix_events
     - zabbix_url: http://localhost

![Apply Index Template](./screenshots/mediatype_settings.png)

3. Create a new user for Elasticsearch:
   - Browse to `Users` -> `Users` and click `Create user`
   - Enter Username `Elasticsearch`
   - Enter a random password
   - Click the `Media` tab and `Add` to add a mediatype
   - Enter `Elastic` in `Send to` and click `Add`
   - Click the `Permissions` tab and add Role `Admin role`
   - Click `Add` to add the user

![Add User](./screenshots/add_user1.png.png)
![Add User Mediatype](./screenshots/add_user2.png.png)
![Add User Permissions](./screenshots/add_user3.png.png)

4. Create the trigger actions:
   - Browse to `Alerts` -> `Actions` -> `Trigger actions` and click `Create action`
   - Add name `Elasticsearch`
   - Under Conditions click `Add`
   - Select type `Trigger severity`, Operator `is greater then or equals` and Severity `Not classified`
   - Click the `Operations` tab and under `Operations` click `Add`



Note: A separate users is created to make sure updates of problems will also be send to Elasticsearch. In this lab Role Admin and User group Zabbix Administrators is used for this user, best practice is to create a separate role and User group with only read rights to hostgroups needed for this Mediatype.