 FROM httpd:2.4
 COPY ./public-html/ /usr/local/apache2/htdocs/
RUN echo '<VirtualHost *:80> \n\
ProxyPreserveHost On \n\
ProxyPass / http://api:8080/ \n\
ProxyPassReverse / http://api:8080/ \n\
</VirtualHost> \n\
LoadModule proxy_module modules/mod_proxy.so \n\
LoadModule proxy_http_module modules/mod_proxy_http.so \n\' >> /usr/local/apache2/conf/httpd.conf
