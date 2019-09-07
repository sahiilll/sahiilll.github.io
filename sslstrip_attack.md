## Introduction

The article demonstrates the sslstrip attack which is a kind of man-in-the-middle attack but It also helps the
attacker strips out the ssl configuration of the connection. The attacker acts as a proxy which sits in between the
connection and make a https website into https website.
The vulnerability was found in around 2002 within the field of “Basic Constraints” in SSL Certificate. The attack is
vulnerable to those websites which supports both http and https connection or the website contains some
redirection link to https or some component which supports https. In this practical arp cache poisoning was
taken place to aim MITM.  

### A little background theory

**ARP protocol** is a address resolution protocol used by the hosts to map the physical address with a logical
address when requested by the hosts. There are two main types of arp packet used in protocol. One is arp reply
and another is arp request. Arp request is sent when the sender has IP address and wants to send the packet
over network through physical address. The sender broadcast this arp request on the network and every hosts
on the network receives this request.

**Arp reply** is sent when the hosts receives the arp request packet. Arp reply packet is sent by the hosts who has
the ip address which is mentioned in arp request packet and every other hosts just ignore the packet. The arp is
unicasted to the sender with the knowledge of source ip address and source hardware address mentioned in arp
request packet. The sender receives this arp reply and make an entry to the arp cache for future reference. Arp
cache is maintained so that the sender does not have to request for the packet every time it wants to send the
packet to the same sender.

**Arp cache poisoning** is a sort of Man-in-the-middle attack used by an attacker to sniff the outbound and inbound
traffic from the victim machine. In this attack, the attacker sends falsified arp packets which tells the victim that
server ip is associated with attackers mac address. The attacker aceheives this by sending out arp replies
continuously with some delay. The victim notices this, make a new entry corresponding to this mac.
So from now on whenever the attacker wants to send the data to server ip, it looks up for in its arp cache for
associated mac (which is already poisoned by the attacker), and sends data to the attacker machine instead of
server. This lets an attacker to sniff on the communication between the server ip and victim machine. Attacker
can follow the same steps for the arp poising attack on server.

This will result in attacker to track all communication between the two machines, both inbound and outbound,
and can also alter the contents of packet being sent form one machine to another machine. If in some way, the
attacker gets successful in bypassing the authentication and verification of integrity of the message, the receiver
won’t be able to know the modified packet and accepts the packet. 

## SSL certificates 
SSL Certficates are digital documents that contains some cryptography key of an organization and needed
by an organization to install this document on their web server to start a connection which is secure
providing the authentication mechanism for client. SSL certificates are mostly issued by some trusted third
party certificate authority (Root Certificates)

## SSL Strip

SSLStrip is an attack which is based on the vulnerability found in around year 2002. Basically, that time the
certificates have a field “Basic Constraints” which used to tell the client the other leaf or end certificates it
supports with it or what certificate should be allowed to do.
In this case if an attacker gets itself register and obtain certificate from some trusted third party issuer, he
can then mention its fake bank website as a trusted one and can direct user to it with https connection. In
this way attacker sits in between client and website and encrypts the message going to the c lient and
decrypts the message coming from client.

In this article, we will show an another method to do MITM attack through sslstrip. It leverages the
vulnerability where websites supports bith https and http connection. When the http website has some
component which refers to https link, we can trace the login or confidential information through the tool
“SSLStrip” which comes in kali.
What it does, is , watch http traffic from client and switches the http link references to https links and vice
versa for server packet. . 

### Algorithm
1. main() : It forms two threads and execute the function receive() and arpattack() with these two different
            threads.
2. Arpattack(): This is where the actual attack execution happens. arpattack() first attempts to calls the
                 function get_mac() to find out the actual mac addresses of the two machines. Then it checks if the hosts
                 is actually alive or not in the if conditions by comparing the value of macs with None. If the hosts are not
                 alive, they will return None. The while is executed which will run for infinite number of times until the
                 user forcefully stops its execution by CTRL + Z. Inside the while loop, it will send the ARP packets by
                 using the send() function defined by scapy library with the delay of 2 seconds.
3. get_mac(): The function attempts to send out the broadcast of arp request packet for the IPs and save
              their mac addresses from the response packets. If the hosts are not alive, it will return None.
4. receive(): it will sniff on the packets sent and receive and output the sniffer.pcap file with result
              returned by sniff function
              
### Experiement
Following steps are followed to execute the attack:
1. The machines were opened in the order as mentioned in instructions. ExtRouter->Kali->Windows
2. Input: Provided that we know the IP address of the router and victim machine, we run the script with
          hardcoded IPs. No input or arguments are required at the time run.
3. Command: Since it’s a python file, we will execute the code by entering python <filename.py> on the
            terminal
4. Output: it’s a continuous logging of the arp requests and replies on the terminal. 

ARP Attack script
Below is the screenshot showing the output of the script. We can track the progress of the arp attack by seeing
how many packets have been sent by the attacker

<img src="images/ssl1.PNG?raw=true"/>

Arp command verification:
As a result we were able to poison the arp cache of the victim machine and client machine. The results are
shown below.

<img src="images/ssl2.PNG?raw=true"/>

It can be seen that the same MAC addresses is registered for the Router’s IP and the attackers machine.

<img src="images/ssl3.PNG?raw=true"/>

As it can be seen that the same Mac addresses are registered for both victim IP and attackers IP on the routers
arp cache. 

This achieves our first step of the attack and finally the attacker can now sniff on the machine. The traffic which
is routed from one machine to another will actually be routed to attacker’s machine. The server and client will
not have any idea.

**Performing sslstrip attack**
Now that we have achieved to sit in the middle of connection we can perform sslstrip attack. Ssltrip is a tool
used comes handy with kali OS which can be used in this case to switching the references from http to https.
The sslstrip is listening on the port 8080. We have altered the iptables in the os firewall to do the port
forwarding from 80 to 8080. The traffic which comes on 80 will be routed to 8080. The sslstrip will work as a
proxy between the machines.

Below is the screenshot for sslstrip command:

<img src="images/ssl4.PNG?raw=true"/>

The sslstrip then logs the decrypted data in sslstrip.log file in the same folder. We can just use cat command to
see the username and password in the plain text. Below is the screenshot which shows the log file and
username and password recorded:

<img src="images/ssl5.PNG?raw=true"/>

**FORM section Change**
What sslstrip does is that it changes and switches the references from https to http and vice versa on the
request and response. We can witness that in the screen shots below where the action tag in the form contains
different links before the attack and after the attack

**Accessing the fakebook page in victim machine**

<img src="images/ssl6.PNG?raw=true"/>


