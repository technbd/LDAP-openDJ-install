
## OpenDJ: 

OpenDJ is an LDAPv3 compliant directory service, which has been developed for the Java platform, providing a high performance, highly available, and secure store for the identities managed by your organization. Its easy installation process, combined with the power of the Java platform makes OpenDJ the simplest, fastest directory to deploy and manage.


- **JE backend**: JE Backend which is based on Berkeley DB Java Edition.
- **PDB Backend**: A PDB Backend stores application data in a Persistit database.


A question I often get is “Which backend should I use? “. And I don’t have a definitive answer. If you have an OpenDJ instance and you’re upgrading to 3.0, keep the JE Backend. This is a simple and automated upgrade. 
If you’re installing a new instance of OpenDJ, then I would say it’s a matter of risks. We don’t have the same wide experience with the PDB backend than we have had with the JE backend over the last 10 years. So, if you want to be really safe, chose the JE Backend. If you have time to test, stage your directory service before putting it in production, you might want to go with the PDB Backend. As, moving forward, we will focus our performance testing and improvements on the PDB backend essentially.


### How Does OpenLdap/LDAP Work:

When you enter your credentials, an API call is initiated. If your credentials are correct, (i.e, the LDAP/Directory sever found your credentials to be correct), you will be authenticated and authorized but if not, the call will be denied.

one of the advantages of Openldap/LDAP services is if you have hundreds or thousands of users/servers that needs to access a central server, instead of creating user accounts on individual servers, you can create the users on the sever with security policies you wish, or even put them in a group and every one of the users can login to the server from their servers (clients). openldap is server-client based and makes the job of an administrator easy.


Openldap imitates the DNS structure. For example, `victor.tekneed.com` is a DNS structure and as it is called a fully qualified domain name. More so, `tekneed.com` is a top level domain.

In LDAP, `victor.tekneed.com` is interpreted as;

```
victor=cn (common name)
tekneed=dc (domain component)
.com=dc (domain component)
```

`tekneed.com` is the base context interpreted as (dc=tekneed,dc=com) which users will authenticate with. As we go on in this course, you will get to see how users will authenticate with the base context.





### Prerequisites:
- Java (JDK) version 11 or above installed and JAVA_HOME environment variable set.

- The default for LDAP is `389`, Administration port default is `4444`, SSL: The default port for LDAPS is `636`.



#### Environment: 

- Server IP: 192.168.10.192 (node1)
- Client IP: 192.168.10.191 (node2)




### Download OpenDJ:

Change to the directory where you extracted the OpenDJ files. 

```
mkdir /apps/
cd /apps
```


```
wget https://github.com/OpenRock/OpenDJ/releases/download/3.0.0/OpenDJ-3.0.0.zip

Or,

wget https://github.com/OpenIdentityPlatform/OpenDJ/releases/download/4.5.4/opendj-4.5.4.zip
```


```
unzip OpenDJ-3.0.0.zip
```


```
cd opendj
```


```
echo $JAVA_HOME
```



### Install OpenDJ using CLI mode: 


```
./setup --cli
```


