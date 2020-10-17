# nginx-fips
Patch NGINX to be compiled with the fipsld module

This is currently working on the latest amazon linux 2 AMI as of 2020-10-17 and nginx v1.19.3 release.

Steps include:

* Go here and follow these steps to ensure the server is in FIPS mode:

https://aws.amazon.com/blogs/publicsector/enabling-fips-mode-amazon-linux-2/

* Go here to download the openssl fips module into /tmp/.

https://www.openssl.org/source/

* Compiile the OpenSSL FIPS module with these steps (fipsld binary will be found here: /usr/local/ssl/fips-2.0/bin/fipsld):

```
cd /tmp
wget https://www.openssl.org/source/openssl-fips-2.0.16.tar.gz
tar -zxvf openssl-fips-2.0.16.tar.gz
cd /tmp/openssl-fips-2.0.16
./config
make
make install
```

* Download the nginx 1.19.3 source from here:

http://nginx.org/download/nginx-1.19.3.tar.gz

* Extact the nginx source and apply the patch from this git repo.

```
NGINX_NAME="nginx-1.19.3"
NGINX_TMP_DIR="/tmp/nginx"
NGINX_FIPS_PATCH_NAME="$NGINX_NAME-fips.patch"

cd /tmp
wget http://nginx.org/download/nginx-1.19.3.tar.gz
tar -zxvf nginx-1.19.3.tar.gz

cd nginx-1.19.3
&& patch -p1 <$NGINX_TMP_DIR/$NGINX_FIPS_PATCH_NAME \
&& ./configure \
--sbin-path=/usr/local/nginx/nginx \
--conf-path=/usr/local/nginx/nginx.conf \
--pid-path=/usr/local/nginx/nginx.pid \
--with-http_ssl_module \
--with-http_geoip_module \
--with-debug \
--with-stream \
--with-compat \
--with-http_realip_module \
--prefix=/etc/nginx \
--sbin-path=/usr/sbin/nginx \
--modules-path=/usr/lib64/nginx/modules \
--conf-path=/etc/nginx/nginx.conf \
--error-log-path=/var/log/nginx/error.log \
--http-log-path=/var/log/nginx/access.log \
--pid-path=/var/run/nginx.pid \
--lock-path=/var/run/nginx.lock \
--fips-mode \
&& make >>/dev/null \
&& make install >>/dev/null || \
{ echo "There was a problem building NGINX"; exit 8; }
```
