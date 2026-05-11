# ansible role mit.acme

## Install

Using bash:

```sh
sudo su -s $(which bash) -l acmesh
cd && git clone https://github.com/Neilpang/acme.sh.git && cd ./acme.sh && ./acme.sh --install
acme.sh --upgrade --auto-upgrade
```

Using fish:

```sh
sudo su -s $(which fish) -l acmesh
cd && git clone https://github.com/Neilpang/acme.sh.git && cd ./acme.sh && ./acme.sh --install
# Add `source ~/.acme.sh/acme.sh.env` to `~/.config/fish/config.fish`
acme.sh --upgrade --auto-upgrade
```

Switch default CA:

```sh
acme.sh --set-default-ca --server letsencrypt
```

To use dns_nsupdate.sh install bind-tools (FreeBSD) which provides `nsupdate` .

## Usage

Add
`include conf.d/local-mit-acmesh.include;`
to nginx-server configuration for port 80.

Add
`Alias /.well-known/acme-challenge /var/lib/acmesh/tmp/.well-known/acme-challenge`
to apache configuration for port 80. If you need a redirect you have to use
`RedirectMatch "^/$" "https://www.my.dom/"`
or

```
    RewriteEngine On
    RewriteCond %{REQUEST_URI} !^/.well-known/acme-challenge
    RewriteCond %{HTTPS} !=on
    RewriteRule ^/?(.*) https://%{SERVER_NAME}/$1 [R,L]
```

instead of only `Redirect / ...`!

```sh
acme.sh --issue -w ~/tmp --reloadcmd "sudo service nginx reload" -d www.example.com
acme.sh --install-cert -d www.example.com \
    --key-file       /path/to/keyfile/in/nginx/key.pem  \
    --fullchain-file /path/to/fullchain/nginx/cert.pem \
    --reloadcmd      "sudo service nginx reload"
```

```sh
acme.sh --issue -w ~/tmp --reloadcmd "sudo service apache2 reload" -d www.my.dom
```

The `--reloadcmd` parameter leads to the config entry
`Le_ReloadCmd='__ACME_BASE64__START_...'`, so you won't find it easily.
