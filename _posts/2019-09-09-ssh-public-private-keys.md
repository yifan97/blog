---
layout: post
current: post
cover: assets/images/cover-img/c19.jpg
navigation: True
title: ssh and public/private keys
date: 2019-09-09
comments: true
---

**This is an introduction of ssh(secure shell) and  public/private keys**

-------------------------------------

**SSH**

Some might be wondering what is ssh, is it different from a normal shell? In a nutshell, ssh provides a way to login to a remote machine and run commands at CLI.

SSH is basically:
- a protocol for creating secure connection, authentication and data transferring
- a command-line client for connecting to an SSH server via the protocol

There are some common use cases:
- Doing work in remote server 
- Transferring date to or from remote servers
- Transporting git repository data

If you ever had experience to work on a remote server, what you typically do is to ```ssh``` to that specific server that you want to work on by ```ssh <user>@<hostname>```. And you will need to provide a way for the server to recognize you, in most cases, the password. For example, I am working on my assignment on my school department server, what I need to do to login to that server is ```ssh myUserId@myDepartmentServerHostName```. 

Before any identifying information (such as a password or public key) is transmitted, the connection itself is encrypted via a shared secret, which is established upon connection using a clever algorithm named after its inventors: Diffie-Hellman Algorithm. Once securely connected, both sides know the communications are protected between one another via encryption. **But no guarantee of who is on the other end!**. The process to establish who is on the other end is called authentication, usually in two steps:
- The server provides an SSH fingerprint the client can verify
- The client either provides a password or, ideally, via public-key authentication

Isn't that bothering when you have a frequently-used server and you have to type a long password everytime you try to authenticate? Moreover, there are some drawbacks using password to authenticate:

- Medium-to-weak passwords are easily cracked
- Sharing passwords across services is common (leaks become problematic)
- Likelihood of accidentally typing your password into the wrong window
- ...

As I mentioned above, there is an ideal way to authenticate: a cryptographic key pairs, i.e. public/private key

**Publuc/Private Keys**

There are basically five steps to authenticate a user(know who is connecting with the server):
- Once the secure connection is established(via ```ssh```), user/client sends a public key fingerprint to the server
- The server receives that fingerprint and search for a public key associated with that fingerprint
- If there is a public key matching that fingerprint resgitered, server will generate a random number and encript it with the public key
- User receives that encripted number and use private key to decript it, send the number after decripting back to server
- Server receives the number, and checks the hash to decide whether it is a safe user, and authenticate the connect if it is

**Notice:** It's obvious and important to note that you should never share your private key!

**Knowing the working principle of public/private key, how can we actually use it in practice?**

There is a specific command ```ssh-keygen```, a part of ssh suits, that can generate a public/private key pair. 

In my computer, I generate a pair by running the following command:

```
ssh-keygen -t rsa -b 4096 -C "my email" -f .ssh/id_rsas
```

where ssh-keygen is the command, followed by four arguments:
- -t rsa &emsp;&emsp;&emsp;&emsp;&emsp; of key is RSA (Rivest-Shamir-Adleman Algorithm)
- -b $$4096$$ &nbsp;&emsp;&emsp;&emsp;&emsp;strength of key is 4096 bytes
- -C "my email" &nbsp;&emsp;&nbsp;comment on key is an email
- -f .ssh/id_rsas &nbsp;&emsp;output private key to this file

We must sote the keys in .ssh file under the user's account because that's where ssh program looks to find the keys when it makes connections. The file named id_rsa is the private key(again, do **not** share) The file named id_rsa.pub is the public key. Since we don't want others to have a chance to access our private key, id_rsa needs special file permissions set on it.



**Now that we have a pair of public/private keys, how do you share your public key with a server for authentication?**

Unfortunately, if it is the first time ever to log into a server, you have to use another method(ex. password authentication) to authenticate. Then, once you log in, you neet to setup the **.ssh** directory and create a file in this directory called **authorized_keys** file . You also set their permissions to be when needed. Then you just need to transfer your id_rsa.pub to the server's authorized_keys file via ```scp``` 

When you log into the server for the first time. Run the following command.
```c
mkdir -p .ssh                        // make a direcoty names .ssh and don't complain if one already exists (-p)
chmod 700 .ssh                       // Change the permissions of this directory such that only you can read/write/list it (700)
touch .ssh/authorized_keys           // Create an empty file in .ssh directory named authorized_keys
chmod 600 .ssh/authorized_keys       // Change the permissions of this file so that only you can read/write to it
```

And in your local machine, ```scp``` the public key to server's authorized_keys file
```c
scp ../.ssh/id_rsa.pub ssh <user>@<hostname>:.ssh/authorized_keys
```

**Notice:** You can have more than one public keys registered with a user. It's common to have one key per device like
laptop/desktop so if it gets stolen you can revoke just its key


Now, the last thing left is to set alias for those server you interact with most so that it's quick to log in instead of typing password everytime you use ```ssh```. The way to do it is you edit the ssh configuration file: in your home directory, type ```vim .ssh/config``` and setup whatever configuration you need. 