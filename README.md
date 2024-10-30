## Overview

A `docker compose` app for developing SilverStripe applications

Support for these applications is provided:

## Docker Services
| Service name   | Image/Context                      | Description                                                                                                                                                                                                                                                          |
|----------------| ---------------------------------- |----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `frontend`     | `./containers/frontend`            | Load balancer that listens on host system for requests to domains hosted by contained services. Based on [`nginx-proxy`](https://github.com/nginx-proxy/nginx-proxy), which automatically creates listeners for services that define a `VIRTUAL_HOST` environment variable and proxies web traffic to them. |
| `mysql`        | `mysql:8.0-latest`                 | Shared database container. Available on host system at port `3306` by default.                                                                                                                                                                                       |
| `hotstart_web` | `./containers/nginx_reverse_proxy` |                                                                                                                                                                                                                                                                      |
| `hotstart_php` | `./containers/feedtest`            |                                                                                                                                                                                                                                                                      |

## Requirements

- Host machine with **Docker Desktop** installed and running.
   - Do not install Docker inside WSL! If you do, you are on your own for solving issues. 
- Check for port conflicts
   - If your host (MacOS, Ubuntu) is natively running any web services (Apache, Nginx, mysql, etc) running on default ports then the docker services may fail to setup and or run.  You will need to stop those services or change the ports used by those services or this docker environment.
   - Windows (IIS)
      - Ensure IIS (Internet Information Services) is not installed.
      - Press WINDOWS, type in `featured` and open `Turn windows features on or off`
      - Search for 'Internet Information Services' and disable.
      - You likely will have to reboot
   - Windows (Other random services)
      - Open Powershell
      - Run `netstat -aon | findstr :8055`
      - Search for the PID of the app listening on port 8055
      - run `$id = <PID>`
      - run `Get-Process | Where-Object Id -EQ $id | Select ProcessName,Id | Format-Table`
      - If docker is using port 80, you should see `ProcessName` = `com.docker.backend`

## Steps to deploy

1. Clone repositories to your working folder
   1. Open terminal and navigate to your working folder
   2. `git clone git@github.com:AlphaCactus/docker-strip.git` (this repo)
   3. `git clone git@github.com:<org>/<repo>.git`
      1. `cd <repo> & git submodule update --init & cd ..`   
2. In `docker-stripe` working folder, copy `.env-original` to `.env`.
3. Edit `.env`
   1. (Optional) The .env assumes all projects exist in the same folder as `docker-stripe` and with their default names. If any of this was changed by you, you must update the `.env` to reflect their actual locations.
   2. (Optional) You may change the domains used to access the sites in your local dev environment.
   3. *Required* Set `FPM_UID` and `FPM_GID`
6. Copy environment files.
   1. Copy `./environment/ss3/_ss_environment.php` to root of Silverstripe 3.x projects.
   2. Copy `./environment/ss4/.env` to root of the SilverStripe 4.x projects.
   3. If you chose your own unique domains in your `.env` then these environment files must also be updated to match.
      - Also update the `$_FILE_TO_URL_MAPPING` array.
   4. If you set your own unique database password in your `.env` then it must also be updated in the environment files.
6. Open a terminal window to the  location of `compose.yaml`
7. Run `docker compose up -d`
   - Wait for a few minutes while the environment is configured and started ...
   - Accept windows UAC confirmation (at the end) when prompted.
8. Configure your `/etc/hosts`
   - Configure hosts file to point to your docker host on each local domain by appending this to the file. You will need to specify the local IP address of your docker host. On MacOS and Linux, this is usually 127.0.0.1. If using WSL, this be the IP address of the Linux instance used to host Docker.
   - MacOS: `/private/etc/hosts`
   - Windows: `%WINDIR%\System32\drivers\etc\hosts`
   - If you chose your own unique domains in your `.env`, these must be updated to match.
   ```
   127.0.0.1 <project name>.ds   
   ```
9. Done!

### What's next?
- Sync Assets
   - See [Post Install Tasks: Sync Assets](./post-install-tasks.md#sync-assets)
- Connect to the db and import databases
- Visit each site to determine if it is working.
- If sites are inexplicably not working after making configuration changes, have Dev Tools open and disable the browser cache before refreshing pages. Browsers often cache error responses and redirects and will fail to attempt to fetch new data if the page response was an error.

## Common Docker Commands

- `docker-compose` **deprecated**
   - This is the legacy v1 command and should no longer be used.
   - https://docs.docker.com/compose/compose-file/legacy-versions/
   - Commonly used file `docker-composer.yaml` which has been deprecated and replaced by `compose.yaml`
   - Sometimes images alias `docker-compose` to `docker compose` so they may actually be the same.
- `docker compose up -d`
   - bring up the entire environment, building if necessary.
   - `-d` or `--detach` starts the containers in the background and leaves them running.
- `docker compose build [SERVICE...]`
   - If you change a service's Dockerfile or the contents of its build directory, run docker compose build to rebuild it.
   - Optionally set `[SERVICE...]` to build only a specific service.
- `docker compose ps`
   - List containers
- `docker volume ls`
   - List volumes
- `docker exec -it <service> composer install`
   - Update composer modules in specified container. for PHP sites that use composer 

