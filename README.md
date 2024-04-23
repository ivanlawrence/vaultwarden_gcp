# Bitwarden self-hosted on Google Cloud for Free

---

## Features

* Bitwarden self-hosted (via Vaultwarden) on Google Cloud 'always free' e2-micro tier 
* Automatic https certificate management through Caddy 2 proxy
* Dynamic DNS updates through ddclient
* Blocking brute-force attempts with fail2ban
* Country-wide blocking through iptables and ipset
* Automatic backups

## Installation
Follow the [guide in the wiki](https://github.com/dadatuputi/bitwarden_gcloud/wiki/Installation) to install and configure Bitwarden self-hosted on Google Cloud
### Create the VM in GCP
```
gcloud compute instances create bitwarden \
    --machine-type e2-micro \
    --zone us-central1-a \
    --image-project cos-cloud \
    --image-family cos-stable \
    --boot-disk-size=30GB \
    --tags http-server,https-server \
    --scopes compute-rw
```
### Create the VM;s firewall rules
```
gcloud compute firewall-rules create bitwarden-http-ingress --action allow --target-tags http-server --rules tcp:80
gcloud compute firewall-rules create bitwarden-https-ingress --action allow --target-tags https-server --rules tcp:443
```
### In the VM
Clone this repo and cd into it
```
$ sh utilities/install-alias.sh
$ source ~/.bashrc
$ docker-compose version
Docker Compose version v2.23.0
```

Copy the provided .env.template to .env. This file is self-documented and requires certain values such as a domain name, Cloudflare API tokens, etc.


## Changelog
2.0.2 - 7 November 2023

* Improve `fail2ban` SMTP env variable documentation in `.env.template` (#79)
* Update IP Header env var (#77)
* Push `fail2ban` logs to STDOUT / docker logging
* Update `docker-compose` to latest version (#76). Requires manual updating of `~/.bash_alias` with the following command:

```bash
$ docker-compose version
$ sed -i "s|docker/compose|docker compose|g" ~/.bash_alias
$ source ~/.bash_alias
$ docker-compose version
```

2.0.1 - 25 October 2023

* Update backup option to include `.env` for full restoration. Off by default. Please encrypt your backup if including `.env`
* Starting new versioning/tagging system to keep track of changes. Arbitrarily starting after 2.0, which was the fully modular approach.

---

> __3 April 2023 Alert__: [Recent changes to Vaultwarden](https://github.com/dani-garcia/vaultwarden/commit/ca417d32578c3b6224c5aa8df56eb776712941b7) may cause Vaultwarden to fail to start due to default environmental variables. `.env.template` has been updated in this repo, however, if you are affected, you must also update `.env` and comment out all `YUBICO_*` variables, so that they appear as:
>
> ```
> #YUBICO_CLIENT_ID=
> #YUBICO_SECRET_KEY=
> #YUBICO_SERVER=
> ```
> Restart with `docker-compose`, and Vaultwarden should come up as normal. Credit to [@AySz88 for reporting this](https://github.com/dadatuputi/bitwarden_gcloud/issues/54).
