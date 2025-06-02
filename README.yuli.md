## Perfetto UI Compilation and Deployment Guide

Build UI

```bash
tools/install-build-deps --ui
ui/build
# This generates the compiled UI files in `out/ui/ui/dist/`
```

Package the Build

```bash
# Navigate to the build output directory and create deployment package:
cd out/ui/ui/
mkdir perfetto
cp -R . ./perfetto
tar czvf perfetto.tar.gz perfetto
# scp to remote
```

Deploy to Server

```bash
# Extract the package
tar xzvf ~/projects/perfetto.tar.gz

# Set proper ownership
chown -R root:root perfetto/
```

Configure MIME Types

```bash
Add WebAssembly support to `/etc/nginx/mime.types`, application/wasm wasm;
```

Update Nginx Configuration

```bash
# /etc/nginx/sites-available/default, two server blocks

location /pets/perfetto/ {
    alias /var/www/html/pets/perfetto/;
    try_files $uri $uri/ @perfetto_fallback;
}

location @perfetto_fallback {
    rewrite ^.*$ /pets/perfetto/index.html last;
}
```

Reload nginx to apply changes

```bash
nginx -t
systemctl reload nginx
```

Log

```bash
tail -f /var/log/nginx/access.log
```
