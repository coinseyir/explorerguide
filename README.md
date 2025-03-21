# explorerguide

Explorer
Copy
sudo apt autoremove nodejs -y
curl -fsSL https://deb.nodesource.com/setup_19.x | sudo -E bash -
curl -sL https://dl.yarnpkg.com/debian/pubkey.gpg | gpg --dearmor | sudo tee /usr/share/keyrings/yarnkey.gpg >/dev/null
echo "deb [signed-by=/usr/share/keyrings/yarnkey.gpg] https://dl.yarnpkg.com/debian stable main" | sudo tee /etc/apt/sources.list.d/yarn.list
sudo apt update
sudo apt install nginx certbot python3-certbot-nginx nodejs git yarn -y
NGINX Configuration
Copy
sudo nano /etc/nginx/sites-enabled/explorer.ynranil.com.conf
Create this sample configuration

Copy
server {
    listen       80;
    listen  [::]:80;
    server_name explorer.dnsarz.xyz;

    #access_log  /var/log/nginx/host.access.log  main;

    location / {
        root /usr/share/nginx/html;
        index  index.html index.htm;
        try_files $uri $uri/ /index.html;
    }

    #error_page  404              /404.html;

    # redirect server error pages to the static page /50x.html
    #
    error_page   500 502 503 504  /50x.html;
    location = /50x.html {
        root   /usr/share/nginx/html;
    }

    gzip on;
    gzip_proxied any;
    gzip_static on;
    gzip_min_length 1024;
    gzip_buffers 4 16k;
    gzip_comp_level 2;
    gzip_types text/plain application/javascript application/x-javascript text/css application/xml text/javascript application/x-httpd-php application/vnd.ms-fontobject font/ttf font/opentype font/x-woff image/svg+xml;
    gzip_vary off;
    gzip_disable "MSIE [1-6]\.";
}
SSL Configuration
Copy
sudo certbot --nginx --register-unsafely-without-email
Copy
sudo systemctl restart nginx
Explorer Configuration
Copy
cd $HOME
git clone https://github.com/ping-pub/explorer
Go kurulumuUpdate ve gereklilikleri kuralum
Copy
nano $HOME/explorer/chains/mainnet/empower.json
Copy
{
    "chain_name": "empower",
    "api": ["https://empower-testnet-api.polkachu.com"],
    "rpc": ["https://empower-testnet-rpc.polkachu.com"],
    "coingecko": "",
    "snapshot_provider": "",
    "sdk_version": "0.47.1",
    "coin_type": "118",
    "min_tx_fee": "500",
    "addr_prefix": "empower",
    "logo": "https://explorer.nodexcapital.com/logos/empower.png",
    "assets": [{
        "base": "umpwr",
        "symbol": "MPWR",
        "exponent": "6",
        "coingecko_id": "",
        "logo": "https://explorer.nodexcapital.com/logos/empower.png"
    }]
  }
Go kurulumuUpdate ve gereklilikleri kuralum
Copy
cd $HOME/explorer
yarn && yarn build

/// or

yarn install --ignore-engines
cd $HOME/explorer
yarn && yarn build
Copy web file to Nginx html folder

Copy
sudo cp -r $HOME/explorer/dist/* /usr/share/nginx/html
sudo systemctl restart nginx
API / RPC  / JSONRPC 

sudo apt -q update

sudo apt -qy install curl git jq lz4 build-essential snapd unzip nginx

sudo apt -qy upgrade

sudo nano /etc/nginx/sites-available/crossfi-testnet-api

Copy
server {
    listen 80;
    server_name crossfi-testnet-api.ynranil.com;

    location / {
        add_header Access-Control-Allow-Origin *;
        add_header Access-Control-Max-Age 3600;
        add_header Access-Control-Expose-Headers Content-Length;

        proxy_pass http://127.0.0.1:1317/;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    }
}

sudo nano /etc/nginx/sites-available/crossfi-testnet-rpc

Copy
server {
    listen 80;
    server_name crossfi-testnet-rpc.ynranil.com;

    location / {
        proxy_pass http://127.0.0.1:26657/;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    }
}
sudo nano /etc/nginx/sites-available/crossfi-testnet-jsonrpc

Copy
server {
    listen 80;
    server_name crossfi-testnet-jsonrpc.ynranil.com;

    location / {
        proxy_pass http://127.0.0.1:8545/;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    }
}
sudo ln -s /etc/nginx/sites-available/crossfi-testnet-* /etc/nginx/sites-enabled/

sudo nginx -t

sudo systemctl reload nginx

sudo certbot --nginx --register-unsafely-without-email

sudo systemctl restart nginx
