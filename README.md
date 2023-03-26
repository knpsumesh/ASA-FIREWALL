# ACL IN ASA-FIREWALL


# LAB

![image](https://user-images.githubusercontent.com/128924924/227759106-edf69c6d-3540-4793-81ca-e80bf3cae375.png)

We want to communicate outside to inside via telnet

R1

```
  int fa0/1
  ip address 192.168.1.1 255.255.255.0
  no shut
  ip route 0.0.0.0 0.0.0.0 192.168.1.100
  exit
```
  
R2
```
int fa0/0
ip address 200.10.10.1 255.255.255.0
no shut
ip route 0.0.0.0 0.0.0.0 200.10.10.100
exit
```

FW
```
  hostname ASA
  int e0
  ip address 192.168.1.100 255.255.255.0
  nameif inside
  no shut
  exit
  int e1
  ip address 200.10.10.100 255.255.255.0
  nameif outside
  no shutdown
 ``` 
Do telnet for access R1 to R2

R1
``
line vty 0 4
  password cisco
  login
```  
R2
telnet 192.168.1.1  not work 
with the help of ACL we do the transfer from out to in.
FW
access-list myacl extended permit tcp host 200.10.10.1 host 192.168.1.1 eq 23	
exit
access-group myacl in interface outside

now ping telnet 192.168.1.1  its work.

(reference:) how we can allow ICMP traffic in all interface help of ACL
access-list myacl2 extended permit icmp any any
access-group myacl2 global

Now ping R2 to R1 its work becz icmp is enabled