```
### Output: 

What would you like to use as the initial root user DN for the Directory
Server? [cn=Directory Manager]:     [-> Hit Enter]

Please provide the password to use for the initial root user: opendj      [-> Hit Enter]
Please re-enter the password for confirmation: opendj     [-> Hit Enter]

Provide the fully-qualified directory server host name that will be used when
generating self-signed certificates for LDAP SSL/StartTLS, the administration
connector, and replication [node1]: opendj.technbd.net      [-> Hit Enter]

On which port would you like the Directory Server to accept connections from
LDAP clients? [389]: 1389       [-> Hit Enter]

On which port would you like the Administration Connector to accept
connections? [4444]: 2389       [-> Hit Enter]

Do you want to create base DNs in the server? (yes / no) [yes]: yes     [-> Hit Enter]

Provide the backend type:

    1)  JE Backend
    2)  PDB Backend

Enter choice [1]: 1     [-> Hit Enter]

Provide the base DN for the directory data: [dc=example,dc=com]: dc=technbd,dc=net       [-> Hit Enter]
Options for populating the database:

    1)  Leave the database empty
    2)  Only create the base entry
    3)  Import data from an LDIF file
    4)  Load automatically-generated sample data

Enter choice [1]: 2     [-> Hit Enter]

Do you want to enable SSL? (yes / no) [no]: no      [-> Hit Enter]

Do you want to enable Start TLS? (yes / no) [no]: no        [-> Hit Enter]

Do you want to start the server when the configuration is completed? (yes /
no) [yes]: no       [-> Hit Enter]


Setup Summary
=============
LDAP Listener Port:            1389
Administration Connector Port: 4444
JMX Listener Port:
LDAP Secure Access:            disabled
Root User DN:                  cn=Directory Manager
Directory Data:                Backend Type: JE Backend
                               Create New Base DN dc=technbd,dc=net
Base DN Data: Only Create Base Entry (dc=technbd,dc=net)

Do not start Server when the configuration is completed


What would you like to do?

    1)  Set up the server with the parameters above
    2)  Provide the setup parameters again
    3)  Print equivalent non-interactive command-line
    4)  Cancel and exit

Enter choice [1]: 1     [-> Hit Enter]

See /tmp/opendj-setup-4964452117381574923.log for a detailed log of this
operation.

Configuring Directory Server ..... Done.
Creating Base Entry dc=technbd,dc=net ..... Done.

To see basic server configuration status and configuration you can launch
/apps/opendj/bin/status
```




_Set Java Home:_

```
vim config/java.properties


default.java-home=/apps/jdk-1.8/jdk1.8.0_371
```



```
chmod +x -R bin
chmod +x -R lib
```



```
./bin/status -V


OpenDJ 3.0.0
Build 20160109123031
```




```
./bin/start-ds
```


```
./bin/status



### Output: 


>>>> Specify OpenDJ LDAP connection parameters

Administrator user bind DN [cn=Directory Manager]:      [-> Hit Enter]

Password for user 'cn=Directory Manager':       [-> Just Hit Enter (Password no need)]

          --- Server Status ---
Server Run Status:        Started
Open Connections:         0

          --- Server Details ---
Host Name:                node1
Administrative Users:     cn=Directory Manager
Installation Path:        /apps/opendj
Version:                  OpenDJ 3.0.0
Java Version:             <not available> (*)
Administration Connector: Port 4444 (LDAPS)

          --- Connection Handlers ---
Address:Port : Protocol : State
-------------:----------:---------
--           : LDIF     : Disabled
0.0.0.0:636  : LDAPS    : Disabled
0.0.0.0:1389 : LDAP     : Enabled
0.0.0.0:1689 : JMX      : Disabled
0.0.0.0:8080 : HTTP     : Disabled

          --- Data Sources ---
Base DN:     dc=technbd,dc=net
Backend ID:  userRoot
Entries:     <not available> (*)
Replication:

* Information only available if you provide valid authentication information
when launching the status command.
```


```
./bin/stop-ds
```


```
netstat -tlpn | grep 1389
```



```
netstat -tlpn | grep 4444

tcp6       0      0 :::4444                 :::*                    LISTEN      79166/java
```



```
telnet 192.168.10.192 4444
telnet node1 4444

Trying 192.168.10.192...
Connected to node1.
Escape character is '^]'.
```



```
tail -f /apps/opendj/logs/access
```






---
---





## Add a New Entry: 

The `ldapmodify` command is used to modify entries in an LDAP directory. You can use it to add, delete, or update entries by providing an LDIF file with the changes you want to make.

### Step-1: Create a Parent Entry (ou=People)

Prepare an LDIF file (e.g., `parent-entry.ldif`) to create the parent `ou` (organizational unit):

- `-a`: To add a new entry, you use the `-a` option. 
- `-f`: Path to the LDIF file.


