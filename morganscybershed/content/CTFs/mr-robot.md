+++
title = "Mr Robot - Boot2Root CTF Walkthrough"
date = "2022-10-24"
description = "Sample article showcasing basic Markdown syntax and formatting for HTML elements."
tags = [
    "CTF",
    "syntax",
]
+++

- <a href="#mr-robot-ctf-walk-through"
  id="toc-mr-robot-ctf-walk-through">Mr Robot CTF Walk-through</a>
  - <a href="#network-enumeration-and-scanning"
    id="toc-network-enumeration-and-scanning">Network Enumeration and
    Scanning</a>
  - <a href="#website-navigation" id="toc-website-navigation">Website
    Navigation</a>
  - <a href="#ctf-flags" id="toc-ctf-flags">CTF Flags</a>
    - <a href="#flag-1" id="toc-flag-1">Flag 1</a>
    - <a href="#flag-2" id="toc-flag-2">Flag 2</a>
  - <a href="#flag-3" id="toc-flag-3">Flag 3</a>

# Mr Robot CTF Walk-through

## Network Enumeration and Scanning

The first we must do before we do anything we must scan the virtual
network. Use arp-net to scan the virtual network. Then a list of the IP
addresses should appear. One of the devices on the network should have
“PCS Systemtechnik GmbH” as the vendor. Command: “sudo arp-scan
–localnet”.

Then will use to scan for any open ports on the server using nmap. From
the nmap scan there are a http port open (80) and a ssl port open(443).
This means that we can access the website.

On the website is a mock terminal which serves as a promotion for the
Mr. Robot TV series (I need to get around watching that).

## Website Navigation

None of the commands in the mock terminal are off any use to us. But a
common file that is deployed on most website is robots.txt. So trying
enter *IP Address*/robots.txt. The text contains two entires
“fsocity.dic” and “key1-of-3.txt”.

## CTF Flags

### Flag 1

The first flag is “073403c8a58a1f80d943455fb30724b9” which is stored on
the text file “key-1-of-3.txt”.

### Flag 2

To find the next flag. You are going to need to scan the web server with
the tool Nikto. Nikto will scan any directories and potential web server
vulnerabilities. Nikto has a login for WordPress “*IP
Address*/wp-login”. This server is running version “4.3.30”. We are
going to use Hydra to brute force for any WordPress logins.
“fsocity.dir” could server has a dictionary file for the usernames. Use
wget to download the file from the website first. The command that will
use is “hydra -L fsocity.dic -p 1234 mr.robot -V http-form-post
’/wp-login.php:log=<sup>USER</sup>&pwd=<sup>PASS</sup>&wp-submit=Log+In:Invalid”.
A username and password has found in the login brute force which should
be highlighted in blue “\[80\]\[http-post-form\] host: 192.168.0.102
login: Elliot password: 1234”. We have found a username but not a
password.

Wpscan will used to find out Elliot’s password. Command is
“<http://192.168.0.102/wp-login>”. We have managed to find Elliot’s
password which is “ER28-0652”. We have now successfully logged in.

Now we need to pull off an exploitation. Navigate to the Appearance page
and click on editor and select 404.php. Download this reverse shell php
script:
<https://raw.githubusercontent.com/pentestmonkey/php-reverse-shell/master/php-reverse-shell.php>
and change the variables “ip” to ‘Your Kali IP address’ and \$port to
any port number (I’m using 443 for this example), replace the contents
of 404.php with the php-reverse-shell script and then setup a netcat
listener on your chosen port command: netcat -lvp 443 and then access
the url *IP address*/wp-content/themes/twentyfifteen/404.php and then
return to the terminal your netcat listener is running and a linux shell
should be displayed. Then navigate into the /home/robot folder and you
should find a password hash for robot in the file password.raw-md5. Copy
the contents of password.raw-md5 into a txt file on your home directory.
Then run the password hash though john the ripper with the rockyou list
and then the password for the user “robot” should appear which is
“abcdefghijklmnopqrstuvwxyz”.

Once you’re logged in, open the key-2-of-3.txt text file and then you
found the second flag which is: ‘822c73956184f694993bede3eb39f959’.

## Flag 3

To find the last whilst you’re still logged in as robot, open up nmap in
interactive, then open up sh, you should have root but check the user
nameusing “whoami” just incase. Navigate to the /root folder and open
key-3-of-3.txt. The text file contains the final key
“04787ddef27c3dee1ee161b21670b4e4”. 


