# Install [IBM Sterling Control Center](https://www.ibm.com/docs/en/control-center/6.3.0)

   > _This recipe is for deploying the IBM Sterling Control Center on Red Hat Enterprise Linux assuiming you already got your system subsription done and DB2 deployed_.

### Section [A: Pre-Cofiguration]()
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
   >> your terminal should look like that 
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

### Section [B: Certificates and ICC Pre-Installation]()
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

4. Generate Sefl Assigned certificate for ICC make sure that the `CN` & `DNS` are matching with your `HostName`
```bash
su - root
mkdir /opt/IBM/certs
```
```bash
openssl req -new -newkey rsa:4096 -days 365 -nodes -x509   \
    	-subj "/C=US/ST=California/L=Orange/O=IBM/CN=icc.sterling.com" \
        -addext "subjectAltName = DNS:icc.sterling.com" \
    	-keyout /opt/IBM/certs/ccenter.key \
	-out /opt/IBM/certs/ccenter.cert
```
   > 💡 **OUTPUT**  
   >> Your terminal should look like that 
      ```
      Generating a RSA private key
      .......++++
      writing new private key to '/opt/IBM/certs/ccenter.key'
      -----
      ```
5. Create Key Cert
```bash
cat /opt/IBM/certs/ccenter.cert \
	/opt/IBM/certs/ccenter.key > \
	/opt/IBM/certs/ccenter.pem
```
6. Import `keycert` into `keystore` and set an easy password to remember i.e `passw0rd`
```bash
openssl pkcs12 -export -name ccenter \
	-in /opt/IBM/certs/ccenter.cert \
	-inkey /opt/IBM/certs/ccenter.key \
	-out /opt/IBM/SterlingControlCenter/conf/security/CCenter.keystore 
```
7. Import Trusted Cert a `truststore` file
```bash
keytool -import -keystore /opt/IBM/SterlingControlCenter/conf/security/CCenter.truststore \
	-noprompt -file /opt/IBM/certs/ccenter.cert  \
	-alias ccenter -storepass passw0rd 
```
8. Keytool list step
```bash
keytool -list -keystore /opt/IBM/SterlingControlCenter/conf/security/CCenter.keystore -storepass <YOUR PASSWORD>
```
   > 💡 **OUTPUT**  
   > your terminal should look like that 
   ```
   Certificate fingerprint (SHA-256): 4C:14:64:85:39:18:C0:21:C0:7F:B4:8F:11:14:34:0F:4E:66:8B:70:C6:52:7A:15:E0:8D:8A:34:9B:91:D3:25
   ```
</details>

### Section [C: Configuration, Installation & Validation]()
<details>
    <summary> Confguration of ICC, Installing ICC and how to vaildate and watch the logs and access the ui  </summary>

1. Make sure that you started your DB2 `db2start` remember you need to login using `db2inst1`

2. Run the config.sh
```bash
su - root
cd /opt/IBM/SterlingControlCenter/bin
chmod +x ./config.sh
./config.sh
```
   > 💡 **OUTPUT**  
   > IBM Sterling Control Center - Not configured...
   
   ```
   1. IBM Sterling Control Center Director
   2. IBM Sterling Control Center Monitor
   3. All Products
   Choose Product Option based on your entitlement [0] : 
   ``` 
   - [x] Enter `3` 
   - [x] Are the values that were entered correct? `Y`
   - [x] Do you want to continue with the configuration? `Y`
   - [x] Provide the path to your keystore: Press <ENTER>
   - [x] Enter the Password for your keystore: <passw0rd>
   - [x] Enter the Path to your store file: Press <ENTER>
   - [x] Enter the Password for your keystore: <passw0rd>
   - [x] Are the values that were entered correct? `Y`
   - [x] Provide a database type: Enter `DB2`
   - [x] Provide the full path of the db2jcc.jar: `/opt/ibm/db2/V11.5/java/db2jcc4.jar`
   - [x] Provide the full path of the license file: `/opt/ibm/db2/V11.5/java/db2jcc_license_cu.jar`
   - [x] Are the values that were entered correct? `Y`
   - [x] Do you want to configure a secure connection to your database? `N`
   - [x] Provide the database host name: Press <ENTER>
   - [x] Provide the database port number: Press <ENTER>
   - [x] Provide the database user name: `db2inst1`
   - [x] Database Passwrod: `<YOUR DATABSE PASSWORD>``
   - [x] Provide the database name: `iccdb`
   - [x] Are the values that were entered correct? `Y`
   - [x] Do you want to partition your database tables `N` `# we do that in Prod ONLY!`
   - [x] Are you sure about your selection? `Y`
   - [x] Enter Default user `admin` password: `P@ssw0rd` `# Feel free to pick your own`
   - [x] Enter Default `admin` E-Mail address: `Raafat@ibm.com`
   - [x] Provide a 10 character Event Processor (engine) name [CCenter]: `ccenter`
   - [x] 


</details>