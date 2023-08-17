# Install [IBM Connect:Direct](https://www.ibm.com/docs/en/connect-direct/6.1.0?topic=connectdirect-v610-pdfs)

   > _This recipe is for deploying the IBM Connect Direct on Red Hat Enterprise Linux assuiming you already got your system subsription done_.

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
4. Run `./cdinstall`

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
        ```
        Please select one of the following installation options:
        (1) Connect:Direct for UNIX Server and Client(CLI/API)
        (2) Connect:Direct for UNIX Server
        (3) Connect:Direct for UNIX Client(CLI/API)
        (4) Connect:Direct for UNIX File Agent
        (5) Connect:Direct Secure+ Option for UNIX
        (6) EXIT
        Enter your choice:[1]
        ```
    ##### 💡 First we will do Server and Client then we will come back do File Agent then Secure + 




</details>