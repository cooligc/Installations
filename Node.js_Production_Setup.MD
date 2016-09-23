Load balancing set up for node.js application with nginx in RHEL6x86
====================================================================

######Here are the list of software that we need to have to have for load balancing
- nginx-1.10.1
- pcre-8.39
- zlib-1.2.8
- openssl-1.0.2f
- node

###### Node script which will create multiple server 

```
var http = require('http');

function startServer(host, port) {
  http.createServer(function (req, res) {
    res.writeHead(200, {'Content-Type': 'text/plain'});
    res.end('Response is from ' + host + ':' + port);
  }).listen(port, host);

  console.log('server started at ' + host + ':' + port);
}

startServer('localhost', 3000);
startServer('localhost', 3001);
startServer('localhost', 3002);

```

## How to install nginx-1.10.1
1. Download the source code of nginx-1.10.1 , pcre-8.39 , zlib-1.2.8 and openssl-1.0.2f
    ```
    wget http://nginx.org/download/nginx-1.10.1.tar.gz
    wget http://ftp.exim.llorien.org/pcre/pcre-8.39.tar.gz
    wget http://zlib.net/zlib-1.2.8.tar.gz
    wget https://codeload.github.com/openssl/openssl/tar.gz/OpenSSL_1_0_2f
    
    ```
2. Install openssl-1.0.2f 
  
   ```
   ./config --prefix=/usr/local/openssl && make && make install
   ```
   
3. Install nginx-1.10.1
  
  ```
   ./configure --prefix=/usr/local/nginx --with-pcre=../pcre-8.39 --with-zlib=../zlib-1.2.8 --with-http_ssl_module --with-stream --with-openssl=/usr/local/openssl/ && make && make install
  ```
  
4. Change the nginx configuration (/usr/local/nginx/conf/nginx.conf)

  ```
      # Define your "upstream" servers - the
      # servers request will be sent to
      upstream node_cluster {
  
              server 127.0.0.1:3000;      # Node.js instance 1
              server 127.0.0.1:3001;      # Node.js instance 2
              server 127.0.0.1:3002;      # Node.js instance 3
      }
      
       server {
          listen       80;
          server_name  localhost;
  
          # pass the request to the node.js server
          # with some correct headers for proxy-awareness
          location / {
                   proxy_set_header X-Real-IP $remote_addr;
                   proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
                   proxy_set_header Host $http_host;
                   proxy_set_header X-NginX-Proxy true;
  
                  proxy_pass http://node_cluster/;
                  proxy_redirect off;
  
                  # Handle Web Socket connections
                  proxy_http_version 1.1;
                  proxy_set_header Upgrade $http_upgrade;
                  proxy_set_header Connection "upgrade";
         }
  ```
5. Start the nginx and test your application using nginx ip. It should balance the node server load and route the upcoming request to the node having less load using **Round Robin** approach


## How to enable SSL in nginx
1. Create the self signed certificate using the same OpenSSL which has been installed earlier
  
   ```
   ./openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout /path/to/key/server.key -out /path/to/certificate/server.crt
   ```
2. Enable SSL
   ```
     server {
            listen       443 ssl;
            server_name  localhost;
    
            ssl_certificate      /path/to/certificate/server.key;
            ssl_certificate_key  /path/to/certificate/server.crt;
    
        #    ssl_session_cache    shared:SSL:1m;
        #    ssl_session_timeout  5m;
    
        #    ssl_ciphers  HIGH:!aNULL:!MD5;
        #    ssl_prefer_server_ciphers  on;
    
        # YOU CAN ADD YOUR RULE-SET
            location / {
                root   html;
                index  index.html index.htm;
            }
       }
    ```