```
vim /home/rcms/parent-entry.ldif

dn: ou=People,dc=technbd,dc=net
objectClass: top
objectClass: organizationalUnit
ou: People
```


_Add the Parent Entry:_

```
./bin/ldapmodify -h 192.168.10.192 -p 1389 -D "cn=Directory Manager" -w opendj -a -f /home/rcms/parent-entry.ldif
```


```
### Output:

Processing ADD request for ou=People,dc=technbd,dc=net
ADD operation successful for DN ou=People,dc=technbd,dc=net
```


_Verify the Parent Entry:_

```
./bin/ldapsearch -h 192.168.10.192 -p 1389 -D "cn=Directory Manager" -w opendj -b "dc=technbd,dc=net" "(ou=People)"
```


```
### Output:

dn: ou=People,dc=technbd,dc=net
objectClass: top
objectClass: organizationalUnit
ou: People
```


### Step-2: Prepare an user LDIF File: 

Create a file (e.g., `new-user.ldif`) with the following content:

- `dn`: The distinguished name of the entry. It must include the user's unique ID (`uid`), organizational unit (`ou`), and the base DN.
- `objectClass`: Defines the type of object. Here, we use inetOrgPerson, which is standard for user accounts.
- `cn`: Common name of the user.
- `sn`: Surname (last name).
- `uid`: Unique identifier for the user.
- `userPassword`: The user's password (can be plaintext or hashed).
- `mail`: User's email address.


```
vim /home/rcms/new-user.ldif


dn: uid=jdoe,ou=People,dc=technbd,dc=net
objectClass: top
objectClass: person
objectClass: organizationalPerson
objectClass: inetOrgPerson
cn: John Doe
sn: Doe
uid: jdoe
userPassword: password123
mail: jdoe@technbd.net

```


### Step-3: Import the LDIF File:

```
./bin/ldapmodify -h 192.168.10.192 -p 1389  -D "cn=Directory Manager" -w opendj -a -f /home/rcms/new-user.ldif
```


```
### Output:

Processing ADD request for uid=jdoe,ou=People,dc=technbd,dc=net
ADD operation successful for DN uid=jdoe,ou=People,dc=technbd,dc=net
```


_Verify the User Entry:_
- The output should display the details of the newly created user.

```
./bin/ldapsearch -h 192.168.10.192 -p 1389 -D "cn=Directory Manager" -w opendj -b "dc=technbd,dc=net" "(uid=jdoe)"
```


```
### Output:

dn: uid=jdoe,ou=People,dc=technbd,dc=net
objectClass: organizationalPerson
objectClass: top
objectClass: person
objectClass: inetOrgPerson
uid: jdoe
mail: jdoe@technbd.net
cn: John Doe
sn: Doe
userPassword: {SSHA}vJV3eHmVoKfkkJeFaP3CtiNsA4nNmFCNAUkptQ==
```




## Modify an Existing Entry:

To modify attributes of an existing entry, you provide an LDIF file that specifies the `changetype` as `modify`.

```
vim /home/rcms/modify-user.ldif

dn: uid=jdoe,ou=People,dc=technbd,dc=net 
changetype: modify
replace: mail
mail: john.doe@technbd.net
```


```
./bin/ldapmodify -h 192.168.10.192 -p 1389 -D "cn=Directory Manager" -w opendj -f /home/rcms/modify-user.ldif
```


```
### Output:

Processing MODIFY request for uid=jdoe,ou=People,dc=technbd,dc=net
MODIFY operation successful for DN uid=jdoe,ou=People,dc=technbd,dc=net
```



## Add a New Attribute to an Entry:

To add a new attribute to an existing entry:

```
vim /home/rcms/add-attribute.ldif

dn: uid=jdoe,ou=People,dc=technbd,dc=net
changetype: modify
add: telephoneNumber
telephoneNumber: +1234567890

```


