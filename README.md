## Install

Using bash:

```shell
sudo su -s /bin/bash -l acmesh
cd && git clone https://github.com/Neilpang/acme.sh.git && cd ./acme.sh && ./acme.sh --install
acme.sh --upgrade --auto-upgrade
```

Using fish:

```shell
sudo -u acmesh -s
cd && git clone https://github.com/Neilpang/acme.sh.git && cd ./acme.sh && ./acme.sh --install
# Add `source /var/db/acmesh/.acme.sh/acme.sh.env` to `~/.config/fish/config.fish`
acme.sh --upgrade --auto-upgrade
```

Switch default CA:

```bash
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
`RedirectMatch "^/$" "https://autoconfig.osmx.eu/"`
or
```
    RewriteEngine On
    RewriteCond %{REQUEST_URI} !^/.well-known/acme-challenge
    RewriteCond %{HTTPS} !=on
    RewriteRule ^/?(.*) https://%{SERVER_NAME}/$1 [R,L]
```
instead of only `Redirect / ...`!

```bash
acme.sh --issue -w ~/tmp --reloadcmd "sudo service nginx reload" -d db3-admin.meissner.it
```

```bash
acme.sh --issue -w ~/tmp --reloadcmd "sudo service apache2 reload" -d db3-admin.meissner.it
```

The `--reloadcmd` parameter leads to the following config entry:  
`Le_ReloadCmd='sudo service apache2 reload`

