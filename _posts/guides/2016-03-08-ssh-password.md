---
layout: post
title:  "SSH login without entering the password"
date:   Tue Mar  8 23:10:20 2016
categories: guides
---

When frequent ssh/scp to the server is need, repeatedly entering the password is arduous. Luckily it is easy to bypass that. This guide assumes both the client and the server systems are Linux/Unix derivatives.

First, from the client side, we generate a pair of authentication keys.

{% highlight bash %}
you@client:~> ssh-keygen -t rsa
Generating public/private rsa key pair.
Enter file in which to save the key (/home/you/.ssh/id_rsa): 
Created directory '/home/you/.ssh'.
Enter passphrase (empty for no passphrase): 
Enter same passphrase again: 
Your identification has been saved in /home/you/.ssh/id_rsa.
Your public key has been saved in /home/you/.ssh/id_rsa.pub.
{% endhighlight %}

Then, use ssh to create the ```~/.ssh``` directory on the server side.

{% highlight bash %}
you@client:~> ssh you@server mkdir -p .ssh
{% endhighlight %}

Append your public key to the authorized keys on the server.

{% highlight bash %}
you@client:~> cat ~/.ssh/id_rsa.pub | ssh you@server 'cat >> .ssh/authorized_keys'
{% endhighlight %}

Enter the password one last time, and you should be able to ssh/scp/sftp to the server smoothly.

{% highlight bash %}
you@client:~> sftp you@server
you@client:~> ssh server -l you
you@client:~> scp something you@server:~/
{% endhighlight %}

