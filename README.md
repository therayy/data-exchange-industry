# Install [IBM DB2](https://www.ibm.com/docs/en/db2/11.1?topic=administration-db2-data-servers)

This recipe is for deploying the DB2 on Red Hat Enterprise Linux.

### Section A: Subscription Manager
<details>
    <summary> Facing trouble while running yum command </summary>

1. You will need to run this command to verify the subscription status.
```bash
subscription-manager status
```
Then
```bash
yum repolist
```
2. If you got an error or result of 0 you will need to run into the following steps to setup your `Subscription`

```bash
subscription-manager attach --auto
```
```bash
subscription-manager remove --all
```
```bash
subscription-manager unregister
```
subscription-manager clean
```bash
yum clean all
```
```bash
rm -rf /var/cache/yum/*
```
```bash
subscription-manager register
```
```bash
subscription-manager attach --auto
```
3. Once this is done, verify the subscription status and checkout the populate the repository.

```bash
subscription-manager status
```
```bash
yum repolist
```
</details>

### Section B: DB2 Pre Check.
<details>
    <summary> Running precheck script to make sure that you have all packages </summary>

1. Copying `DB2 tar file` into your server and extracting it.
```bash
Copy DB2_Svr_11.5_Linux_x86-64.tar to server
tar -xvf DB2_Svr_11.5_Linux_x86-64.tar to server
```
2. Access `DB2_Svr Directory`
```bash
cd /DB2_Svr_11.5_Linux_x86-64/server_dec
ls -la
```
3. Run the script that lists all the missing packages, you will have to permit that script.
```bash
chmod +x ./db2prereqcheck
```
```bash
./db2prereqcheck -i -l 
``` 
   > 💡 **OUTPUT**  
   > Should return without any missing packages.

_OR_

   > **⚠️** **FAILED** 

   > `The db2prereqcheck utility failed to find the following libary file: libstdc++.so.6`

4. Assuming this is the only one missing to fix that you will need to run the following.
```bash
yum install libstdc++.so.6 # Assuming this is the one mentioned on failure message
``` 
Also you will need to run 
```bash
ldconfig -p | grep libpam.so.*
```
   > 💡 **OUTPUT**  
   > Should return with `libpam package` you will have to install as well.

```bash
yum install libpam.so.0 #Assuming this was on your output from the previous command
```
5. Re-run the precheck again to make sure.
```bash
cd /server_dec
./db2prereqcheck -i -l
```
</details>

### Section C: Install DB2
<details>
    <summary> Installing DB2 by running the DB2 Install script </summary>

1. Make sure you at the right directory
```bash
cd /DB2_Svr_11.5_Linux_x86-64/server_dec
```
2. Permit & Run the script.
```bash
chmod +x ./db2_install
./db2_install
```
3. Prompts will show up 
    - License: `YES`
    - Specify one of the DB2 Products: `SERVER`
    - PureScale: `NO`
   > 💡 **OUTPUT**  
   > Should return with `The execution completed successfully` after running `59` Tasks, its ok to see warnings not ~~ERRORS~~.
4. Check the Install
```bash
chmod +x ./db2ls
./db2ls
```
   > 💡 **OUTPUT**  
   > You should see the following 

| Install Path | Level | Install Date | UID |
| --- | --- | --- | --- |
| /opt/ibm/db2/V11.5 | 11.5.0.0 | --/--/---- | 0 -> root |
5. Validation
```bash
ls /usr/local/bin | grep db2  #output 
```
   > 💡 **OUTPUT**  
   > `db2greg` & `db2ls`
```bash
/user/local/bin/db2grep -dump
```
   > 💡 **OUTPUT**  
   > You will see all your installation information

```bash
/opt/ibm/db2/V11.5/bin/db2val
```
   > 💡 **OUTPUT**  
   > You should see that `db2val command is running`, installation file ..../db2/V11.5 was successful & The `db2val command completed successfully`
6. Check log file for errors
```bash
cd /tmp
ls | grep db2val-
```
Copy the output and run this command to debug
```bash
more /tmp/db2val-xxxxx.log # The output you copied above.
```
   > 💡 **OUTPUT**  
   > You should see that all success and no ~~errors~~
</details>

### Section D
<details>
    <summary> Creating DB2 Instance & Database Creation </summary>

1. First we need to make sure if the `userid` exists.
```bash
id db2inst1
``` 
IF NOT
```bash
useradd -m db2inst1
```
```bash
passwd db2inst1 #create a new password & memorize it I used db2inst1 as the password as well.
```
2. Create DB2 Instance
```bash
/opt/ibm/db2/V11.5/instance/db2icrt -u db2inst1 db2inst1
```
   > 💡 **OUTPUT**  
   > You should see 4 Tasks with `The execution completed successfully`
3. Use your `userid` to start your instance.
```bash
su - db2inst1 #login with db2inst1
```
Find the port to connect to
```bash
cat /etc/services | grep db2c_db2inst1 #In my case it is 50000
```
4. Start the DB2 instance.
```bash 
db2start
```
   > 💡 **OUTPUT**  
   > You should see `DB2START processing was successful.`
5. Check for the listen port
```bash
netstat -na | grep 50000 #Assuming that your port was 50000 from the step D.3
```
   > 💡 **OUTPUT**  
   > You should see the record ending on your right side with `LISTEN`
6. Create your first Database using `db2sampl`
```bash
db2sampl -name <DATABASE_NAME>
```
7. Test Connection
```bash
su - db2inst1
```
```sql
db2 connect to <DATABASE_NAME>
```
   > 💡 **OUTPUT**  
        > #### Database Connection Information
 
 | Database server        | = DB2/LINUXX8664 11.5.0.0 |
 | --- | --- | 
 | SQL authorization ID  | = DB2INST1 |
 | Local database alias  | = <DATABASE_NAME> | 

</details>