```
./bin/ldapmodify -h 192.168.10.192 -p 1389 -D "cn=Directory Manager" -w opendj -f /home/rcms/add-attribute.ldif
```


```
### Output:

Processing MODIFY request for uid=jdoe,ou=People,dc=technbd,dc=net
MODIFY operation successful for DN uid=jdoe,ou=People,dc=technbd,dc=net
```



## Delete an Attribute from an Entry:
To remove an attribute from an existing entry:

```
vim /home/rcms/delete-attribute.ldif

dn: uid=jdoe,ou=People,dc=technbd,dc=net
changetype: modify
delete: telephoneNumber
```


```
./bin/ldapmodify -h 192.168.10.192 -p 1389 -D "cn=Directory Manager" -w opendj -f /home/rcms/delete-attribute.ldif
```


```
### Output:

Processing MODIFY request for uid=jdoe,ou=People,dc=technbd,dc=net
MODIFY operation successful for DN uid=jdoe,ou=People,dc=technbd,dc=net
```



## Delete an Entry:
To delete an entry, specify the DN and the changetype as delete.

```
vim /home/rcms/delete-entry.ldif

dn: uid=jdoe,ou=People,dc=technbd,dc=net
changetype: delete
```


```
./bin/ldapmodify -h 192.168.10.192 -p 1389 -D "cn=Directory Manager" -w opendj -f /home/rcms/delete-entry.ldif
```


```
### Output:

Processing DELETE request for uid=jdoe,ou=People,dc=technbd,dc=net
DELETE operation successful for DN uid=jdoe,ou=People,dc=technbd,dc=net
```



```
./bin/ldapsearch -h 192.168.10.192 -p 1389 -D "cn=Directory Manager" -w opendj -b "dc=technbd,dc=net" "(objectClass=*)"


### Output:

dn: dc=technbd,dc=net
objectClass: top
objectClass: domain
dc: technbd

dn: ou=People,dc=technbd,dc=net
objectClass: top
objectClass: organizationalUnit
ou: People
```



---
---





## Verify Directory Services: (Basic Search)

The `ldapsearch` command is used to query an LDAP directory.

- `-b` --baseDN :  	base dn for search
- `-D` --bindDN : 	bind DN
- `-h` --hostname :	LDAP server
- `-p` --port :   	port on LDAP server; Default value: 389
- `-w` --bindPassword :  bind password (for simple authentication)
- `-W` --keyStorePassword :	{keyStorePassword} prompt for bind password
- `-X`, --trustAll : Trust all server SSL certificates
- `-Y` --proxyAs {authzID} : Use the proxied authorization control with the given authorization ID
- `-Z` --useSSL : Use SSL for secure communication with the server

- Note: `1389` for non-SSL LDAP, `636` for LDAPS, `4444` for admin port).


```
./bin/ldapsearch --help
```



_Syntax:_

```
./bin/ldapsearch -h <hostname> -p <port> -D <bindDN> -w <password> -b <baseDN> "<search filter>"
```


_Search for all entries under the base DN:_

```
./bin/ldapsearch -h 192.168.10.192 -p 1389 -D "cn=Directory Manager" -w opendj -b "dc=technbd,dc=net" "(objectClass=*)"


### Output:

dn: dc=technbd,dc=net
objectClass: top
objectClass: domain
dc: technbd

dn: ou=People,dc=technbd,dc=net
objectClass: top
objectClass: organizationalUnit
ou: People

dn: uid=jdoe,ou=People,dc=technbd,dc=net
objectClass: organizationalPerson
objectClass: top
objectClass: person
objectClass: inetOrgPerson
uid: jdoe
mail: jdoe@technbd.net
cn: John Doe
sn: Doe
userPassword: {SSHA}vJV3eHmVoKfkkJeFaP3CtiNsA4nNmFCNAUkptQ==
```


Or,


```
./bin/ldapsearch \
  --hostname 192.168.10.192 \
  --port 1389 \
  --bindDN "cn=Directory Manager" \
  --baseDN "dc=technbd,dc=net" \
  --bindPassword opendj \
  "(objectClass=*)"
```




