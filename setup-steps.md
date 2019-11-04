Below details the steps required to build a 3 zone (ui, db and worker zone) setup, with a master db region.
This will build the deployment within region = 1.


# Create Virtual Machines
* Create 5 x VMs:
 * 2 x VMDB
 * 1 x UI
 * 1 x Worker
* Increase disk space by adding adding addition virtual disk to the required VMs, and then performing the steps in `increase-disk.txt`


## Configure VMDB Redunant air
## VMDB 1
* Run script `clean-miq.sh`.
* Run the command below:
```
appliance_console_cli --region 1 \
                      --internal \
                      --key \ 
                      --username root \
                      --password {DB_PASSWORD} \
                      --dbname vmdb_production \
                      --verbose \
                      --dbdisk /dev/vdb \
                      --standalone \
                      --timezone=Europe/London \
                      --force-key
```
* Check status of device from within `appliance_console`.
* Run the command below:
```
appliance_console_cli --username root \
                      --password {DB_PASSWORD} \
                      --dbname vmdb_production \
                      --replication primary \ 
                      --primary-host {DB_IP1} \ 
                      --cluster-node-number 1 \ 
                      --auto-failover
```
## VMDB 2
* Run script `clean-miq.sh`.
* Run the command below:
```
appliance_console_cli --replication standby \
                      --username root \
                      --password={DB_PASSWORD}  \
                      --primary-host {DB_IP1}\
                      --standby-host {DB2_IP} \
                      --cluster-node-number 2 \
                      --dbname vmdb_production \
                      --dbdisk /dev/vdb \
                      --fetch-key {DB_IP1} \
                      --sshpassword {SSH_PASSWORD} \
                      timezone=Europe/London
```
* Check status of device from within `appliance_console`.

# Configure UI
* Run the command below:
```
appliance_console_cli --hostname {DB_IP1} \
                      --username root \
                      --password {DB_PASSWORD} \
                      --timezone=Europe/London \
                      --fetch-key {DB_IP1}  \
                      --sshpassword {SSH_PASSWORD} \
                      --timezone=Europe/London
```

# Configure Worker
* Run the command below:
```
appliance_console_cli --hostname {DB_IP1} \ 
                      --username root \
                      --password {DB_PASSWORD} \
                      --fetch-key {DB_IP1} \
                      --sshpassword {SSH_PASSWORD} \
                      --timezone=Europe/London
```
