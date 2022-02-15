# Creating new Routing Options
This section will descripe how to create a new route from a new url e.g. `x.theodorrisager.dk` to an internal service running on the VPS.

## Create new Rout Option with HTTPS
First create a new DNS record [here](https://www.simply.com/dk/controlpanel/theodorrisager.dk/dns/) with the hostname `x.theodorrisager.dk` and value set to the IP of the VPS.
Then follow this [tutorial](https://community.hetzner.com/tutorials/add-ssl-certificate-with-lets-encrypt-to-nginx-on-ubuntu-20-04?fbclid=IwAR0jE78_-Iv0Cjqt0b56lOZApjwqrNvW81Dq9IrL79y6WDCPSnKk8LDoSO0) to create a new routing option for `x.theodorrisager.dk`, where you replace `example.com` with `x.theodorrisager.dk`.

## Create the Reverse Proxy
Here we need to redirect the incomming call on `x.theodorrisager.dk` to the internal ip e.g. `localhost:5000`.

To do this we need to edit the file found here `/etc/nginx/sites-enabled/x.theodorrisager.dk`, this can be done using nano and sudo access. It will look somthing like this
```
server {

        server_name x.theodorrisager.dk;

        root /var/www/x.theodorrisager.dk/html;
        index index.html;

        location / {
                try_files $uri $uri/ =404;
        }

        listen 443 ssl; # managed by Certbot
        ssl_certificate /etc/letsencrypt/live/x.theodorrisager.dk/fullchain.pem; # managed by Certbot
        ssl_certificate_key /etc/letsencrypt/live/x.theodorrisager.dk/privkey.pem; # managed by Certbot
        include /etc/letsencrypt/options-ssl-nginx.conf; # managed by Certbot
        ssl_dhparam /etc/letsencrypt/ssl-dhparams.pem; # managed by Certbot
}
server {
        if ($host = x.theodorrisager.dk) {
                return 301 https://$host$request_uri;
        } # managed by Certbot

        listen 80;

        server_name x.theodorrisager.dk;
        return 404; # managed by Certbot
}
```

In order to create a change the `url/` redirect to `localhost:5000` we need change the `location /` object to look like this
```
location / {
        proxy_pass http://localhost:5000;
}
```
If we want to create a new routing rule for example for mapping `x.theodorrisager.dk/app1` to `localhost:5001`, then we just create new location below the other location like this 
```
location /app1 {
        proxy_pass http://localhost:5001;
}
```

That was it, now you app should be able to be accessed through HTTPS.
