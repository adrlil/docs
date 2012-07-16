

Nginx Installation Notes
========================
`Nginx <http://wiki.nginx.org/Main>`_ is a free, open-source, high-performance HTTPserver and reverse proxy, as well as an IMAP/POP3 proxy server. Unlike traditional servers, Nginx doesn't rely on threads to handle requests. Instead it uses a much more scalable event-driven (asynchronous) architecture. This architecture uses small, but more importantly, predictable amounts of memory under load. Normally, the `PHP-FPM <http://php-fpm.org/>`_ (FastCGI Process Manager) is used to integratePHP with Nginx. Nowadays, PHP-FPM is bundled with any Unix PHP distribution.  Phalcon+Nginx+PHP-FPM provides an interesting strategy to increase the performance of your websites. 

Configuring Nginx for Phalcon
-----------------------------
The following are possible configurations you can use to setup nginx with Phalcon.This first settings allow you to configure nginx as an dedicated instance for Phalcon: 

.. code-block:: nginx

    server {
         listen    80;
         server_name  localhost;
    
         charset utf-8;
    
         #access_log  /var/log/nginx/host.access.log  main;
    
         location / {
              root   /srv/www/htdocs/phalcon-website/public;
              index  index.php index.html index.htm;
    
              #if file exists return it right away
              if (-f $request_filename) {
                break;
              }
    
              # otherwise rewrite it
              if (!-e $request_filename) {
                rewrite ^(.+)$ /index.php?_url=$1 last;
                break;
              }
         }
    
         location ~ \.php {
          # try_files $uri =404;
    
          fastcgi_index /index.php;
          fastcgi_pass 127.0.0.1:9000;
    
          include fastcgi_params;
          fastcgi_split_path_info ^(.+\.php)(/.+)$;
          fastcgi_param PATH_INFO $fastcgi_path_info;
          fastcgi_param PATH_TRANSLATED $document_root$fastcgi_path_info;
          fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
        }
    
        location ~* ^/(css|img|js|flv|swf|download)/(.+)$ {
          root $root_path;
        }
    }
    

And this second configuration allow you to have different configurations by host:

.. code-block:: nginx
    
    server {
       listen 80;
       server_name localhost;
       set $root_path '/var/www/$host/public';
       root $root_path;
    
       access_log /var/log/nginx/$host-access.log;
       error_log  /var/log/nginx/$host-error.log error;
    
       index index.php index.html index.htm;
    
       try_files $uri $uri/ @rewrite;
    
       location @rewrite {
        rewrite ^/(.*)$ /index.php?_url=$1;
       }
    
       location ~ \.php {
        # try_files $uri =404;
    
        fastcgi_index /index.php;
        fastcgi_pass 127.0.0.1:9000;
    
        include fastcgi_params;
        fastcgi_split_path_info ^(.+\.php)(/.+)$;
        fastcgi_param PATH_INFO $fastcgi_path_info;
        fastcgi_param PATH_TRANSLATED $document_root$fastcgi_path_info;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
       }
    
       location ~* ^/(css|img|js|flv|swf|download)/(.+)$ {
        root $root_path;
       }
    
       location ~ /\.ht {
        deny all;
       }
    }
    
Preparing Phalcon to Nginx
--------------------------
If you're using the default router.Nginx puts a slash (/) at the beggining of the $_GET['_url']. Is necessary to remove the URL for a proper operation of the router. 

.. code-block:: php

    <?php
    
    error_reporting(E_ALL);
    
    try {
    
       if(isset($_GET["_url"])){
           $_GET["_url"] = preg_replace("#^/#", "", $_GET["_url"]);
       }
    
       $front = Phalcon_Controller_Front::getInstance();
    
       $config = new Phalcon_Config_Adapter_Ini("../app/config/config.ini");
       $front->setConfig($config);
    
       echo $front->dispatchLoop()->getContent();
    
    } catch(Phalcon_Exception $e){
       echo "PhalconException: ", $e->getMessage();
    }
