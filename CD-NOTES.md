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
, The Remote ID has both a Functional Authority and Proxy record on your C:D node. <br/>
🔗 Your C:D node will attempt to validate the incoming ID with the Functional Authority record. If this fails, the process fails. _C:D will not try the Proxy record._

### Senario B: 
**`proxy.attempt=N`**
, The Remote ID has only a Proxy record on your C:D node <br/>
🔗 Your C:D node will first look in Functional Authorities for the incoming ID and _will not find a record for the incoming ID_
- C:D will then look in Proxies for a record. Finding it
- C:D will map the process to the `LocalUserid` specified in the Proxy record.
- C:D will then attempt to validate the `LocalUserid` in the Functional Authorities records. 
- _If this validation for the LocalUserid fails, the process fails._

### Senario C: 
**`proxy.attempt=Y`**
, The Remote ID has both a Functional Authority and Proxy record on your C:D node. <br/>
🔗 Your C:D node will first look in the Proxy records. Finding the record.
- C:D will map the process to the `LocalUserid` specified in the Proxy record.
- C:D will then attempt to validate the `LocalUserid` in the Functional Authorities records. 
- If this validation for the `LocalUserid` fails, the process fails. _C:D will not try the Functional Authorities record for the incoming ID._

### Senario D:
**`proxy.attempt=Y`**
, The Remote ID has only a Proxy record on your C:D node. <br/>
🔗 Your C:D node will first look in the Proxy records. Finding the record. 
- C:D will map the process to the `LocalUserid` specified in the Proxy record. 
- C:D will then attempt to validate the `LocalUserid` in the Functional Authorities records. 
- If this validation for the `LocalUserid` fails, the process fails.
- If your C:D node does not find a Proxy record, it will look in Functional Authorities for a record for the remote ID. Not finding one, _the process will fail._

### Best Practice:

1. Define remote user IDs in the Proxies.
2. **DO NOT** define remote user IDs in Functional Authorities.
3. Set `proxy.attempt=Y`

### For `userfile.cfg`
- Various proxies as shown below always mapped to `cdadmin/passw0rd`
- Local users for `cdadmin, cduser, and cdremote`
- Defined users `cdadmin/passw0rd, cduser/pass, cdremote/1234`

| Proxy | Local | Snodeid | Result |
| :--- | :---: | :--- | :--- |
| cduser@cdnode05 | cdadmin | cduser,passw0rd | fail |
| cduser@cdnode04 | cdadmin | cduser,passw0rd  | success |
| cduser@cdnode04 | cdadmin | cduser,pass | success |
| cduser@cdnode05 | cdadmin | cduser,passw0rd  | success |
| cduser@cdnode05 | cdadmin | cdremote,1234  | success |
| cduser@cdnode05 | cdadmin | cdremote,passw0rd  | fail |
| cduser@cdnode04 | cdadmin | cdremote,passw0rd  | fail |
| cduser@cdnode05 | cdadmin | cdremote,passw0rd  | success |
| cduser@cdnode05 | cdadmin | cdremote,1234 | success |
| cduser@cdnode05 | cdadmin | cdremote,passw0rd  | fail |

### Removed `cdremote`as Local user in `userfile.cfg`
| Proxy | Local | Snodeid | Result |
| :--- | :---: | :--- | :--- |
| cduser@cdnode05 | cdadmin | cdremote,1234 | fail |
| cduser@cdnode04 | cdadmin | cdremote,1234  | fail |
| cduser@cdnode04 | cdadmin | cdremote,passw0rd | success |
| cduser@cdnode05 | cdadmin | cdremote1,passw0rd  | success |
| cduser@cdnode05 | cdadmin | cdremote1,1234  | success |
| cduser@cdnode05 | cdadmin | cdremote1,xxxx  | fail |
| cduser@cdnode04 | cdadmin | cdremote1,pass  | fail |
| cduser@cdnode05 | cdadmin | cdadmin,pass  | fail |
| cduser@cdnode05 | cdadmin | cdremote1,passw0rd | fail |
| cduser@cdnode05 | cdadmin | cdremote1,1234  | fail |
| abcdef@cdnode4 | cdadmin | abcdef, qwerty | success |
| abcdef@cdnode4 | cdadmin | qwerty, qwerty  | fail |
| abcdef@cdnode4 | cdadmin | qwerty, qwerty  | fail |
| qwerty@cdnode4 | cdadmin | qwerty, qwerty | success |
| qwerty@cdnode4 | cdadmin | qwerty, passw0rd | success |
| *@cdnode4 | cdadmin | qwerty, qwerty  | success |
| *@cdnode5 | cdadmin | qwerty, qwerty  | fail |
| *@cdnode5 | cdadmin | cdadmin, qwerty  | fail |
| *@cdnode4 | cdadmin | cdadmin, qwerty  | success |

In Case of `proxy.attempt=N`
| Proxy | Local | Snodeid | Result |
| :--- | :---: | :--- | :--- |
| *@cdnode4  | cdadmin | cdadmin, qwerty | fail |
| *@cdnode4  | cdadmin | cdadmin, passw0rd | success |
| *@cdnode4  | cdadmin | qwerty, passw0rd | fail |
| qwerty@cdnode4  | cdadmin | qwerty, passw0rd | fail |

### Other Cases:
- #### `proxy.attempt=n`
    - FA  **+** proxy ➡ Validate FA, otherwise _FAIL_
    - Proxy ONLY! ➡ Validate FA first, not there so try proxy next, then _FAIL_
- #### `proxy.attempt=y`
    - FA  **+** proxy ➡ Validate Proxy, otherwise _FAIL_
    - Proxy ONLY! ➡ Validate Proxy, if bad attempt to find FA, then _FAIL_
- #### `proxy.attempt=y` with secure+ enabled
| Proxy | Local | Snodeid | Result |
| :--- | :---: | :--- | :--- |
| qwerty@cdnode5 | cdadmin | qwerty, passw0rd | fail |
| qwerty@cdnode5  | cdadmin | qwerty, qwerty | fail |
| qwerty@cdnode5 | cdadmin | asdfg, asdfg | fail |
| *@cdnode5  | cdadmin | qwerty, qwerty | fail |
| *@cdnode5  | cdadmin | cdadmin, qwerty | fail |
| *@cdnode5  | cdadmin | cdadmin, passw0rd | success |
| qwerty@cdnode5 | cdadmin | qwerty, passw0rd | success |
| qwerty@cdnode5 | cdadmin | qwerty, qwerty | success |
| qwerty@* | cdadmin | qwerty, qwerty| success |