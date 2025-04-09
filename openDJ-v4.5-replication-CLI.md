
## OpenDJ Replication v4.5:

In OpenDJ replication, **there is no fixed "master" or "primary" server**. OpenDJ employs **multi-master replication**, which means that all replicated servers are peers, and any of them can accept write operations. Changes made on one server are automatically propagated to the others in the replication topology.


### Key Concepts in Multi-Master Replication:
- Equality of Servers:
    - All servers in the replication topology are considered equal and can accept both read and write operations.

- Conflict Resolution:
    - OpenDJ uses conflict resolution mechanisms (e.g., timestamps) to handle concurrent changes to the same data across multiple servers.

- Primary Use Cases:
    - Multi-master replication is designed for high availability and load balancing. Clients can connect to any server to perform operations.

- Administrative Nodes (not "Master"):
    - During configuration, you often use one server as a reference to enable replication or add new servers to the topology, but this server doesn't hold a special "master" role afterward.


### Prerequisites:

- OpenDJ installed and running on both servers.
- The same base DN (e.g., dc=rcms,dc=com) on both servers.
- Ports 8989, 4444, and 1389 (LDAP) or 636 (LDAPS) open between servers.




#### Environment: 

- Server-1: 192.168.10.192
- Server-2: 192.168.10.193


_Set local DNS Entry:_

```
cat /etc/hosts

192.168.10.193  localvm01.technbd.net     localvm01
192.168.10.192  localvm02.technbd.net     localvm02
```



## Configure or Enable Replication on Each Server:

#### Requires Info:

```
FQHN: localvm01.technbd.net
FQHN: localvm02.technbd.net

LDAP Listener Port: 1389
Administrator port: 4444

Bind DN: cn=Manager,dc=rcms,dc=com
Base DN: dc=rcms,dc=com

Root User DN: cn=Manager,dc=rcms,dc=com
password: your_password
```


### On Server-1 (192.168.10.193):

Use the `dsreplication` command to `enable` replication on each server:

```
./bin/dsreplication enable \
--host1 localvm01.technbd.net --port1 4444 --bindDN1 "cn=Manager,dc=rcms,dc=com" --bindPassword1 secret \
--host2 localvm02.technbd.net --port2 4444 --bindDN2 "cn=Manager,dc=rcms,dc=com" --bindPassword2 secret \
--replicationPort1 8989 --replicationPort2 8989 \
--baseDN "dc=rcms,dc=com" \
--adminUID admin --adminPassword admin123 \
--trustAll --no-prompt
```



```
### Output:

Establishing connections ..... Done.
Checking registration information ..... Done.
Configuring Replication port on server localvm02.technbd.net:4444 ..... Done.
Configuring Replication port on server localvm01.technbd.net:4444 ..... Done.
Updating replication configuration for baseDN dc=rcms,dc=com on server
localvm02.technbd.net:4444 .....Done.
Updating replication configuration for baseDN dc=rcms,dc=com on server
localvm01.technbd.net:4444 .....Done.
Updating registration configuration on server localvm02.technbd.net:4444 ..... Done.
Updating registration configuration on server localvm01.technbd.net:4444 ..... Done.
Updating replication configuration for baseDN cn=schema on server
localvm02.technbd.net:4444 .....Done.
Updating replication configuration for baseDN cn=schema on server
localvm01.technbd.net:4444 .....Done.
Initializing registration information on server localvm01.technbd.net:4444
with the contents of server localvm02.technbd.net:4444 .....Done.
Initializing schema on server localvm01.technbd.net:4444 with the contents of
server localvm02.technbd.net:4444 .....Done.
The clocks of servers localvm02.technbd.net:4444 and
localvm01.technbd.net:4444 have a difference superior to 5 minutes.
Replication does not require clocks to be synchronized but monitoring of
replication updates between servers can be difficult.

Replication has been successfully enabled.  Note that for replication to work
you must initialize the contents of the base DNs that are being replicated
(use dsreplication initialize to do so).


See /tmp/opendj-replication-3820312226406038630.log for a detailed log of this
operation.

```



