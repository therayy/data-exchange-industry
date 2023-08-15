# Install [IBM Sterling Control Center](https://www.ibm.com/docs/en/control-center/6.3.0)

   > _This recipe is for deploying the IBM Sterling Control Center on Red Hat Enterprise Linux assuiming you already got your system subsription done and DB2 deployed_.

### Section A: Pre-Cofiguration
<details>
    <summary> Starting your DB2 & Createing your Database, turning off your firewall and adding your hostname into etc/hosts </summary>

1.  Start your database engine.
```bash
su - db2inst1
db2start
```
```bash
db2
```
   > 💡 **OUTPUT**  
   > your terminal should look like that 
   ```
   db2 =>
   ```
2. Create ICC database.
```sql
CREATE DATABASE ICCDB AUTOMATIC STORAGE YES USING CODESET UTF-8 TERRITORY DEFAULT COLLATE USING SYSTEM PAGESIZE 32768
```
   > 💡 **OUTPUT**  
   > ```DB20000I  The CREATE DATABASE command completed successfully.``` 
```sql
CONNECT TO ICCDB
```
```sql
CREATE BUFFERPOOL ICCDB_04KBP IMMEDIATE SIZE AUTOMATIC PAGESIZE 4K
```
```sql
CREATE BUFFERPOOL ICCDB_08KBP IMMEDIATE SIZE AUTOMATIC PAGESIZE 8K
```
```sql
CREATE BUFFERPOOL ICCDB_16KBP IMMEDIATE SIZE AUTOMATIC PAGESIZE 16K
```
```sql
CONNECT RESET
```
```sql
CONNECT TO ICCDB
```
```sql

```
```sql
CREATE  USER TEMPORARY  TABLESPACE SCCUSERTMP PAGESIZE 32K  BUFFERPOOL  IBMDEFAULTBP 
```
```sql
CREATE REGULAR TABLESPACE TS_REG04_ICCDB PAGESIZE 4K   BUFFERPOOL  ICCDB_04KBP PREFETCHSIZE AUTOMATIC
```
```sql
CREATE REGULAR TABLESPACE TS_REG08_ICCDB  PAGESIZE 8K   BUFFERPOOL  ICCDB_08KBP PREFETCHSIZE AUTOMATIC
```
```sql
CREATE REGULAR TABLESPACE TS_REG16_ICCDB  PAGESIZE 16K  BUFFERPOOL  ICCDB_16KBP PREFETCHSIZE AUTOMATIC
```
```sql
CONNECT RESET
```
3. Turn off the firewall
```bash
su - root
systemctl disable firewalld
```
   > 💡 **OUTPUT**  
   >> ```Removed /etc/systemd/system/multi-user.target.wants/firewalld.service.```
   >> ``` Removed /etc/systemd/system/dbus-org.fedoraproject.FirewallD1.service.``` 

4. Setting up the Hostname
```bash
hostnamectl set-hostname icc.sterling.com
```
Find out your ip `inet`
```bash
ifconfig
```
Copy the IP address and paste it inside the hosts file 
```bash
vi /etc/hosts
```
Press i, then go to the 2nd line and paste the IP address in there along with the host short name and hostname
```bash
192.168.44.xxx icc icc.sterling.com #as an example
```
Press `ESC` then type `:wq!` to save and quit

</details>

### Section B: Certificates and ICC Pre-Installation
<details>
    <summary> Keystore & Truststore certs and ICC configurations </summary>

1.  Download `icc.tar` file and extract it
```bash
tar -xvf SCC.V6300.Linux.tar
```
2. Install prerequisite packages.
```bash
yum install libstdc++ libX11 libXau libXdmcp
```
3. Install Pre-Installation for ICC make sure to access `/SCC.V6300.Linux/Linux/`directoru
```bash
chmod +x ./CCInstall64.bin
./CCInstall64.bin
```
Press <ENTER>:
```bash

Note: This installer is common for the following products:
1. IBM Sterling Control Center Director
2. IBM Sterling Control Center Monitor

Note: During the configuration process, you may choose to deploy as CC 
Director, CC Monitor or Both.
```
- [x] Accept the License Press `1`.
- [x] Accept the default install folder `/opt/IBM/SterlingControlCenter` Press <ENTER>
- [x] Review the Pre-Installation Summary Press <ENTER>
- [x] Installation Complete Press <Enter>
```
Installation complete
---------------------

IBM Sterling Control Center V6.3.0 has been successfully installed to:  
/opt/IBM/SterlingControlCenter

Next Steps:
1. To complete the configuration of IBM Sterling Control Center V6.3.0, 
execute:

/opt/IBM/SterlingControlCenter/bin/config.sh

2. After configuring IBM Sterling Control Center V6.3.0, execute

/opt/IBM/SterlingControlCenter/bin/runEngine.sh

to start  IBM Sterling Control Center V6.3.0.

3. After starting, launch the UI with following URL:
https://<hostname>:<port> or http://<hostname>:<port>

4. Log in to IBM Sterling Control Center V6.3.0.

```

### Good time to take a snapshot `shutdown -h now` and snapshot and resume.
</details>