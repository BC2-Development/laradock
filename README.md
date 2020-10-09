# This is forked from laradock template

## Step to setup certbot:

1. - Edit the laradock/nginx/sites/default.conf (may or may not be useful, in our case it works without changing the path)
a - root /var/www/public; to root /var/www;

2. - Edit the laradock/docker-compose.yml
a - Add nginx volumes:
- ./data/certbot/certs/:/var/certs
- ./certbot/letsencrypt/:/var/www/letsencrypt
b - Update certbot `docker-compose.yml` to the correct email and domain

2. - docker-compose up -d nginx

3. - docker-compose up -d certbot
***** Important: run certbot after nginx

4. - docker-compose logs certbot

5. Correct the nginx conf file to enable 443 ssl and private keys:

```
listen 80;
listen [::]:80;
# For https
listen 443 ssl;
listen [::]:443 ssl ipv6only=on;

###### GENERATED CERTS AND KEYS #######
ssl_certificate /var/certs/cert1.pem;
ssl_certificate_key /var/certs/privkey1.pem;
#####################################

server_name docker.yourdomain.com;
root /var/www;
index index.php index.html index.htm;
location / {
     try_files $uri $uri/ /index.php$is_args$args;
}
location /pma {
    try_files $uri $uri/ /pma/index.php?$args;
}
location /project1 {
    try_files $uri $uri/ /project1/index.php?$args;
}
location /project2 {
    try_files $uri $uri/ /project2/index.php?$args;
}
```

6. Down and  up for changes:
```
docker-compose down
docker-compose up -d nginx
```


## Troubleshooting

If at any point you encounter any permission error in the docker. Go to `docker-compose exec workspace bash`, then change EVERY FILE owner to laradock:laradock

`chown -R laradock:laradock /var/www/*`
