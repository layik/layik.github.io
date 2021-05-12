---
permalink: /anydomain.html
---
[Home](https://layik.github.io)
<hr/>

28th March 2019

# Binding any domain to a machine
Working on a specific project we needed to simulate a domain name pointing to one of our servers. What seems to be a difficult problem, is actually totally easy to solve.

The Linux `/etc/hosts` file can do this by adding a new line such as:
`127.0.0.1  anydomain.com`

You can then do whatever you like on your machine and your machine won't be asking anyone (DNS servers) for IP addresses for the domain `anydomain.com`. You can also set your local machine to point the domain to any IP address (say 161.0.0.1 IP).

How would you do this for a remote machine? There are two ways of doing this as far as I have come to learn. I am not going to discuss the second one here. The first and easy way of doing this is to:

1. Configure your local machine's `etc/hosts` with the remot server's IP (say 161.0.0.1 IP).
2. Configure your remote/server machine's `/etc/hosts` with the machine's local IP which is `127.0.0.1`.

What is happening now?

Your local machine is pinting to the server, the server responds by pointing to itself. Done.

The second way of doing this does not involve (1) but needs SSH tunneling throught the remote but (2) remains the same.