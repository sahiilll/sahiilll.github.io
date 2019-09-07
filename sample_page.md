## DHCP STARVATION ATTACK 

**What is dhcp starvation attack** DHCP Starvation attack is a type of denial of service attack where the victim machine will
not be able to obtain IP from dhcp server on the network. The attacker sends series of DHCP request packets for IPs available in dhcp pool for bogus MAC addresses. The dhcp server then give acknowledgement for all the packets which have requested IPs that are not allotted to any machine and available in DHCP IP pool. Thus the dhcp registers that IP with some bogus MAC address in dhcp.leases file for the time defined by lease time. Any machine when tries to connect to the network and ask for IP. The dhcp won’t be able to give that IP to the
machine. 

### 1. Tools to be used

Scapy – Scapy is a package in python which is used to send and receive manipulated network packets wrapped in any one of the layers of OSI model. It is able to forge or decode packets of a wide number of protocols, send them on the wire, capture them, match requests and
replies

```javascript
if (isAwesome){
  return true
}
```

### 2. Algorithm 

1. Two different threads has been formed 1) one for sending the packets and 2) another
  one for sniffing on the interface and receiving the packets.
2. Send() : the function attempts to send dhcp packets with random bogus MAC address.
  The for loop is used to iterate over all possible IPs in IP pool. Sendp() function is
  from the library which is used to send the wrapped data in all layers.
3. Receive(): the function is defined with sniff function in it which takes care of any
  incoming and outgoing packets on any of the interfaces on machine. Here filter is et
  for the dhcp packets as “udp and port 67 and 68”
4. Dhcp_parser(): function is called whenever the sniff function encounters the packet
  which satisfies the filter and calls this function. The function then attemots to read the
  options of received packet to check if the ACK is received or NACK is received.
5. The threads will be active till the point when send() and receive stops sending and
  receiving packets. 

### 3. What you get after you run the program

Vim tool is used to open the file and any IP registration which were there already has been
deleted to empty the IP pool. The dhcpd.leases is maintained to register any IP for any MAC
for specified amount of time. 
<img src="images/dhcp1.jpg?raw=true"/>

root@kali:#/ python dhcpStarvation.py
The above command is used to launch the dhcp starvation attack and run the script on the machine.
The program outputs if ACK is received for the packet or NACK is received.

<img src="images/dhcp2.jpg?raw=true"/>

### 4. Provide a basis for further data collection through surveys or experiments

Sed ut perspiciatis unde omnis iste natus error sit voluptatem accusantium doloremque laudantium, totam rem aperiam, eaque ipsa quae ab illo inventore veritatis et quasi architecto beatae vitae dicta sunt explicabo. 

For more details see [GitHub Flavored Markdown](https://guides.github.com/features/mastering-markdown/).
