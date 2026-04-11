# Eighteen

Eighteen is a Easy difficulty Windows machine.

Machine information: As is common in real life Windows penetration tests, you will start the Eighteen box with credentials for the following account: kevin / iNa2we6haRj2gaw!

Starting off with nmap scan:

![image.png](image.png)

In the results, we see:

Port 80/tcp, which is http, and Microsoft IIS httpd 10.0. This redirects to “eighteen.htb”

![image.png](image%201.png)

Port 1433/tcp. Which is ‘ms-sql-s’ and Microsoft SQL Server 2022. Showing the domain is ‘eighteen.htb’ and the Host is ‘DC01.eighteen.htb’

![image.png](image%202.png)

Port 5985/tcp. http. Microsoft HTTPAPI httpd 2.0 ( SSDP/UPnP )

![image.png](image%203.png)

Adding to my /etc/hosts

![image.png](image%204.png)

![image.png](image%205.png)

Using ‘mssqlclient’ to connect to MSSQL with the credentials that were given to us.

![image.png](image%206.png)

From here, we use SQL commands to enumerate the admin’s hash.

We first ‘enum_impersonate’, which reveals ‘appdev’.

![image.png](image%207.png)

From here, we ‘USE financial planner’, and get the ‘password_hash’

![image.png](image%208.png)

So, we now know the admin hash is:

pbkdf2:sha256:600000$AMtzteQIG7yAbZIa$0673ad90a0b4afb19d662336f0fce3a9edd0b7b19193717be28ce4d66c887133

![image.png](image%209.png)

Using a python script to crack the hash, we get the plaintext of the hash.

![image.png](image%2010.png)

Next, we enumerate the users found on the Domain, using “RID Brute Force”.

![image.png](image%2011.png)

![image.png](image%2012.png)

Here, we see more users:

![image.png](image%2013.png)

We can make a text file, and put all the users inside the file, and then run a “password spray attack” against the file of usernames, using the password we got from cracking the hash.

What this means, is that we will attempt to use that specific password, against all the usernames in the file.

![image.png](image%2014.png)

We then run the attack. and get a match with adam.scott

![image.png](image%2015.png)

We can now remotely login as [adam.scot](http://adam.scot) using evil-winrm. 

![image.png](image%2016.png)

Now, we are logged into a Windows machine, as adam.scott, and are in the Documents directory.

We check what’s inside the Documents directory:

![image.png](image%2017.png)

We go up on directory, into essentially the home directory:

![image.png](image%2018.png)

We go to the Desktop directory, and find the user flag.

![image.png](image%2019.png)

![image.png](image%2020.png)

We now enumerate internally:

![image.png](image%2021.png)

![image.png](image%2022.png)
