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
<detatils>
    <summary> Making sure that you have all packages </summary>

1. Copying `DB2 Tar file` into your server and extracting it.
```bash
Copy DB2_Svr_11.5_Linux_x86-64.tar to server
tar -xvf DB2_Svr_11.5_Linux_x86-64.tar to server
```
2. Access `DB2_Svr Directory`
```bash
cd /server_dec
ls -la
```
3. Run the script that lists all the missing packages, you will have to permit that script.
```bash
chmod +x ./db2prereqcheck
```
```bash
./db2prereqcheck -i -l 
```

> 💡 **NOTE**  
> 
</details>