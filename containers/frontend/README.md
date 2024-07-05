# Overview

This nginx container is set up as a reverse proxy to handle virtual hosts.

Multiple `web` service containers may be configured to use the same port, port 80, and accessed from different domains.
The proxy automatically detects the configuration for each web service container via the `environment: -VIRTUAL_HOST=some-domain` in the compose.yaml.
This works because of how Docker networking works as a router.

# Usage
There is no manual configuration for this container. It is automated to watch for changes to configurations in the compose.yaml file

## Notes

proxy config created automatically by docker-gen installed on the NGINX container

### References
- https://hub.docker.com/r/jwilder/nginx-proxy
- http://jasonwilder.com/blog/2014/03/25/automated-nginx-reverse-proxy-for-docker/
- 
