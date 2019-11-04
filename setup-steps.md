Below details the steps required to build a 3 zone (ui, db and worker zone) setup, with a master db region.
This will build the deployment within region = 1.


# Create VMs
Create 5 x VMs
VMDB - 2 x 
1 x VM


# Create VMDB
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

# ui
appliance_console_cli --hostname {DB_IP}  --username root --password {DB_PASSWORD} --timezone=Europe/London --fetch-key {DB_IP}  --sshpassword {SSH_PASSWORD}

# worker
appliance_console_cli --hostname {DB_IP} --username root --password {DB_PASSWORD} --fetch-key {DB_IP} --sshpassword {SSH_PASSWORD} --timezone=Europe/London
