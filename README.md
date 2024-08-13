# Install NGiNX module from source on Mac OSX

1. Install NGiNX with [Homebrew](https://brew.sh):
> If you already have your NGiNX installation, just skip this step.
```
brew install nginx
```

2. Create and enter a temporary folder to compile NGiNX and modules from source:
```
mkdir ~/tmp-dir && cd ~/tmp-dir
```

3. Download and unpack source code of NGiNX. It MUST be the same version as we got from the following command:
```
nginx -v
```
```
curl -OL https://nginx.org/download/nginx-X.XX.X.tar.gz
```
```
tar xvzf nginx-X.XX.X.tar.gz && rm nginx-X.XX.X.tar.gz
```

4. Download and unpack source code of module you want to add. For example 
ngx_http_js_module (NJS):
```
curl -OL http://hg.nginx.org/njs/archive/tip.tar.gz
```
```
tar xvzf tip.tar.gz && rm tip.tar.gz
```

5. Find current NGiNX compile flags we will use for (re)compiling:
```
nginx -V
```

6. Copy-paste these flags and if are present, remove all `--add-dynamic-module` flags. These flags are used for adding additional modules. Because these modules are already compiled, we don't need to recompile them.

7. Use updated flags and add to them new flag `--add-dynamic-module` that will point to our module. Finally (re)compile NGiNX with updated flags. It should look like this:
```
cd nginx-X.XX.X
```
```
sudo ./configure --prefix=/usr/local/Cellar/nginx/X.XX.X --sbin-path=/usr/local/Cellar/nginx/X.XX.X/bin/nginx --with-cc-opt='-I/usr/local/opt/pcre/include -I/usr/local/opt/openssl/include' --with-ld-opt='-L/usr/local/opt/pcre/lib -L/usr/local/opt/openssl/lib' --conf-path=/usr/local/etc/nginx/nginx.conf --pid-path=/usr/local/var/run/nginx.pid --lock-path=/usr/local/var/run/nginx.lock --http-client-body-temp-path=/usr/local/var/run/nginx/client_body_temp --http-proxy-temp-path=/usr/local/var/run/nginx/proxy_temp --http-fastcgi-temp-path=/usr/local/var/run/nginx/fastcgi_temp --http-uwsgi-temp-path=/usr/local/var/run/nginx/uwsgi_temp --http-scgi-temp-path=/usr/local/var/run/nginx/scgi_temp --http-log-path=/usr/local/var/log/nginx/access.log --error-log-path=/usr/local/var/log/nginx/error.log --with-compat --with-debug --with-http_addition_module --with-http_auth_request_module --with-http_dav_module --with-http_degradation_module --with-http_flv_module --with-http_gunzip_module --with-http_gzip_static_module --with-http_mp4_module --with-http_random_index_module --with-http_realip_module --with-http_secure_link_module --with-http_slice_module --with-http_ssl_module --with-http_stub_status_module --with-http_sub_module --with-http_v2_module --with-ipv6 --with-mail --with-mail_ssl_module --with-pcre --with-pcre-jit --with-stream --with-stream_realip_module --with-stream_ssl_module --with-stream_ssl_preread_module --add-dynamic-module=../njs-XXXXXXXXXXXX/nginx
```
```
sudo make modules
```

8. Install compiled module:
```
mkdir /opt/homebrew/Cellar/nginx/X.XX.X/modules
```
```
cp objs/ngx_*.so /opt/homebrew/Cellar/nginx/X.XX.X/modules
```

9. Now you should be able to load new module by adding the following command to your `nginx.conf` file:
```
load_module modules/ngx_http_js_module.so;
```
> File `nginx.conf` is located at `/opt/homebrew/etc/nginx/nginx.conf`. Everytime you add/load new module reload NGiNX with `brew services reload nginx`.
