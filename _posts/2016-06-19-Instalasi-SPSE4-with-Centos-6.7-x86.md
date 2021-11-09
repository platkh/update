---
layout: post
title: Instalasi SPSE4 with Centos 6.7 x86
subtitle: 
---
spse4 latihan tutorial install by alphaone – lpse kab. barito selatan

pengantar :
spse4 berjalan berdampingan dengan spse36sp2 (yang merupakan versi upgrade dari spse36) dan membutuhkan alat dan bahan sbb :
centos 6.7 x86_64 (fresh instal, bisa di ssh dan sdh terhubung ke jaringan)

~~~
play 1.3.3
epns-lat-36sp2
spse-lat-4
epel-release-6-8.noarch.rpm
modsecurity265.tar.gz
apache2-centos.zip
jdk-8u60-linux-x86.tar.gzip
jce_policy-8.zip
epns_lat-spse4.sql
~~~

copikan paket2 tersebut ke /usr/local/src/

~~~
cd /usr/local/src/
yum update
yum install nano
(teks editornya saya menggunakan nano)
instal paket dan depedensi yang diperlukan :
rpm -ivh epel-release-6-8.noarch.rpm
yum update
yum -y install psmisc httpd-devel gcc-c++ pcre-devel libxml2-devel 
httpd postgresql-server make mod_security mod_evasive unzip rsync 
lynx elinks vim tcptraceroute nmap htop lshw iperf httperf pktstat
~~~

default port dan folder, redirect match yang akan digunakan :
spse3.6 sp2 latihan spse4 latihan

~~~
/home/appserv/epns-lat-36sp2 /home/appserv/spse-lat-4
8080 9090
/latihan /eproc4lat
~~~

instalasi dimulai :

~~~
mkdir /home/appserv
mv epns-lat-36sp2 /home/appserv/
mv spse-lat-4 /home/appserv/
mkdir /home/file
mkdir /home/file/file_latihan
mkdir /home/file/file_prod
~~~

persiapan postgres, apache dkk :

~~~
#/etc/init.d/postgresql initdb (untuk inisiasi directory database)
#/etc/init.d/postgresql start (restart aplikasi database)
#chkconfig postgresql on (aktifkan start up postgresql pada operating system)
#chkconfig httpd on (aktifkan start up http / apache pada operating system)
#unzip apache2-centos.zip (extract file apache2-centos.zip)
#cat settingapache.txt | cat >> /etc/httpd/conf/httpd.conf
#tar -xzvf modsecurity265.tar.gz
#cd /usr/local/src/modsecurity265/curl-7.28.1/ (proses compile mod_security / curl)
#./configure
#make
#make install 
#cd ../modsecurity-apache_2.6.5/
#./configure –with-apxs=/usr/sbin/apxs
#make
#make install
#cd ../
#cat additional_rule_mod_security.conf | cat >> /etc/httpd/conf/httpd.conf
#cd ../
#/etc/init.d/iptables stop
#chkconfig iptables off
#sed -i “s/enforcing/disabled/g” /etc/selinux/config
#tar -xzvf jdk-8u60-linux-x86.tar.gzip (extrac file java)
#mv jdk1.8.0_60 jdk1.8.0
#unzip jce_policy-8.zip (extrac java policy)
#cp -vr UnlimitedJCEPolicyJDK8/* jdk1.8.0/jre/lib/security (copy file java policy ke directory java)
overwrite pilih y –> enter
~~~

Konfigurasi Database dan Restore Database

~~~
su postgres [ENTER] ——-> masuk ke user postgres
psql [ENTER] ———-> masuk ke dalam database
CREATE USER epns password ‘epns’; [ENTER]
\q [ENTER] ———> keluar dari database
createdb -U postgres -O epns epns_latihan ; [ENTER] ————> membuat nama database baru yaitu epns_latihan
psql -U postgres -d epns_latihan -f epns_lat-spse4.sql [ENTER]
exit —–> keluar dari user postgres
~~~

Rubah Konfigurasi database :

