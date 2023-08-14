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
</details>
