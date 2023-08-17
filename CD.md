# Install [IBM Connect:Direct](https://www.ibm.com/docs/en/connect-direct/6.1.0?topic=connectdirect-v610-pdfs)

   > _This recipe is for deploying the IBM Connect Direct on Red Hat Enterprise Linux assuiming you already got your system subsription done & hostname was define as icc.sterling.com_.

### Section A: [Pre-Cofiguration](./CD.md)
<details>
    <summary> Create a usename & move files using that user name into /home/username/ibm directory & chmod of all scripts </summary>

1.  Adding new user:
``` bash
useradd cdadmin
passwd cdadmin #set your password
```
2. Copy over all files into `ibm` folder
```bash
mkdir /home/cdadmin/ibm
```
3. Give permission to run all scripts
```bash
cd /home/cdadmin/ibm
chmod +x ./*
```
4. In this case I extracted the C:D installation tar and renamed it `cd` into that ../ibm directory
 
5. Run `./cdinstall`

</details>

### Section B: [Connect:Direct Installation](./CD.md)
<details>
    <summary> Install Connect Direct step by step </summary>

    > 💡 **NOTE**  
    >> Pressing `<ENTER>` Picks the default value which is in the `[Square Brackets]`

- [x] Unix is a registered Trademark of the open group: Press `<ENTER>`
- [x] You can use /home/cdadmin to shorten the name:[/home/cdadmin/cdunix] Press `<ENTER>`
- [x] You have chosen /home/cdadmin/cdunix as destination directory. Please confirm it:[Y/N] `Y`

   > 💡 **OUTPUT**  
   >> Your terminal should look like that 
    ```bash
    Please select one of the following installation options:
    (1) Connect:Direct for UNIX Server and Client(CLI/API)
    (2) Connect:Direct for UNIX Server
    (3) Connect:Direct for UNIX Client(CLI/API)
    (4) Connect:Direct for UNIX File Agent
    (5) Connect:Direct Secure+ Option for UNIX
    (6) EXIT
    Enter your choice:[1] #PRESS <ENTER>
    ```
    ##### 💡 First we will do Server and Client then we will come back do File Agent then Secure + 

2. Connect:Direct for UNIX Server and Client(CLI/API)

- [x] Specify the fully qualified name of the Connect:Direct for UNIX installation
file (file name prefixed with the absolute path, e.g., /localhome/cdadmin/cdunix): i.e `/home/cdadmin/ibm/cd/cdunix`

- [x] Both Connect:Direct for UNIX Server and Client(CLI/API) Version 6.3.0.0
will be installed in your system. Do you want to continue?:[Y/N] `Y`   

3. Initial Configuration 
```bash
The customization procedure allows you to create configuration
files for:
  (1) Configure the Connect:Direct for UNIX Server.
  (2) Configure the Connect:Direct for UNIX Client.
  (3) Configure the Connect:Direct for UNIX Server and Client.
  (4) Configurations requiring root privilege.
  (5) EXIT.
Enter your choice:[3] #Press <ENTER>
```
- [x] Please enter name of Connect:Direct node you want to customize: `cdnode01` if your doing this for the first machine
- [x] Take the default on all the following Prompts by Pressing `<ENTER>`
- [x] Insert remote user record?`Y` this is the user that C:D will authenticate to remote request
- [x] Enter remote userid:`*` That will allow any-user to access.
- [x] Enter remote Connect:Direct node name:`*` taking anyone from any machine
- [x]   

</details>