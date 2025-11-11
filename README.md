# CHATWOOT-N8N-WAHA
MELHOR FORMA QUE ENCONTREI PARA SUBIR CONDENSADO EM 1 SEM PROBLEMAS

1- EXECUTE PRIMEIRO O CHATWOOT

 docker compose run --rm rails bundle exec rails db:chatwoot_prepare

Depois

2 - EXECUTE TODOS PARA FICAR UP
 
docker compose -f n8n.yml -f waha.yml -f chatwoot.yml up -d

WEB ----
Configure Nginx and Let’s Encrypt
​
1. Configure Nginx to serve as a frontend proxy

Copy
sudo apt-get install nginx
cd /etc/nginx/sites-enabled
nano yourdomain.com.conf
​
2. Use the following Nginx config
Use the following Nginx config after replacing the yourdomain.com in server_name.

Copy
server {
  server_name <yourdomain.com>;

  # Point upstream to Chatwoot App Server
  set $upstream 127.0.0.1:3000;

  # Nginx strips out underscore in headers by default
  # Chatwoot relies on underscore in headers for API
  # Make sure that the config is set to on.
  underscores_in_headers on;
  location /.well-known {
    alias /var/www/ssl-proof/chatwoot/.well-known;
  }

  location / {
    proxy_pass_header Authorization;
    proxy_pass http://$upstream;
    proxy_set_header Upgrade $http_upgrade;
    proxy_set_header Connection "upgrade";
    proxy_set_header Host $host;
    proxy_set_header X-Forwarded-Proto $scheme;
    proxy_set_header X-Forwarded-Ssl on; # Optional

    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;

    proxy_http_version 1.1;
    proxy_buffering off;

    client_max_body_size 0;
    proxy_read_timeout 36000s;
    proxy_redirect off;
  }
  listen 80;
}
​
3. Verify and reload Nginx config

Copy
nginx -t
systemctl reload nginx
​
4. Run Let’s Encrypt to configure SSL certificate

Copy
apt install certbot
apt-get install python3-certbot-nginx
mkdir -p /var/www/ssl-proof/chatwoot/.well-known
certbot --webroot -w /var/www/ssl-proof/chatwoot/ -d yourdomain.com -i nginx
​
5. Access your installation
Your Chatwoot installation should be accessible from the https://yourdomain.com now.
​
Steps to build images yourself
We publish our base images to the Docker hub. You should be able to build your Chatwoot web/worker images from these base images.
​

<img width="598" height="335" alt="image" src="https://github.com/user-attachments/assets/0744edb4-f6d9-4f24-835a-d254d5648000" />
