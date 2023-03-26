# LAB

![image](https://user-images.githubusercontent.com/128924924/227775790-5887f45e-c082-4779-8343-a0436cc9ac34.png)

![image](https://user-images.githubusercontent.com/128924924/227775811-c22c3327-97cd-4793-a79e-2dd9f24bb7d8.png)

R1 want to communicate R2 (inside to outside)

R1
```
int fa0/0
  ip address 192.168.1.1 255.255.255.0
  no shut
  ip route 0.0.0.0 0.0.0.0 192.168.1.100
  ```

R2
```
int fa0/0
  ip address 200.10.10.1 255.255.255.0
  no shut
 ip route 0.0.0.0 0.0.0.0 200.10.10.100
 ```
 Fw
 ```
hostname ASA
  int e0
  ip address 192.168.1.100 255.255.255.0
 no shut
nameif inside
exit
int e3
ip address 200.10.10.100 255.255.255.0
no shut
nameif outside
exit
```
### show commands
```
show nameif = check security level
Show ip 
Show interface ip brief 
```
Then ping R1 to R2 ping not happen why?? 
Because the ping work based on icmp protocol . once R1 ping, hits on firewall and data transfer to R2 then reply back firewalls blocked that is the reason.
2 reason:
1) icmp is stateless
2) in firewall icmp  inspection is not on by default so that connection tables not created

By default icmp protocol is not enabled we need to fix the problem by enable it.

Fw
```
Fixup protocol icmp
```
Now inside can access outside

## We need to communicate DMZ1 to DMZ2 same security level

R4
```
int fa0/0
  ip address 172.16.1.1 255.255.255.0
  no shut
  ip route 0.0.0.0 0.0.0.0  172.16.1.100
```
R3
```
int fa0/0
  ip address 172.16.2.1 255.255.255.0
  no shut
  exit
  ip route 0.0.0.0 0.0.0.0 172.16.2.100
  ```
FW
```
int e1
  ip address 172.16.2.100 255.255.255.0
  no shut
  nameif DMZ2
  security-level 50
  exit
  int e2
  ip address 172.16.1.100 255.255.255.0
  no shut
  nameif DMZ1
  security-level 50
  exit
  ```

Then ping to R4 to R3 , not working ? ? ?  becz same security-level
FW
```
same-security-traffic permit inter-interface
```

Now ping is Working.


## Then want to communicate R2 to R5

![image](https://user-images.githubusercontent.com/128924924/227776216-6cc4b1bd-77a4-4902-b101-9d111c6b4db1.png)

R2 want to reach R5 but traffic enter and leave same interface of firewall gig0/0. Also traffic coming and reaching on same security level 0 .

R5
```
int fa0/0
  ip address 200.10.10.2 255.255.255.0
  no shut
  exit
  ip route 0.0.0.0 0.0.0.0 200.10.10.100
  int loopback 0
  ip address 1.1.1.1 255.255.255.0
  exit
  ```
Now ping R2 to R5 not work ??? we can’t see 1.1.1.1 route in fw .


Fw 
```
route outside 1.1.1.0 255.255.255.0 200.10.10.2
```
Now check ping not happen. because when R2 traffic reach to firewall, Firewall knows the destination but firewall don’t know what to do that traffic.
Fw
```
same-security-traffic permit intra-interface
```

Now ping will successfully reached.









