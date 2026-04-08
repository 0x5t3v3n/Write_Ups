# Fawn

![image.png](Fawn/image.png)

FTP stands for “File Transfer Protocol”.

![image.png](Fawn/image01.png)

FTP is port…21, if IIRC.

![image.png](Fawn/image02.png)

Quick nmap scan of the target IP shows the open port, and the service is ftp. So, yes, it’s 21.

![image.png](Fawn/image03.png)

sftp, ssh file transfer protocol.

It provides similar functionality to FTP but runs over the Secure Shell (**SSH**) protocol, encrypting both commands and data for secure file transfers.

![image.png](Fawn/image04.png)

ping. The ‘ICMP echo request’ is the actual packet that is sent  to the target iirc. When you ‘ping’ a target, you’re testing if you can reach the target. For example, in study material, you could ping 8.8.8.8, which is Google, testing if you’re host has internet connection.

![image.png](Fawn/image05.png)

For this one, I will do another nmap scan, but this time, with the -sV flag.

![image.png](Fawn/image06.png)

vsftpd 3.0.3

![image.png](Fawn/image07.png)

Unix, is said in the above nmap -sV scan output.

![image.png](Fawn/image08.png)

This is always command -? or command -h or command —help.

![image.png](Fawn/image09.png)

![image.png](Fawn/image10.png)

This is always ‘anonymous’

![image.png](Fawn/image11.png)

This is asking for the response code after successfully logging in.

![image.png](Fawn/image12.png)

Here, we see, the command entered was “ftp <IP>”. We see ‘connected’, and then we are asked to input a Name. Here, I put ‘anonymous’. For the password, I leave empty. After hitting Enter, it connects and we end up in a ftp shell. It’s 230, that is shown with the Login successful.

![image.png](Fawn/image13.png)

This is asking for a 2 character long command that lists files and directories on a FTP server. It also then says, it’s a common way of doing the same thing, on Linux. And it’s a 2 character answer. So it’s going to be ‘ls’. 

![image.png](Fawn/image14.png)

‘ls’ is the command in Linux that lists the contents of the directory you’re in.

![image.png](Fawn/image15.png)

Task 11 is asking for the command to download files on a FTP server. 3 characters. I believe it’s ‘get’. A little confusing, because it reminds me of ‘GET’ requests, like from HTTP, but it is correct.

![image.png](Fawn/image16.png)

And now, we finish with submitting the flag. When we ran ‘ls’, we see there’s a file named ‘flag.txt’. We download it with ‘get’, and the print the contents to read them, and submit.

![image.png](Fawn/image17.png)

This shows, while logged in as ‘anonymous’, on the FTP server, I run ‘ls’, and see ‘flag.txt’.  I run ‘get flag.txt’, and the next few lines show downloading the file. Once it’s downloaded, I run ‘quit’, to drop back into our normal, home shell, and run ‘ls’ and see that it’s now on our host pwd. Run ‘cat flag.txt’, and get the flag.

Notice though, that I dropped back Out of the FTP server, in order to read the flag. Because while logged into the FTP server, and i ‘get’ or download the file, it’s downloading Under the ftp shell, downloading to the actual host.

![image.png](Fawn/image18.png)

Fawn is completed.
