# Overview

This nginx container is the web server and is replicated once per site by `docker compose` via `compose.yaml`.
This container exposes the website on port 80 on the backend network and does not have any ports open for the front end website 
so the only way to access the site is via the `frontend` proxy service. The `frontend` proxy automatically detects the configuration for this container via information in the docker-compose.yaml.

## Notes

- `/configs` contains nginx configuration files for specific sites, which is referenced in the `volumes` section of service definition in `compose.yaml`.
