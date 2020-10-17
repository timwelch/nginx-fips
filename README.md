# nginx-fips
Patch NGINX to be compiled with the fipsld module

This is currently working on the latest amazon linux 2 AMI as of 2020-10-17 and nginx v1.19.3 release.

Steps include:

* Go here and follow these steps to ensure the server is in FIPS mode:

https://aws.amazon.com/blogs/publicsector/enabling-fips-mode-amazon-linux-2/

* Go here to download the openssl fips module.

https://www.openssl.org/source/

* Compiile the OpenSSL FIPS module with these steps:

```
echo "Compiling OpenSSL FIPS module..."
tar -xzf openssl-fips-2.0.16.tar.gz -C /tmp \
&& cd /tmp/openssl-fips-2.0.16 \
&& ./config \
&& make >>/dev/null \
&& make install >>/dev/null \
&& cp -a /usr/local/ssl/fips-2.0 /etc/pki/tls/ || \
{ echo "There was a problem building FIPS module"; exit 3; }
```

