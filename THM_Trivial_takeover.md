# Trivial takeover

## Read before you start!

Read the notes and launch the machine.

Add the machine to our hosts file
```
echo '10.X.X.X trivial.thm' >>/etc/hosts
```
<br>
<br>
<br>
## Enumeration

### What protocol is running on port TCP/179?

TCP scan using nmap
```
nmap -Pn -n -p- -sS trivial.thm

PORT    STATE SERVICE
22/tcp  open  ssh
179/tcp open  bgp

```

_As per the CTF notes no need to brute force in this challenge!_


### Is this a router? (yay or nay)

Machine is running BGP so I would say so!

### What services are running on UDP port 69 and 161 respectively?

UDP scan using nmap
```
nmap -Pn -n -F -sU trivial.thm

PORT    STATE         SERVICE
69/udp  open|filtered tftp
161/udp open          snmp
```
<br>
<br>
<br>
## Walk the walk

### What is the default 'read' community string? 

Google _"default snmp community"_

### What is the System Location string?

SNMP walk the router with the default community and look for sysLocation (OID = iso.3.6.1.2.1.1.6.0)

```
apt install snmp

snmpwalk -v2c -c ****** trivial.thm .
```

## Where are the files?

### Does the supported file transfer protocol support directory listings? (yay or nay)

Google _"does tftp support file browsing"_ for the answer
<br>
<br>
<br>
## First Flag

Enumerate TFTP using nmap

```
nmap -sU -p69 --script tftp-enum.nse trivial.thm

PORT   STATE SERVICE
69/udp open  tftp
| tftp-enum:
|_  *********
```

Then copy the config to attack box

```
apt install tftp

tftp trivial.thm                 
tftp> get *********
tftp> quit  
```

Read the file
```
cat *********
```

....and find the first flag "THM{**************************}"


## Credentials
<br>
<br>
<br>
### The username is....

See if there are any credentials in the file - there are!


### What type (number) is the router using for it's password encryption?  

The password is also in the file and looks like a type number before the ciphertext


## Second Flag
<br>
<br>
<br>
Crack the password

Google _"router type 7 password"_

Log in with credentials and find the second flag

```
ssh ******@trivial.thm

cat flag.txt

THM{***************************}
```