## Search for a Specific User:

Find a user by their unique ID (uid). `uid=jdoe` Searches for the entry with uid equal to `jdoe`.

```
./bin/ldapsearch -h 192.168.10.192 -p 1389 -D "cn=Directory Manager" -w opendj -b "dc=technbd,dc=net" "(uid=jdoe)"
```


```
### Output: 

dn: uid=jdoe,ou=People,dc=technbd,dc=net
objectClass: organizationalPerson
objectClass: top
objectClass: person
objectClass: inetOrgPerson
uid: jdoe
mail: jdoe@technbd.net
cn: John Doe
sn: Doe
userPassword: {SSHA}zqqw7cJR82JdvdHFiKcXagh/74dtSOzqtpRFTg==
```


## Retrieve Specific Attributes:

Limit the attributes returned in the results.

```
./bin/ldapsearch -h 192.168.10.192 -p 1389 -D "cn=Directory Manager" -w opendj -b "dc=technbd,dc=net" "(uid=jdoe)" cn mail
```


```
### Output: 

dn: uid=jdoe,ou=People,dc=technbd,dc=net
cn: John Doe
mail: jdoe@technbd.net
```




## Search with a Logical Filter:

Find entries that match multiple conditions (e.g., `cn=John` and `sn=Doe`).
- `(&(...)(...))` : Combines filters with a logical AND.


```
./bin/ldapsearch -h 192.168.10.192 -p 1389 -D "cn=Directory Manager" -w opendj -b "dc=technbd,dc=net" "(&(cn=John)(sn=Doe))"

Or,

./bin/ldapsearch -h 192.168.10.192 -p 1389 -D "cn=Directory Manager" -w opendj -b "dc=technbd,dc=net" "(&(cn=John*)(sn=Doe))"
```


```
### Output: 

```



## Search Using Wildcards:

Find all users whose `mail` attribute ends with `@technbd.net`.

```
./bin/ldapsearch -h 192.168.10.192 -p 1389 -D "cn=Directory Manager" -w opendj -b "dc=technbd,dc=net" "(mail=*@technbd.net)"
```


```
### Output: 

dn: uid=jdoe,ou=People,dc=technbd,dc=net
objectClass: organizationalPerson
objectClass: top
objectClass: person
objectClass: inetOrgPerson
uid: jdoe
mail: jdoe@technbd.net
cn: John Doe
sn: Doe
userPassword: {SSHA}zqqw7cJR82JdvdHFiKcXagh/74dtSOzqtpRFTg==
```



## Search for Users Only:
- Find all entries of type `inetOrgPerson`.

```
./bin/ldapsearch -h 192.168.10.192 -p 1389 -D "cn=Directory Manager" -w opendj -b "dc=technbd,dc=net" "(objectClass=inetOrgPerson)"
```



```
### Output: 

dn: uid=jdoe,ou=People,dc=technbd,dc=net
objectClass: organizationalPerson
objectClass: top
objectClass: person
objectClass: inetOrgPerson
uid: jdoe
mail: jdoe@technbd.net
cn: John Doe
sn: Doe
userPassword: {SSHA}zqqw7cJR82JdvdHFiKcXagh/74dtSOzqtpRFTg==
```



## Search with a Size Limit: 
Limit the number of results returned.
- `--sizeLimit 5`: Limits results to 5 entries.

```
./bin/ldapsearch -h 192.168.10.192 -p 1389 -D "cn=Directory Manager" -w opendj -b "dc=technbd,dc=net" "(objectClass=*)" --sizeLimit 5
```


```
### Output: 

dn: dc=technbd,dc=net

dn: ou=People,dc=technbd,dc=net

dn: uid=jdoe,ou=People,dc=technbd,dc=net

```



## Search for Locked or Disabled Accounts: 
Search for users with a specific operational state, such as locked accounts.

