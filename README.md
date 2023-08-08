# Install [IBM DB2](https://www.ibm.com/docs/en/db2/11.1?topic=administration-db2-data-servers)

This recipe is for deploying the DB2 on Red Hat Enterprise Linux.

### Section A: Subscription Manager

You will need to run this command to verify the subscription status.
```bash
subscription-manager status
```
Then
```bash
yum repolist
```
If you got an error or result of 0 you will need to run into the following steps to setup your `Subscription`

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

