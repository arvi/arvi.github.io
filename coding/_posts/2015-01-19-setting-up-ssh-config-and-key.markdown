---
layout:     post
title:      "Setting up SSH Config and Key"
subtitle:   ""
date:       2015-01-19 11:04:00
author:     "Arvi A."
header-img: ""
category: coding
---

<p>I recently needed to set-up SSH keys on both my Linux and MAC OS X machine so I can access our AWS server whether I'm at home or in the office.
I was so used to Windows Putty+Pageant solution, that I had no idea how to do it in other OS.</p>

<p>I must admit it wasn't as easy as setting it in a Windows machine. There were way too many suggestions on how to do it, that if I would repeat the process of figuring out how to do it again, I wouldn't remember what works and what doesn't for me.</p>

<p>The moment I got it working after multiple attempts, I combined solutions I found in Stackoverflow and different articles to make the solution more readable for me than my previous attempt... and it worked! :) </p>

## HERE'S HOW I DID IT:

<br />

### 1. Open preferred terminal application.
<p>I'm using iTerm2 for MAC OS X and Terminal for Linux</p>

<hr>

### 2. Create an SSH config file (if not yet existing)

{% highlight shell-session %}
cd ~/.ssh
touch config
nano config
{% endhighlight %}

<hr>

### 3. Edit the config file where:
- Host: any name that is easy for you to remember (we'll use this in accessing our server later)
- User: Server username
- HostName: Server name
- IdentityFile: where you put your pem file in your local machine. I put mine in same directory as SSH config file.

{% highlight shell-session %}
Host meteor.app
    User ubuntu
    HostName ec2-54-111-22-333.us-west-1.compute.amazonaws.com
    IdentityFile ~/.ssh/meteorkey.pem
{% endhighlight %}

<hr>

### 4. Create an SSH key
{% highlight shell-session %}
cd ~/.ssh
ssh-keygen -t rsa
{% endhighlight %}

<p>The following will appear in your terminal after the <em>ssh-keygen -t rsa</em> command.</p>
<p>I highly recommend renaming the <em>id_rsa</em> key so it's easier to distinguish when you have multiple servers to access.</p>

{% highlight shell-session %}
Generating public/private rsa key pair.
Enter file in which to save the key (/Users/Arvi/.ssh/id_rsa): meteor_rsa
{% endhighlight %}

<p>I also prefer having no passphrase since I recall that I ran into either git cloning problem or meteor deploy (mup) when I had a passphrase (for which I ended up adding a new ssh key, but this time with no passphrase).<br /><br />
Just hit <em>Enter</em> if you don't want to have a passphrase.</p>

{% highlight shell-session %}
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in meteor_rsa.
Your public key has been saved in meteor_rsa.pub.
The key fingerprint is:
bd:87:4a:36:8f:d9:25:ea:ae:c7:82:e8:39:73:92:14 Penned@Penned-iMac.local
The key's randomart image is:
+--[ RSA 2048]----+
|                 |
|                 |
|                 |
|  E      .       |
|   .    S .      |
|  .        o     |
| . o . .+ + o    |
|  *.o .ooO +     |
| .o=  .*B o      |
+-----------------+
{% endhighlight %}

<hr>

### 5. Add key to your server instance

<p>This will read the contents of your generated public key (created in Step 3).</p>
{% highlight shell-session %}
cat ~/.ssh/meteor_rsa.pub
{% endhighlight %}

<p>This will append the contents of your generated public key to the server's list of authorized keys (<em>meteor.app</em> is configured in Step 2).</p>
{% highlight shell-session %}
ssh meteor.app "cat >> ~/.ssh/authorized_keys"
{% endhighlight %}

<hr>

### 6. Access your server instance
{% highlight shell-session %}
ssh meteor.app
{% endhighlight %}

<p>which will give you this</p>

{% highlight ruby %}
Welcome to Ubuntu 14.04.1 LTS (GNU/Linux 3.13.0-36-generic x86_64)

 * Documentation:  https://help.ubuntu.com/

  System information as of Mon Jan 19 05:16:59 UTC 2015

  System load:  0.08               Processes:           69
  Usage of /:   22.6% of 14.64GB   Users logged in:     0
  Memory usage: 25%                IP address for eth0: 10.111.22.333
  Swap usage:   0%

  Graph this data and manage this system at:
    https://landscape.canonical.com/

  Get cloud support with Ubuntu Advantage Cloud Guest:
    http://www.ubuntu.com/business/services/cloud

96 packages can be updated.
53 updates are security updates.


*** System restart required ***
Last login: Mon Jan 19 05:17:01 2015 from 112.345.678.90
ubuntu@ip-10-111-22-333:~$
{% endhighlight %}

<hr>

### 7. Access the authorized keys list and check if the generated public key in Step 3 has been appended successfully.
{% highlight ruby %}
ubuntu@ip-10-111-22-333:~$ nano ~/.ssh/authorized_keys
{% endhighlight %}

<hr>

### 8. Celebrate! Woooohoooo!


<br>
<hr>
<br>

### Many Thanks to:

- <a href="http://mikeeverhart.net/ubuntu/adding-an-ssh-key-to-amazon-ec2-servers/" target="_blank">Mike Everhart - Adding an SSH key to Amazon EC2 servers</a>
- <a href="http://stackoverflow.com/questions/2419566/best-way-to-use-multiple-ssh-private-keys-on-one-client" target="_blank">StackOverflow - Best way to use multiple SSH private keys on one client</a>



