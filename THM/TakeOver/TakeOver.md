# TakeOver

![image.png](image.png)

This explains the lab. We are being asked to respond to a claim from hackers saying they could takeover and then ransom it back to the owners. We are to figure out what they could takeover and how.

We’re giving the URL of the website, and are given a hint to add the IP to the ‘/etc/hosts/’ file.

![image.png](image%201.png)

I start by ping’ing the IP address to make sure it’s up.

![image.png](image%202.png)

Running an nmap scan, we see that port 22/tcp is up which is SSH. There’s port 80 up, http, with a redirect to ‘https://futurevera.thm/’

I’ll add that to the /etc/hosts

![image.png](image%203.png)

In the room info, we’re told they’re rebuilding their support, so we add support.futurevera.thm to /etc/hosts

![image.png](image%204.png)

i’ll use firefox to go to that domain

![image.png](image%205.png)

now, adding support to the URL

![image.png](image%206.png)

you get this:

![image.png](image%207.png)

clicking on advanced, and then view certificate, we see another subdomain

![image.png](image%208.png)

adding it to /etc/hosts

![image.png](image%209.png)

![image.png](image%2010.png)

when going to that URL, we get the flag.

![image.png](image%2011.png)

![image.png](image%2012.png)

tips for future:

![image.png](image%2013.png)

![image.png](image%2014.png)
