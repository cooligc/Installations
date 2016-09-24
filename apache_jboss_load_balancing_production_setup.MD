Load balancing set up for JBOSS/Tomcat application server + apache httpd + mod_jk in linux
===================================================================================

Consider, we have 2 jboss instance running with following spectification
- JBOSS_PORT=8080, AJP_PORT=8009
- JBOSS_PORT=8081, AJP_PORT=8010

####Note
For tomcat, AJP connector activate by default. For Jboss , we need to do it manually by adding connector tag into standalone.xml under subsystem tag

```
<connector name="ajp13" protocol="AJP/1.3" socket-binding="ajp"/>
```

##Prerequisites Softwares
- jboss-as-7.1.1 (``` wget http://download.jboss.org/jbossas/7.1/jboss-as-7.1.0.Final/jboss-as-7.1.0.Final.tar.gz ```)
- httpd-2.4.23 (``` wget http://mirror.fibergrid.in/apache//apr/apr-1.5.2.tar.gz ```)
- apr-util-1.5.4 (``` wget http://mirror.fibergrid.in/apache//apr/apr-util-1.5.4.tar.gz ```)
- apr-1.5.2 (``` wget http://mirror.fibergrid.in/apache//apr/apr-1.5.2.tar.gz ```)
- pcre-8.39 (``` wget http://ftp.exim.llorien.org/pcre/pcre-8.39.tar.gz ```)
- openssl-1.0.2h (``` wget https://www.openssl.org/source/openssl-1.0.2h.tar.gz ```)
- mod_jk connector (``` wget http://mirror.fibergrid.in/apache/tomcat/tomcat-connectors/jk/tomcat-connectors-1.2.41-src.tar.gz ```)

## Installation 
- Install pcre-8.39

    ```
    tar -xzvf pcre-8.39.tar.gz
    cd pcre-8.39
    ./config --prefix=/usr/local/pcre && make && make install
    ```
- Install openssl-1.0.2h

    ```
    tar -xzvf openssl-1.0.2h.tar.gz
    cd openssl-1.0.2h
    ./config --prefix=/usr/local/ssl && make && make install
    ```
- Install httpd-2.4.23

    ```
    tar -xzvf httpd-2.4.23.tar.gz
    tar -xzvf apr-util-1.5.4.tar.gz
    tar -xzvf apr-1.5.2.tar.gz
    # Add APR and APR module to httpd
    mv apr-util-1.5.4 httpd-2.4.23/srclib/apr-util
	  mv apr-1.5.2 httpd-2.4.23/srclib/apr
    cd httpd-2.4.23
    ./configure --prefix=/usr/local/apache --with-pcre=/usr/local/pcre/bin/pcre-config --with-ssl=/usr/local/ssl/ --enable-ssl --with-included-apr && make && make install
    ```
- Install mod_jk

    ```
    tar -xzvf tomcat-connectors-1.2.41-src.tar.gz
    cd tomcat-connectors-1.2.41-src/native
    ./configure --with-apxs=/usr/local/apache/bin/apxs && make && make install
    ```
## mod_jk configuration
- Add the following entries in httpd.conf (/usr/local/apache/conf)

    ```
    Include conf/mod_jk.conf
    ```

- Create a file under conf folder with name mod_jk.conf and add below entries

    
    ```
    LoadModule    jk_module  modules/mod_jk.so

    JkWorkersFile conf/workers.properties

    JkLogFile     logs/mod_jk.log
    JkLogLevel    emerg
    JkLogStampFormat "[%a %b %d %H:%M:%S %Y] "
    JkOptions     +ForwardKeySize +ForwardURICompat -ForwardDirectories
    JkRequestLogFormat     "%w %V %T"

    JkMount  /status  status
    JkMount  /*  balancer

    ```
    
- Create a workers.properties file with below entries
    

    ```
    worker.list=balancer,status

    worker.node1.type=ajp13
    worker.node1.port=8009
    worker.node1.host=localhost
    
    worker.node2.type=ajp13
    worker.node2.port=8010
    worker.node2.host=localhost
    
    worker.balancer.type=lb
    worker.balancer.balance_workers=node1,node2
    
    worker.status.type=status

    ```
- Restart the apache (``` apachectl restart ```)and start accessing (``` http://localhost ```) on browser


## Enable SSL on apache

- Uncomment below entries
    1. LoadModule socache_shmcb_module modules/mod_socache_shmcb.so
    2. Include conf/extra/httpd-ssl.conf

- Generate the Self Signed certificate and load it to conf/extra/httpd-ssl.conf
    
    ``` /usr/local/ssl-1.0.2h/bin/openssl req -x509 -newkey rsa:2048 -nodes -keyout /usr/local/apache/conf/server.key -out /usr/local/apache/conf/server.crt -days 365 ```
- SSL on apache is done now. You can write specific rule to redirect to your application server
