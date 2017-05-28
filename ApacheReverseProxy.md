# Apache Reverse Proxy

## Explanation
This article includes how to set up a reverse proxy based Ubuntu using Apache2.

## When to use:
When you have a remote server, and server runs a program using a specific port, i.e. Nexus which using **8081** port number. 
You will find that you are able to access it internally, but you can't access it remotely.

Therefore, one solution is to setup a reversy proxy on server.

This page is a instruction of how to do it.

Basic description


* Access Nexus externally which is now running on my server, on **0.0.0.0:8081**.
* The server is Ubuntu **14.04**
* In this example, the server ip address is : **146.118.101.130** 
* If I try to access **0.0.0.0:8081** internally, it works fine. However, if I try to access by **146.118.101.130:8081**, it fails.

## Content

* [step 1: Install Apache](#step1)
* [step 2: Load Modules](#step2)
* [step 3: Add setting](#step3)
* [final](#final)
* [Trouble Shooting](#troubleShooting)

## <a name='step1'/>Step 1: Install Apache
Install Apache2 by using command line:

```
sudo apt install apache2
```
After this, your server will have installed Apache2.

You can try to type your server ip address on browser, you will see a index page of apache similar like:

![image.jpg](https://raw.githubusercontent.com/MelbPaulZ/AndroidNotes/master/apache.png)

## <a name='step2'>Step 2: Load modules
type in command line to load apache modules:

```
a2enmod proxy proxy_balancer proxy_http
```

Then restart apache2 by using:

```
sudo service apache2 restart
```

## <a name='step3'>Step3: Add settings
Using comming line:

```
cd /etc/apache2/sites-enabled
```
Then edit setting file `000-default.conf`

Add the following:

```
<VirtualHost *:80>
...
...

# set localhost as default servername, to suppress warning
ServerName localhost

# off => open reverse proxy
# on means proxy
ProxyRequests Off
ProxyMaxForwards 100
ProxyPreserveHost On

# the virtual proxy will direct to 8081 port
# i.e. when you type in the ip, http://146.118.101.130,
# this will map to the 0.0.0.0:8081

ProxyPass / http://0.0.0.0:8081/
ProxyPassReverse / http://0.0.0.0:8081/


<Proxy *>
        Order Deny,Allow
        Allow from all
</Proxy>

</VirtualHost>
```

Save and re-run apache:

```
sudo service apache2 restart
```

## <a name='final'>Final 
Open browser, type in 

```
146.118.101.130
```
You can successfully access Nexus. The reverse proxy has done.

![nexus.jpg](https://github.com/MelbPaulZ/MavenPrivate/blob/master/nexus.jpeg?raw=true)

## <a name='troubleShooting'>Trouble Shooting

If still cannot access, try to edit `apache2.conf` as following:

Change default **required all deny** to **required all granted**

```
<Directory />
	Options FollowSymLinks
	AllowOverride None
	Require all granted
</Directory>
```




