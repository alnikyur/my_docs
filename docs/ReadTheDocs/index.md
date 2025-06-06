# MKDocs runned in docker

Create folder `mkdocs` and cd into this folder.  
Run `docker run --rm -itd -v "$(pwd):/app" -w /app -p 4444:8000 minidocks/mkdocs serve -a 0.0.0.0:8000 -t readthedocs`  

Nginx configuration for VHost [wiki.itmaestro.xyz](https://wiki.itmaestro.xyz)
```
server {
  listen 80;
  server_name wiki.itmaestro.xyz;

  location / {
    auth_basic           "closed site";
    auth_basic_user_file /etc/nginx/.htpasswd;
    proxy_pass http://127.0.0.1:4444;
    proxy_set_header Host $host;
    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_set_header X-Forwarded-Proto $scheme;
  }
}
```