# Load balancing set up for node.js application with nginx in RHEL6x86

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
    wget 
    ```
    
 ./configure --prefix=/usr/local/nginx --with-pcre=/usr/local/pcre/bin/pcre-config --with-zlib=../zlib-1.2.8 --with-http_ssl_module --with-stream --with-openssl=/usr/local/openssl/ && make && make install
nginx configuration : 
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

