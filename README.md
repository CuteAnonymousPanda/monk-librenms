LibreNMS meets Monk
===

This repository contains Monk.io template to deploy LibreNMS system either locally or on cloud of your choice (AWS, GCP, Azure, Digital Ocean).

This template uses [Traefik](https://traefik.io/) as a reverse proxy to provide full HTTPS encryption.
Traefik will try to obtain full SSL certificate from Lets Encrypt(assuming valid hostname is provided in the `manifest.yaml`) and if not it will fallback to a default self generated SSL certificate.

If you wish to disable Traefik(for example you have your own reverse proxy) please look at [Variables](#variables) section.

- [LibreNMS meets Monk](#librenms-meets-monk)
  - [Prerequisites](#prerequisites)
    - [Make sure monkd is running.](#make-sure-monkd-is-running)
    - [Clone Repository](#clone-repository)
    - [Load Template](#load-template)
    - [Verify if it's loaded correctly](#verify-if-its-loaded-correctly)
  - [Deploy Stack](#deploy-stack)
    - [Deploy LibreNMS Server](#deploy-librenms-server)
  - [Variables](#variables)
  - [Stop, remove and clean up workloads and templates](#stop-remove-and-clean-up-workloads-and-templates)
  - [Persistency](#persistency)

## Prerequisites
- [Install Monk](https://docs.monk.io/docs/get-monk)
- [Register and Login Monk](https://docs.monk.io/docs/acc-and-auth)
- [Add Cloud Provider](https://docs.monk.io/docs/cloud-provider)
- [Add Instance](https://docs.monk.io/docs/multi-cloud)

### Make sure monkd is running.

```bash
foo@bar:~$ monk status
daemon: ready
auth: logged in
not connected to cluster
```

### Clone Repository

```bash
git clone git@github.com:CuteAnonymousPanda/monk-librenms.git
```

### Load Template

```bash
cd monk-librenms
monk load librenms.yaml
```

### Verify if it's loaded correctly

```bash
$ monk list -l librenms

✔ Got the list
Type      Template          Repository  Version  Tags
runnable  librenms/db       local       -        -
runnable  librenms/server   local       -        -
group     librenms/stack    local       -        -
runnable  librenms/traefik  local       -        -
```

## Deploy Stack

When running in a cloud or on multi node environment it is advised that LibreNMS will be started as a whole on one node.
This can be achieved by running a run command with a `--peer` parameter.

To get a list of nodes you can run `monk cluster peers` command or `monk c peers`, and then run the stuck by running:

```bash
$ monk run --peer <yourPeerName> librenms/stack
```

### Deploy LibreNMS Server

```bash
$ monk run librenms/stack
✔ Starting the job: local/librenms/stack... DONE
✔ Preparing nodes DONE
✔ Checking/pulling images...
✔ [================================================] 100% docker.io/library/mariadb:10.5 local
✔ [================================================] 100% docker.io/library/traefik:v2.8 local
✔ [================================================] 100% docker.io/librenms/librenms:latest local
✔ [================================================] 100% docker.io/crazymax/msmtpd:latest local
✔ [================================================] 100% docker.io/library/redis:5.0-alpine local
✔ Checking/pulling images DONE
✔ Starting containers DONE
✔ Starting containers DONE
✔ Starting containers DONE
✔ New container f974f601c5bed4f378892c63cd88eff2-local-librenms-db-mariadb created DONE
✔ New container 4e6af4510139afe6604eabaa66759433-local-librenms-db-redis created DONE
✔ New container 83d62aa534b0bd9b1c04cc264ebd5ac2-cal-librenms-server-dispatcher created DONE
✔ New container c09bd751c45f8090f23ede5e38ac93da-local-librenms-server-msmtpd created DONE
✔ New container 9aac573262afb0912c4efccd80d62607-local-librenms-server-librenms created DONE
✔ New container e8dd0ff58a124d8adeef2491e63c6810-local-librenms-server-syslogng created DONE
✔ New container 8f55623cbc60ca30b86851e9fe1a902e-ocal-librenms-server-snmptrapd created DONE
✔ Started local/librenms/stack

🔩 templates/local/librenms/stack
 └─🧊 Peer local
    ├─🔩 templates/local/librenms/server
    │  └─📦 8f55623cbc60ca30b86851e9fe1a902e-ocal-librenms-server-snmptrapd
    │     ├─🧩 docker.io/librenms/librenms:latest
    │     ├─💾 /var/lib/monkd/volumes/librenms/librenms -> /data
    │     ├─🔌 open tcp 1.1.1.1:162 -> 162
    │     └─🔌 open udp 1.1.1.1:162 -> 162
    ├─🔩 templates/local/librenms/server
    │  └─📦 e8dd0ff58a124d8adeef2491e63c6810-local-librenms-server-syslogng
    │     ├─🧩 docker.io/librenms/librenms:latest
    │     ├─💾 /var/lib/monkd/volumes/librenms/librenms -> /data
    │     ├─🔌 open tcp 1.1.1.1:514 -> 514
    │     └─🔌 open udp 1.1.1.1:514 -> 514
    ├─🔩 templates/local/librenms/traefik
    │  └─📦 6ad2d4e950c8254958c0e289dc2d33e2-local-librenms-traefik-traefik
    │     ├─🧩 docker.io/library/traefik:v2.8
    │     └─💾 /var/run/podman/podman.sock -> /var/run/docker.sock
    ├─🔩 templates/local/librenms/server
    │  └─📦 c09bd751c45f8090f23ede5e38ac93da-local-librenms-server-msmtpd
    │     └─🧩 docker.io/crazymax/msmtpd:latest
    ├─🔩 templates/local/librenms/db
    │  └─📦 f974f601c5bed4f378892c63cd88eff2-local-librenms-db-mariadb
    │     ├─🧩 docker.io/library/mariadb:10.5
    │     └─💾 /var/lib/monkd/volumes/librenms_mysql -> /var/lib/mysql
    ├─🔩 templates/local/librenms/server
    │  └─📦 83d62aa534b0bd9b1c04cc264ebd5ac2-cal-librenms-server-dispatcher
    │     ├─🧩 docker.io/librenms/librenms:latest
    │     └─💾 /var/lib/monkd/volumes/librenms/librenms -> /data
    ├─🔩 templates/local/librenms/db
    │  └─📦 4e6af4510139afe6604eabaa66759433-local-librenms-db-redis
    │     └─🧩 docker.io/library/redis:5.0-alpine
    └─🔩 templates/local/librenms/server
       └─📦 9aac573262afb0912c4efccd80d62607-local-librenms-server-librenms
          ├─🧩 docker.io/librenms/librenms:latest
          ├─💾 /var/lib/monkd/volumes/librenms_data -> /data
          └─🔌 open 1.1.1.1:80 -> 8000

💡 You can inspect and manage your above stack with these commands:
        monk logs (-f) local/librenms/stack - Inspect logs
        monk shell     local/librenms/stack - Connect to the container's shell
        monk do        local/librenms/stack/action_name - Run defined action (if exists)
💡 Check monk help for more!
```

## Variables

The variables are stored in `manifest.yaml` file.
You can quickly setup by editing the values there.

| Variable           | Description                                                                    | Default              |
| ------------------ | ------------------------------------------------------------------------------ | -------------------- |
| hostname           | Your LibreNMS master hostname                                                  | <- ip-address-public |
| enable-traefik     | If disabled traffic will be routed only to LibreNMS via non-ssl encrypted port | false                |
| http-port          | Port that Traefik/LibreNMS will listen for non SSL traffic                     | 80                   |
| https-port         | Port that Traefik will listen for SSL traffic                                  | 443                  |
| db-name            | Database name that will be used to store LibreNMS data                         | librenms             |
| db-user            | Database username                                                              | librenms             |
| db-pass            | Database password                                                              | HappyIsHappyDog!     |
| timezone           | Timezone set for LibreNMS                                                      | Europe/London        |
| smtp_host          | SMTP host that will be used for sending emails                                 | smtp.gmail.com       |
| smtp_port          | SMTP port that will be used for sending emails                                 | 587                  |
| smtp_tls           | Should TLS be enabled for SMTP                                                 | on                   |
| smtp_starttls      | Should Start TLS be enabled for SMTP                                           | on                   |
| smtp_tls_checkcert | Should SMTP validate certificates                                              | on                   |
| smtp_auth          | Should we use SMTP auth                                                        | on                   |
| smtp_user          | SMTP username                                                                  | fooGoes              |
| smtp_password      | SMTP password                                                                  | ToBar                |
| smtp_from          | This will set From: for emails ls                                              | foo@gmail.com        |

## Stop, remove and clean up workloads and templates

```bash
monk purge    librenms/stack
monk purge -x librenms/stack
```

## Persistency
If you're using any of the clouds available via Monk. You can use volume definition to spin a disk block device to make your LibreNMS instance independent from the node it's running on.
To do simply uncomment the `volume` blocks in `librenms.yaml`
