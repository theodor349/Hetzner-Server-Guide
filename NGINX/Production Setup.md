# Production Setup
I currenlty have one VPS hosted at hetzner which will be used to host two types of applications: API's and webpages.
Where the API's will be accessible through the subdomain `api` and webpages through `app`.

## API's `api.theodorrisager.dk`
This endpoint is responsible for all my API endpoints.
At the moment this redirects to my **WeeklyReview** and **WorkTracker** API's.

`.../proxy/...` is added in case I want to make a default response for any request sent to `api.theodorrisager.dk`.
```
server {
        server_name api.theodorrisager.dk;

        root /home/theodor349/WorkTracking/FrontEnd/wwwroot;
        index index.html;

        location /proxy/worktracker/ {
                proxy_pass http://localhost:5003/;
        }

        location /proxy/weeklyreview/ {
                proxy_pass http://localhost:5001/;
        }

        listen 443 ssl; # managed by Certbot
        ssl_certificate /etc/letsencrypt/live/api.theodorrisager.dk/fullchain.pem; # managed by Cert>
        ssl_certificate_key /etc/letsencrypt/live/api.theodorrisager.dk/privkey.pem; # managed by Ce>
        include /etc/letsencrypt/options-ssl-nginx.conf; # managed by Certbot
        ssl_dhparam /etc/letsencrypt/ssl-dhparams.pem; # managed by Certbot
}

server {
        if ($host = api.theodorrisager.dk) {
                return 301 https://$host$request_uri;
        } # managed by Certbot


        listen 80;

        server_name api.theodorrisager.dk;
        return 404; # managed by Certbot
}
```

## Webpages `app.theodorrisager.dk`