```
./bin/status



### Output: 

>>>> Specify OpenDJ LDAP connection parameters

Administrator user bind DN [cn=Directory Manager]: cn=Manager,dc=rcms,dc=com

Password for user 'cn=Manager,dc=rcms,dc=com': secret

          --- Server Status ---
Server Run Status:        Started
Open Connections:         1

          --- Server Details ---
Host Name:                localvm02.technbd.net
Administrative Users:     cn=Manager,dc=rcms,dc=com
Installation Path:        /root/opendj
Version:                  OpenDJ Server 4.5.4
Java Version:             1.8.0_371
Administration Connector: Port 4444 (LDAPS)

          --- Connection Handlers ---
Address:Port : Protocol    : State
-------------:-------------:---------
--           : LDIF        : Disabled
8989         : Replication : Enabled
0.0.0.0:636  : LDAPS       : Disabled
0.0.0.0:1389 : LDAP        : Enabled
0.0.0.0:1689 : JMX         : Disabled
0.0.0.0:8080 : HTTP        : Disabled

          --- Data Sources ---
Base DN:                      dc=rcms,dc=com
Backend ID:                   userRoot
Entries:                      0
Replication:                  Enabled
Missing Changes:              <not available>
Age of Oldest Missing Change: <not available>

```



### On Server-2 (192.168.10.192):

```
./bin/dsreplication enable \
--host1 localvm02.technbd.net --port1 4444 --bindDN1 "cn=Manager,dc=rcms,dc=com" --bindPassword1 secret \
--host2 localvm01.technbd.net --port2 4444 --bindDN2 "cn=Manager,dc=rcms,dc=com" --bindPassword2 secret \
--replicationPort1 8989 --replicationPort2 8989 \
--baseDN "dc=rcms,dc=com" \
--adminUID admin --adminPassword admin123 \
--trustAll --no-prompt
```



```
### Output:


Establishing connections ..... Done.

There are no base DNs available to enable replication between the two servers.

The following base DNs are already replicated between the two servers:
dc=rcms,dc=com
```



```
./bin/status


>>>> Specify OpenDJ LDAP connection parameters

Administrator user bind DN [cn=Directory Manager]: cn=Manager,dc=rcms,dc=com

Password for user 'cn=Manager,dc=rcms,dc=com':

          --- Server Status ---
Server Run Status:        Started
Open Connections:         1

          --- Server Details ---
Host Name:                localvm01.technbd.net
Administrative Users:     cn=Manager,dc=rcms,dc=com
Installation Path:        /root/opendj
Version:                  OpenDJ Server 4.5.4
Java Version:             1.8.0_201
Administration Connector: Port 4444 (LDAPS)

          --- Connection Handlers ---
Address:Port : Protocol    : State
-------------:-------------:---------
--           : LDIF        : Disabled
8989         : Replication : Enabled
0.0.0.0:636  : LDAPS       : Disabled
0.0.0.0:1389 : LDAP        : Enabled
0.0.0.0:1689 : JMX         : Disabled
0.0.0.0:8080 : HTTP        : Disabled

          --- Data Sources ---
Base DN:                      dc=rcms,dc=com
Backend ID:                   userRoot
Entries:                      0
Replication:                  Enabled
Missing Changes:              <not available>
Age of Oldest Missing Change: <not available>

```




### If Add Additional Servers to the Replication: (Server-3)

_To add a third server (Server 3):_

```
./bin/dsreplication enable \
--host1 localvm01.technbd.net --port1 4444 --bindDN1 "cn=Manager,dc=rcms,dc=com" --bindPassword1 secret \
--host2 localvm03.technbd.net --port2 4444 --bindDN2 "cn=Manager,dc=rcms,dc=com" --bindPassword2 secret \
--replicationPort1 8989 --replicationPort2 8989 \
--baseDN "dc=rcms,dc=com" \
--adminUID admin --adminPassword admin123 \
--trustAll --no-prompt
```



### Verify Replication Status:


#### On Server-1: 

