Below details the steps required to build a 3 zone (ui, db and worker zone) setup, with a master db region.
This will build the deployment within region = 1.


# Create Virtual Machines
Create 5 x VMs:
 * 2 x VMDB
 * 1 x UI
 * 1 x Worker
* Increase disk space by adding adding addition virtual disk to the required VMs, and then performing the steps in `increase-disk.txt`


## Configure VMDB Redunant air
### VMDB 1
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
* via appliance_console reset DB.
### VMDB 2
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
* via appliance_console reset DB.
## Configure UI
* Run the command below:
```
vmdb ; rm config/database.yml
appliance_console_cli --hostname {DB_IP1} \
                      --username root \
                      --password {DB_PASSWORD} \
                      --timezone=Europe/London \
                      --fetch-key {DB_IP1}  \
                      --sshpassword {SSH_PASSWORD}
```

## Configure Worker
* Run the command below:
```
vmdb ; rm config/database.yml
appliance_console_cli --hostname {DB_IP1} \ 
                      --username root \
                      --password {DB_PASSWORD} \
                      --fetch-key {DB_IP1} \
                      --sshpassword {SSH_PASSWORD} \
                      --timezone=Europe/London
```
## UI Configuration
Via the portal configure the required zones and services for each device:
* Zones : Configuration / Settings / Zones - Create Zone and assign servers to zones.
* Services : Under each server configure the required services.