```
./bin/ldapsearch -h 192.168.10.192 -p 1389 -D "cn=Directory Manager" -w opendj -b "dc=technbd,dc=net" "(pwdAccountLockedTime=*)"
```





## Search with LDAPS (Secure): 

- `-p 636`: Port for LDAPS.
- `-Z`: Enforce encryption.
- `--trustAll`: Trust the server’s certificate (useful for self-signed certificates).


```
./bin/ldapsearch -h 192.168.10.192 -p 636 -D "cn=Directory Manager" -w opendj -b "dc=technbd,dc=net" "(uid=jdoe)" -Z --trustAll
```








---
---






## OpenDJ Control Panel:

OpenDJ Control Panel offers a graphical user interface for managing both local and remote servers. You choose the server to manage when you start the Control Panel. The Control Panel connects to the administration server port, making a secure LDAPS connection.


_Start OpenDJ Control Panel:_ 
- (UNIX) Run `./bin/control-panel` 
- (Windows) Double-click `bat\control-panel.bat`



_Install `X11` Packages: On the server:_

```
yum install xorg-x11-xauth xorg-x11-utils xorg-x11-fonts* -y

yum install xclock -y
yum install xhost -y
```


```
export DISPLAY=192.168.10.44:0

echo $DISPLAY
```


```
xclock
```



```
./bin/control-panel
```



#### Error: 
```
Could not connect to the server. check that the server is running. details: connect error: the connection attempt to server/0.0.0.0:4444 has failed because the connection timeout period of 30000 ms was exceeded.


an error occurred connecting to the server. details: javax.naming.communicationException: simple bind failed: 0.0.0.0:4444 [Root exception is javax.net.ssl.SSLHandshakeException: no appropriate protocol (protocol is disabled or cipher suites are inappropriate)]

```





```
./bin/dsconfig get-connection-handler-prop \
  --handler-name "LDAPS Connection Handler" \
  --hostname node1 \
  --port 4444 \
  --bindDN "cn=Directory Manager" \
  --bindPassword opendj \
  --trustAll
```


```
### Output:

Unable to connect to the server at "node1" on port 4444
```




---
---




## Enable Secure Connections (Optional)

- Verify LDAPS Port: Ensure port 636 is open and listening. If port 636 is not listed, the server is not properly configured for LDAPS.
- If you are using a self-signed certificate, ensure the `--trustAll` option is used with `ldapsearch`.



```
./bin/ldapsearch -h 192.168.10.192 -p 636 -D "cn=Directory Manager" -w opendj -b "dc=technbd,dc=net" "(uid=jdoe)" -Z --trustAll
```


_Verify Certificate Validity:_

```
openssl s_client -connect 192.168.10.192:636
```




_Check if LDAPS is Enabled on the Server:_

```
./bin/dsconfig list-connection-handlers -h 192.168.10.192 -p 4444 --bindDN "cn=Directory Manager" --bindPassword opendj --trustAll
```


_If it's disabled, enable it:_

```
./bin/dsconfig set-connection-handler-prop \
  --handler-name "LDAPS Connection Handler" \
  --set enabled:true \
  --hostname 192.168.10.192 \
  --port 4444 \
  --bindDN "cn=Directory Manager" \
  --bindPassword opendj \
  --trustAll
```



```
### Output:

Unable to connect to the server at "192.168.10.192" on port 4444

Unable to connect to the server at "node1" on port 4444
```







### Links:
- [openDJ Getting Started](https://forgerock.github.io/opendj-community-edition/)
- [Installing OpenDJ Servers](https://backstage.forgerock.com/docs/opendj/3/install-guide/#before-you-install)
- [OpenIdentityPlatform | github.com](https://github.com/OpenIdentityPlatform/OpenDJ)
- [OpenIdentityPlatform releases](https://github.com/OpenIdentityPlatform/OpenDJ/releases)
- [OpenRock | github.com](https://github.com/OpenRock/OpenDJ)







