```
./bin/dsreplication status \
--hostname localvm01.technbd.net --port 4444 \
--adminUID admin --adminPassword admin123 \
--trustAll
```



```
### Output:


> --hostname localvm01.technbd.net --port 4444 \
> --adminUID admin --adminPassword admin123 \
> --trustAll
Suffix DN      : Server                     : Entries : Replication enabled : DS ID : RS ID : RS Port (1) : M.C. (2) : A.O.M.C. (3) : Security (4)
---------------:----------------------------:---------:---------------------:-------:-------:-------------:----------:--------------:-------------
dc=rcms,dc=com : localvm01.technbd.net:4444 : 0       : true                : 13921 : 19312 : 8989        : 0        :              : false
dc=rcms,dc=com : localvm02.technbd.net:4444 : 0       : true                : 17255 : 4456  : 8989        : 0        :              : false

[1] The port used to communicate between the servers whose contents are being
replicated.
[2] The number of changes that are still missing on this server (and that have
been applied to at least one of the other servers).
[3] Age of oldest missing change: the date on which the oldest change that has
not arrived on this server was generated.
[4] Whether the replication communication through the replication port is
encrypted or not.
```




```
./bin/dsreplication status \
  --hostname localvm01.technbd.net --port 4444 \
  --adminUID admin --adminPassword admin123 \
  --baseDN "dc=rcms,dc=com" \
  --trustAll
```






#### On Server-2:

```
./bin/dsreplication status \
--hostname localvm02.technbd.net --port 4444 \
--adminUID admin --adminPassword admin123 \
--trustAll
```



```
### Output:


> --hostname localvm02.technbd.net --port 4444 \
> --adminUID admin --adminPassword admin123 \
> --trustAll
Suffix DN      : Server                     : Entries : Replication enabled : DS ID : RS ID : RS Port (1) : M.C. (2) : A.O.M.C. (3) : Security (4)
---------------:----------------------------:---------:---------------------:-------:-------:-------------:----------:--------------:-------------
dc=rcms,dc=com : localvm01.technbd.net:4444 : 0       : true                : 13921 : 19312 : 8989        : 0        :              : false
dc=rcms,dc=com : localvm02.technbd.net:4444 : 0       : true                : 17255 : 4456  : 8989        : 0        :              : false

[1] The port used to communicate between the servers whose contents are being
replicated.
[2] The number of changes that are still missing on this server (and that have
been applied to at least one of the other servers).
[3] Age of oldest missing change: the date on which the oldest change that has
not arrived on this server was generated.
[4] Whether the replication communication through the replication port is
encrypted or not.
```



### Test Replication:

#### Create the Base DN (dc=rcms,dc=com): (If Need)

```
vim /home/base.ldif

dn: dc=rcms,dc=com
objectClass: top
objectClass: domain
dc: rcms
```


```
./bin/ldapadd -h localvm01.technbd.net -p 1389 -D "cn=Manager,dc=rcms,dc=com" -w secret -f /home/base.ldif
```


_Verify the Entry:_

```
./bin/ldapsearch -h localvm01.technbd.net -p 1389 -D "cn=Manager,dc=rcms,dc=com" -w secret -b "dc=rcms,dc=com" "(objectClass=*)"
```




#### On Server-1:

_Now that the base DN is created, try adding testuser:_

```
./bin/ldapmodify -h localvm01.technbd.net -p 1389 -D "cn=Manager,dc=rcms,dc=com" -w secret <<EOF
dn: uid=testuser,dc=rcms,dc=com
objectClass: inetOrgPerson
uid: testuser
cn: Test User
sn: User
mail: testuser@example.com
EOF
```




_If Modify:_

```
vim /home/modify.ldif

dn: uid=testuser,dc=rcms,dc=com
changetype: modify
replace: mail
mail: newemail@example.com
```



```
./bin/ldapmodify -h localvm01.technbd.net -p 1389 -D "cn=Manager,dc=rcms,dc=com" -w secret -f /home/modify.ldif
```




#### On Server-2: (Verify replication)

