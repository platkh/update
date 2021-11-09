---
layout: post
title: Cannot establish FTP connection to an SFTP server FileZilla
subtitle: 
---

  Setelah upgrade ke ubuntu 16.04, nemu ni masalah di FileZilla. 

~~~  
Error:    Cannot establish FTP connection to an SFTP server. Please select proper protocol.
Error:    Critical error: Could not connect to server
~~~

![img](/img/Problem1.jpeg)

Ketimbang lupa mending masukin dimari, brangkatt..
Pada menu filezilla buka **File -> Site Manager**

![img](/img/Problem2.jpeg)

Pada **Site Manager**,klik **New Site** (terserah mo kasih nama apa) ke General tab kemudian pilih **SFTP - SSH File Transfer Protocol**
Isikan host server, port number, user dan password.

![img](/img/Problem3.png)

ehemmm..mudahan sukses

Ref: itsfoss.com
