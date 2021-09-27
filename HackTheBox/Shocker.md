# Shocker
In this writeup we'll pwn the Shocker box, an Easy challenge that uses a very well-known vulnerability.

![Shocker](../Images/htb_shocker_1.png)

**Tools: nmap, dirb, Metasploit.**

We begin with a scan using nmap (this machine’s IP is 10.10.10.56):
> nmap -Pn -sC -sSV --open -p- 10.10.10.56

![Shocker](../Images/htb_shocker_2.png)

Ports 80 and 2222 are open, so let's take a look at the webpage on port 80:

![Shocker](../Images/htb_shocker_3.png)

Nothing there, using Firefox' dev-tools doesn't show anything as well.  

Let's try some fuzzing with dirb to find interesting directories:
> dirb http://10.10.10.56

![Shocker](../Images/htb_shocker_4.png)

We've found /cgi-bin/ and it is our next target, because it's a directory known for the Shellshock vulnerability. We can try running dirb again, providing some different extensions like .sh:
> dirb http://10.10.10.56/cgi-bin -X .sh

![Shocker](../Images/htb_shocker_5.png)

We can try downloading the file we've just found by accessing the webpage and then we can read it.
> http://10.10.10.56/cgi-bin/user.sh

![Shocker](../Images/htb_shocker_6.png)

It's not so useful, but now that we have a full path to a file inside cgi-bin, we can run Metasploit with the respective exploit.

![Shocker](../Images/htb_shocker_7.png)

Next up we configure the exploit/payload:

![Shocker](../Images/htb_shocker_8.png)

We got a meterpreter shell! Now we can grab the user flag...

![Shocker](../Images/htb_shocker_9.png)

Now we force metasploit to give us a local shell, then we check for the files we can run with sudo permission with sudo -l (as you can see below, it's perl, so it's an easy privilege escalation):
> sudo perl -e 'exec "/bin/sh"'

![Shocker](../Images/htb_shocker_10.png)

Thats it, we're root and the root flag is ours!   

Thanks for reading and keep hacking!