```
./bin/ldapsearch -h localvm02.technbd.net -p 1389 -D "cn=Manager,dc=rcms,dc=com" -w secret -b "dc=rcms,dc=com" "(uid=testuser)"
```





### Monitor Replication:

_To continuously monitor replication:_


```
watch "./bin/dsreplication status --hostname localvm02.technbd.net --port 4444 --bindDN "cn=Manager,dc=rcms,dc=com" --bindPassword 'secret' --baseDN dc=rcms,dc=com"
```



```
./bin/ldapsearch -h 192.168.10.192 -p 1389 -D "cn=Manager,dc=rcms,dc=com" -w secret -b "cn=replication,cn=monitor" "(objectclass=*)"

./bin/ldapsearch -h 192.168.10.193 -p 1389 -D "cn=Manager,dc=rcms,dc=com" -w secret -b "cn=replication,cn=monitor" "(objectclass=*)"
```




### To remove replication: 


#### Check Current Replication Status: 

```
./bin/dsreplication status \
--hostname localvm01.technbd.net --port 4444 \
--adminUID admin --adminPassword admin123 \
--trustAll
```



#### Disable Replication for `localvm02.technbd.net`: 


_**To Disable All Replication** on the Server: Use `--disableAll` without the `--baseDN` argument:_

```
./bin/dsreplication disable \
  --hostname localvm02.technbd.net --port 4444 \
  --adminUID admin --adminPassword admin123 \
  --disableAll \
  --trustAll --no-prompt

```




```
### output:


Establishing connections ..... Done.
You have decided to disable the replication server (replication changelog).
After disabling the replication server only one replication server will be
configured for the following suffixes:
dc=rcms,dc=com
To avoid a single point of failure at least two replication servers must be
configured.
Disabling replication on base DN cn=admin data of server
localvm01.technbd.net:4444 .....Done.
Disabling replication on base DN dc=rcms,dc=com of server
localvm01.technbd.net:4444 .....Done.
Disabling replication on base DN cn=schema of server
localvm01.technbd.net:4444 .....Done.
Removing references on base DN cn=admin data of server
localvm02.technbd.net:4444 .....Done.
Removing references on base DN cn=schema of server localvm02.technbd.net:4444
.....Done.
Removing references on base DN dc=rcms,dc=com of server
localvm02.technbd.net:4444 .....Done.
Disabling replication port 8989 of server localvm01.technbd.net:4444 ..... Done.
Removing registration information ..... Done.
```



_**To Disable Replication for a Specific Host** Base DN: If you want to disable replication only for a specific base DN, use `--baseDN` without the `--disableAll` argument:_

```
./bin/dsreplication disable \
  --hostname localvm02.technbd.net --port 4444 \
  --adminUID admin --adminPassword admin123 \
  --baseDN "dc=rcms,dc=com" \
  --trustAll --no-prompt
```


```
### Output:


Establishing connections ..... Done.
You have chosen to disable replication on all the replicated base DNs of
'localvm02.technbd.net:4444'.  If you want also the replication server
(changelog and replication port) to be disabled you must also specify the
'--disableReplicationServer' or '--disableAll' option.
Disabling replication on base DN dc=rcms,dc=com of server
localvm02.technbd.net:4444 .....Done.
Removing references on base DN dc=rcms,dc=com of server
localvm01.technbd.net:4444 .....Done.
```



```
./bin/dsreplication status --hostname localvm01.technbd.net --port 4444 --adminUID admin --adminPassword admin123 --trustAll 

Suffix DN      : Server                     : Entries : Replication enabled : DS ID : RS ID : RS Port (1) : M.C. (2) : A.O.M.C. (3) : Security (4)
---------------:----------------------------:---------:---------------------:-------:-------:-------------:----------:--------------:-------------
dc=rcms,dc=com : localvm01.technbd.net:4444 : 2       : true                : 17184 : 3120  : 8989        : 0        :              : false
dc=rcms,dc=com : localvm02.technbd.net:4444 : 2       :                     :       :       :             :          :              :
```



_`Note`: If you want to fully disable the replication server (including the changelog and replication port), you can use the following command:_

