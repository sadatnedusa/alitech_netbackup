### **Veritas NetBackup Master Server Cheat Sheet**

- This cheat sheet summarizes essential **commands** and **concepts** for managing and troubleshooting a Veritas NetBackup (NBU) Master Server. 
- It covers tasks related to backups, policies, storage, and system health.

---

### **1. NetBackup Services Management**

#### **Check NetBackup Services Status:**
```bash
bpps -a           # Lists all NetBackup processes running on the server
nbdb_ping         # Verifies NetBackup database connectivity
```

#### **Start/Stop Services:**
```bash
/usr/openv/netbackup/bin/goodies/netbackup start      # Start NetBackup services
/usr/openv/netbackup/bin/goodies/netbackup stop       # Stop NetBackup services
```

#### **Check Detailed Status:**
```bash
bpdbjobs          # Shows the job status for active, queued, and completed jobs
nbemmcmd -listhosts   # Lists all hosts managed by the EMM server
```

---

### **2. Backup Policies Management**

#### **List Backup Policies:**
```bash
bppllist          # Lists all backup policies
bppllist <policy_name> -U     # List details of a specific policy
```

#### **Create/Modify/Delete a Backup Policy:**
```bash
bpplinfo <policy_name> -add       # Add a new policy
bpplinfo <policy_name> -modify    # Modify an existing policy
bpplinfo <policy_name> -delete    # Delete a policy
```

#### **Activate/Deactivate a Policy:**
```bash
bpplinfo <policy_name> -active    # Activate a policy
bpplinfo <policy_name> -inactive  # Deactivate a policy
```

---

### **3. Backup Jobs and Job Management**

#### **Manually Initiate a Backup:**
```bash
bpbackup -i -p <policy_name> -s <schedule_name> -S <master_server>
```

#### **Monitor Running Jobs:**
```bash
bpdbjobs -report        # Provides detailed information about running jobs
```

#### **Cancel a Running Job:**
```bash
bpdbjobs -cancel <job_id>
```

#### **Job Status Codes:**
- `0`: Success
- `1`: Partial success
- `6`: Backup failed due to network issue
- `58`: Can't communicate with the client

For more details:
```bash
bperrcode <status_code>
```

---

### **4. Catalog and Images Management**

#### **Catalog Backup:**
```bash
bpbackupdb            # Back up the NetBackup catalog
```

#### **List Images of Backups:**
```bash
bpimagelist -d <mm/dd/yyyy> -e <mm/dd/yyyy> -client <client_name> -U
```

#### **Restore from Catalog:**
```bash
bprestore -L /path/to/logfile -C <client_name> -p <policy_name> -s <start_date> -e <end_date>
```

#### **Catalog Cleanup:**
```bash
bpexpdate -backupid <backup_id> -d 0    # Expire a backup immediately
bpexpdate -d <mm/dd/yyyy> -force        # Expire images before a specific date
```

---

### **5. Media and Storage Management**

#### **List Storage Units:**
```bash
bpstulist -U
```

#### **Check Available Media:**
```bash
vmquery -a                    # Lists all available tapes and their status
nbemmcmd -listmedia -allvolumes   # Lists media across all storage
```

#### **Reassign Media:**
```bash
bpmedia -movedb -m <media_id> -newserver <new_media_server>
```

#### **Inventory Tape Library:**
```bash
vmphyinv -rn <robot_number> -rt tld -rh <media_server> -v
```

---

### **6. Disk Pools and Disk Staging**

#### **List Disk Pools:**
```bash
nbdevquery -listdp -stype PureDisk -U   # List disk pools by type (e.g., PureDisk)
```

#### **Check Disk Pool Usage:**
```bash
nbdevquery -liststs -U           # Lists storage server status
```

#### **Configure Disk Staging Storage Unit:**
```bash
bpstuadd <stunit_name> -dssu -maxfrag 5 -rl 3 -maxsize 1000
```

---

### **7. Troubleshooting**

#### **Logs and Log Locations:**
- **/usr/openv/netbackup/logs/**: Main directory for NetBackup logs.
- **/usr/openv/logs/**: Additional system logs.
- **bpdbjobs**: Monitor and review job logs.

#### **Enable Verbose Logging (Debugging Mode):**
```bash
vxlogcfg -a -p 51216 -o 200 -s DebugLevel=6
```

#### **Check Client-Server Communication:**
```bash
bpclntcmd -pn           # Checks communication between client and master server
bpclntcmd -hn <hostname>   # Resolves client hostname
```

#### **Check Media Server Communication:**
```bash
nbemmcmd -listhosts
```

---

### **8. General Commands**

#### **Check Version Information:**
```bash
nbemmcmd -version         # EMM Server Version
bpgetconfig               # NetBackup version
```

#### **List All Clients:**
```bash
bpplclients <policy_name> -allunique
```

#### **Configure Backup Windows:**
```bash
bpplinfo <policy_name> -window <day> <start_time> <end_time>
```

---

### **9. Security Management**

#### **NetBackup User and Role Management:**
```bash
bpnbaz -ProvisionUser -UserName <username> -DomainName <domain> -RoleName <role> -force
```

#### **Configure Host Security:**
```bash
bpnbat -login -logintype WEB
```

#### **Backup Encryption:**
```bash
nbkmcmd -createenckey -keyname <key_name> -kek <encryption_key>
```

---

### **10. NetBackup CLI Commands Overview**

- **`bpdbjobs`**: Job status and history.
- **`bpimagelist`**: List image backups.
- **`bppllist`**: List backup policies.
- **`nbdevquery`**: Query disk storage units.
- **`bperror`**: Error code explanations.
