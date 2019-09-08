## Introduction

Unlike passive information gathering, which involves an intermediate system for gathering information, active 
information gathering involves a direct connection with the target. The client probes for information directly 
with the target with no intermediate system in between. While this technique may reveal much more information 
than passive information gathering, there's always a chance of security alarms going off on the target system. 
Since there's a direct connection with the target system, all the information requests would be logged and can 
later be traced back to the source.  

## Custom Bash script for PingSweep

Bash script has been created to do the task 1 of the assignment. As you can see the script right
now does not take user input and only scans for 10.0.2.0/24. This is already set by default in
the script. To execute the bash script “ ./ “ is appended before the script name. The output
shows the 5 hosts are up and running which is also verified by the Nmap –sP scan.
“awk” is used to take the input from the ping output through pipeline and process only those
lines which have /bytes from/ pattern substring in it. These lines will be only the lines for
which hosts has received replies. 

<img src="images/info1.PNG?raw=true"/>

## Banner Grabbing of 3 ports 

Firstly the Netcat is used for port scanning.

Nc –v –n –z –w1 <Host> <Port Range>

The above command is used for the port scan on the host specified within the port range given. Along
with open tcp ports it also tells us about the service running on the port. –v option is used for the
verbose output of netcat command. –n is mentioned when doesn’t we don’t want to resolve names. –z is 
mentioned when you don’t want to send any data on the connection. -w1 is used when you want it to wait
more that 1 second. The command gives us all the open ports. 

<img src="images/info2.PNG?raw=true"/>

## HTTP Server Banner:

<img src="images/info3.PNG?raw=true"/>

Since the http port is already open. We will try to send the http request to the server and
observe the response. As seen in the screen shot, I sent GET HTTP request on the connection
which was not recognized by the server and hence in return, the server replied with full body
html page back to the client. There we can see that Apache/2.2.8 (Ubuntu ) DAV/2 Server
is running at metasploitable.localdomain.

## Smtp Banner:

<img src="images/info4.PNG?raw=true"/>

The connection on the smtp port gives you the smtp server running on the host machine. It
shows that metaploitable.localdomain ESMTP Postfix (Ubuntu) server is running as
SMTP.

## SSH Banner:

<img src="images/info5.PNG?raw=true"/>

Same with the ssh connection on doing netcat connection on port 22, it gives you the service
running on the port and the version of the service running. Thus we can infer that the host
machine is running SSH-2.0-OpenSSH_4.7p1 service on Ubuntu.

## Overnvas Scan

Openvas is a vulerability scan tool which maintains its own database from NVD and CVE for different
vulnerailties and queries during runtime while doing scan on the machines. It generates the report
specifying the different vulnerabilities and severity of those vulnerabilties. On the basis of those
scores it gives you a single score of severity. The reports can be generate in both pdf and xml and
vaious formats.

To start a open vas, run: openvas-setup on the command line and then it will run the Web UI on port
9392. 

**MetaSploitable Scan:**
As we can see, the scan give the following result. Scan found out 19 vulnerabilities with high
severity, 36 vulnerabilities with medium severity and 3 vulnerabilities with low severity. The final
score was 10.0 on the severity scale which is very high.

<img src="images/info6.PNG?raw=true"/>

## Compare port scanning with NetCat vs NMAP
**Nmap:**
  Pros:
    1. Nmap is a very fast and useful tool for network scanning.
    2. Nmap provide many choices and options for user to scan the network according to
        his/her convenience.
    3. It can be used for banner grabbing and also to find out any service or OS running on
        the machine.
    4. It provides the capabilities for different result formats: XML, Pipeline to grep
    5. Nmap offers both GUI and command line utility.
Cons:
    1. Creates very noise on port scan. Although Stealthier scan option have alsobeen
        included now.
    2. No updates. Thus not very useful for new services and technologies
    3. Scripted in Lua language. Difficult to customize the code and use for some specific
        purpose.
**Netcat:**
Pros:
  1. Originally designed to help network managers, netcat is also useful for network
      exploitation and information gathering
  2. Netcat can serve as both client and server utility.
  3. Netcat commands can be executed directly fromterminal or it can be used in program
      or script
  4. It has added functionalities over nmap as it can be used to transfer file. It can establish
      and maintain a tcp connection for real time.
Cons:
  1. Netcat single line commands can not be used to achieve big tasks as in nmap