```
./bin/dsreplication disable \
  --hostname localvm01.technbd.net --port 4444 \
  --adminUID admin --adminPassword admin123 \
  --baseDN "dc=rcms,dc=com" \
  --disableReplicationServer \
  --trustAll --no-prompt
```


```
### Output:

Establishing connections ..... Done.
Disabling replication on base DN cn=admin data of server
localvm01.technbd.net:4444 .....Done.
Disabling replication on base DN cn=schema of server
localvm01.technbd.net:4444 .....Done.
Removing references on base DN cn=admin data of server
localvm02.technbd.net:4444 .....Done.
Removing references on base DN cn=schema of server localvm02.technbd.net:4444
.....Done.
Disabling replication port 8989 of server localvm01.technbd.net:4444 ..... Done.

```




#### Reinitialize Replication:

```
./bin/dsreplication enable \
  --host1 localvm01.technbd.net --port1 4444 \
  --host2 localvm02.technbd.net --port2 4444 \
  --adminUID admin --adminPassword admin123 \
  --baseDN "dc=rcms,dc=com" \
  --trustAll --no-prompt
```



```
### Output:


Establishing connections ..... Done.
Checking registration information ..... Done.
Updating remote references on server localvm01.technbd.net:4444 ..... Done.
Updating remote references on server localvm02.technbd.net:4444 ..... Done.
Updating replication configuration for baseDN dc=rcms,dc=com on server
localvm01.technbd.net:4444 .....Done.
Updating replication configuration for baseDN dc=rcms,dc=com on server
localvm02.technbd.net:4444 .....Done.
Updating replication configuration for baseDN cn=schema on server
localvm01.technbd.net:4444 .....Done.
Updating replication configuration for baseDN cn=schema on server
localvm02.technbd.net:4444 .....Done.
Updating replication configuration for baseDN cn=schema on server
localvm02.technbd.net:4444 .....Done.
The clocks of servers localvm01.technbd.net:4444 and
localvm02.technbd.net:4444 have a difference superior to 5 minutes.
Replication does not require clocks to be synchronized but monitoring of
replication updates between servers can be difficult.

Replication has been successfully enabled.  Note that for replication to work
you must initialize the contents of the base DNs that are being replicated
(use dsreplication initialize to do so).
```



Or,


```
./bin/dsreplication enable \
  --host1 localvm01.technbd.net --port1 4444 \
  --bindDN1 "cn=Manager,dc=rcms,dc=com" --bindPassword1 'admin123' \
  --host2 localvm02.technbd.net --port2 4444 \
  --bindDN2 "cn=Manager,dc=rcms,dc=com" --bindPassword2 'admin123' \
  --replicationPort1 8989 --replicationPort2 8989 \
  --baseDN "dc=rcms,dc=com" \
  --adminUID admin --adminPassword admin123 \
  --trustAll --no-prompt
```





```
### Output:

Establishing connections ..... Done.
Checking registration information ..... Done.
Updating remote references on server localvm01.technbd.net:4444 ..... Done.
Updating remote references on server localvm02.technbd.net:4444 ..... Done.
Updating replication configuration for baseDN dc=rcms,dc=com on server
localvm01.technbd.net:4444 .....Done.
Updating replication configuration for baseDN dc=rcms,dc=com on server
localvm02.technbd.net:4444 .....Done.
Updating replication configuration for baseDN cn=schema on server
localvm01.technbd.net:4444 .....Done.
Updating replication configuration for baseDN cn=schema on server
localvm02.technbd.net:4444 .....Done.
Updating replication configuration for baseDN cn=schema on server
localvm02.technbd.net:4444 .....Done.
The clocks of servers localvm01.technbd.net:4444 and
localvm02.technbd.net:4444 have a difference superior to 5 minutes.
Replication does not require clocks to be synchronized but monitoring of
replication updates between servers can be difficult.

Replication has been successfully enabled.  Note that for replication to work
you must initialize the contents of the base DNs that are being replicated
(use dsreplication initialize to do so).
```



You have successfully configured OpenDJ replication on two servers! Now, both servers will automatically synchronize directory data.