~~~
# nano /var/lib/pgsql/data/pg_hba.conf
kita rubah ident menjadi trust 
# “local” is for Unix domain socket connections only
local all all trust
# IPv4 local connections:
host all all 127.0.0.1/32 trust
# IPv6 local connections:
host all all ::1/128 trust
Ctrl – X + Y + Enter
restart service database :
/etc/init.d/postgresql restart
~~~

Konfigurasi Apache / HTTPD

~~~
#hostname
appservlpse
#nano /etc/hosts
tambahkan 127.0.0.1 appservlpse
Ctrl X – Y + enter
~~~

~~~
nano /etc/httpd/conf/httpd.conf
beri tanda coment (#)
#LoadModule jk_module modules/mod_jk.so
rubah epns-lat dengan inisial 35 menjadi 36sp2
rubah epns-prod dengan inisial 35 menjadi spse-lat-4
Alias /latihan /home/appserv/epns-lat-36sp2
Alias /eproc4lat /home/appserv/spse-lat-4
~~~

~~~
AllowOverride None
Order deny,allow
deny from all
~~~

beri tanda # untuk baris yang menggunakan inisial JK
misal 

~~~
#JkWorkersFile /etc/httpd/workers.properties
#JkLogFile /var/log/httpd/mod_jk.log
#JkLogLevel error
#JkLogStampFormat “[%a %b %d %H:%M:%S %Y] ”
#JkMount /latihan* worker1
#JkMount /eproc* worker2
#JkMountCopy All
~~~

kemudian tambahakan/input comand diatas baris JKWorkers 

~~~
ProxyRequests Off
ProxyVia Off
Proxytimeout 600
ProxyPass /latihan http://localhost:8080/latihan
ProxypassReverse /latihan http://localhost:8080/latihan
ProxyPass /eproc4lat http://localhost:9090/eproc4lat
ProxypassReverse /eproc4lat http://localhost:9090/eproc4lat
edit redirect match 
RedirectMatch ^/$ /eproc4lat/
RedirectMatch ^/latihan$ /latihan/
tambahkan baris paling bawah :
Servername appservlpse
Ctrl -X + Y + Enter
~~~

unzip play-1.3.3-prod.zip

~~~
nano /home/appserv/epns-lat-36sp2/spse3
sesuaikan :
JAVA_HOME=/usr/local/src/jdk1.8.0
APP_HOME=/home/appserv/epns-lat-36sp2
PORT =8080
CONTEXT =/latihan
nano /home/appserv/epns-lat-36sp2/webapp/WEB-INF/classes/application.properties
sesuaikan :
jdbc.url=jdbc:postgresql://localhost/epns_latihan
~~~

~~~
nano /home/appserv/spse-lat-4/spse4
sesuaikan :
JAVA_HOME=/usr/local/src/jdk1.8.0
APP_HOME=/home/appserv/spse-lat-4
PLAY_HOME=/usr/local/src/play-1.3.3
PLAY_VERSION=play-1.3.3
PORT=9090
nano /home/appserv/spse-lat-4/conf/application.conf
sesuaikan :
http.port=9090
spse3.url=http://ip-server/latihan
~~~

~~~
chmod 755 /home/appserv/epns-lat-36sp2/spse3
chmod 755 /home/appserv/spse-lat-4/spse4
~~~

restart apache :

~~~
/etc/init.d/httpd restart
bila failed ===> coba reboot untuk mendisable SElinux dan ulangi start httpd
jalankan spse36sp2 :
cd /home/appserv/epns-lat-36sp2/
./spse3 start
~~~

jalankan spse4latihan :

~~~
cd /home/appserv/spse-4-lat/
./spse4 start
agar spse36sp3 start otomatis pada saat boot :
nano /etc/rc.local
rm -rf /home/appserv/epns-lat-36sp2/server.pid
/home/appserv/epns-lat-36sp2/spse3 start
sleep 60
rm -rf /home/appserv/spse-4-lat/server.pid
/home/appserv/spse-4-lat/spse4 start
ctrl + x + s
~~~

*reboot server..enjoy!*


*thanks san sifu Agus NR Barsel*



Ref : 
```
kloxo.web.id
```


