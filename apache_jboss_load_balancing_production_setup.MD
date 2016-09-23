Load balancing set up for JBOSS application server + apache httpd + mod_jk in linux
===================================================================================

Consider, we have 2 jboss instance running with following spectification
- JBOSS_PORT=8080, AJP_PORT=8009
- JBOSS_PORT=8081, AJP_PORT=8010

##Prerequisites Softwares
- jboss-as-7.1.1 (``` wget http://download.jboss.org/jbossas/7.1/jboss-as-7.1.0.Final/jboss-as-7.1.0.Final.tar.gz ```)
- httpd-2.4.23 (``` wget http://mirror.fibergrid.in/apache//apr/apr-1.5.2.tar.gz ```)
- apr-util-1.5.4 (``` wget http://mirror.fibergrid.in/apache//apr/apr-util-1.5.4.tar.gz ```)
- apr-1.5.2 (``` wget http://mirror.fibergrid.in/apache//apr/apr-1.5.2.tar.gz ```)
- pcre-8.39 (``` wget http://ftp.exim.llorien.org/pcre/pcre-8.39.tar.gz ```)
- openssl-1.0.2h (``` wget https://www.openssl.org/source/openssl-1.0.2h.tar.gz ```)
- mod_jk connector (``` wget http://mirror.fibergrid.in/apache/tomcat/tomcat-connectors/jk/tomcat-connectors-1.2.41-src.tar.gz ```)
