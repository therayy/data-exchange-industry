# [IBM Connect:Direct](https://www.ibm.com/docs/en/connect-direct/6.1.0?topic=connectdirect-v610-pdfs) - [Installation & Configuration](./CD.md)

   > _These notes is for C:D the IBM Connect Direct assuiming you already installed it on your system & hostname was define as i.e cdnode01.sterling.com_.
    >> References <br>
    <span style="font-size: 10px;">
    <sub> 
    [How the Proxy.attempt & SNODEID work together](https://www.ibm.com/support/pages/connectdirect-unix-how-proxyattempt-and-snodeid-parameters-work-together) </sub > <br>
    <sub> [For Windows: How does the Proxy.attempt parameter work?](https://www.ibm.com/support/pages/connectdirect-windows-how-does-proxyattempt-parameter-work-0)</sub>
    </span>
### Senario A: 
**`proxy.attempt=N`**
, The Remote ID has both a Functional Authority and Proxy record on your C:D node. 
🔗 Your C:D node will attempt to validate the incoming ID with the Functional Authority record. If this fails, the process fails. ~~C:D will not try the Proxy record.~~ 

### Senario B: 
**`proxy.attempt=N`**
, The Remote ID has only a Proxy record on your C:D node. 
🔗 Your C:D node will first look in Functional Authorities for the incoming ID and ~~will not find a record for the incoming ID~~
- C:D will then look in Proxies for a record. Finding it
- C:D will map the process to the `LocalUserid` specified in the Proxy record.
- C:D will then attempt to validate the LocalUserid in the Functional Authorities records. If this validation for the LocalUserid fails, the process fails.

### Senario C: 
**`proxy.attempt=Y`**
, The Remote ID has only a Proxy record on your C:D node.
🔗 Your C:D node will first look in Functional Authorities for the incoming ID and ~~will not find a record for the incoming ID~~
- C:D will then look in Proxies for a record. Finding it
- C:D will map the process to the `LocalUserid` specified in the Proxy record.
- C:D will then attempt to validate the LocalUserid in the Functional Authorities records. If this validation for the LocalUserid fails, the process fails.