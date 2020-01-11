# docker-compose for Red Hat Quay 

It's Quay docker-compose solution for Quay community and enterprise by Jimmy Zhang

Add new environment varible "ENCRYPTED_ROBOT_TOKEN_MIGRATION_PHASE=new-installation"

to support Quay development. If it's Quay enterprise, you may remove the environment varible from docker-compose yaml file, 

Please refer to the doc below.

[Quay Image Build](https://github.com/quay/quay/blob/master/docs/development-container.md)

For Clair Deployment, Please revise clair-config/config.yaml based on your real environment.

Please refer to the official doc below.

[Clair Scan Deployment](https://access.redhat.com/documentation/en-us/red_hat_quay/3/html-single/manage_red_hat_quay/index#quay-security-scanner)
## Quay Deployment
```
# Generate self certification 
./self-cert-generate.sh test.com registry.test01.com test01.com test01

# Deploy Quay
# create Directory for Quay
sudo sh pre-quaydeploy.sh

# Create the quayconfig container
#for mysql
sudo docker-compose  -f docker-compose.config-mysql.yml up -d
#for pgsql
sudo docker-compose  -f docker-compose.config-pgsql.yml up -d
sudo docker-compose -f docker-compose.config-pgsql.yml exec pgsql /bin/bash /usr/local/bin/post-pgsql.sh

# Generate config file via web GUI
username/password: quayconfig/redhat
sudo mv quay-config.tar.gz  /quay/config
cd /quay/config && tar -zxvf quay-config.tar.gz

# Delete the quayconfig container
sudo sh ./pre-deleteconfig.sh

# Stop redis and mysql/pgsql
#for mysql
sudo docker-compose  -f docker-compose.config-mysql.yml stop
#pgsql
sudo docker-compose  -f docker-compose.config-pgsql.yml stop

# Start mysql, redis and Quay
#for mysql
sudo docker-compose  -f docker-compose.quay-mysql.yml up -d
#for pgsql
sudo docker-compose  -f docker-compose.quay-pgsql.yml up -d

# Clean up Quay
sh clear-quay.sh
