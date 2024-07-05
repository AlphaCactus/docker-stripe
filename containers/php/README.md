# Overview
The PHP container runs the PHP FPM version defined in the .env file. The apache servers use an internal apache proxy configured in the apache.conf VirtualHost section to forward all PHP requests to this PHP server

# Usage
Nothing is required for this container to process requests from the apache servers

## php-cli
There is currently no ssh server installed on the php containers so to access the cli for use in a terminal window you must use the terminal provided by Docker Desktop. This can be accessed by clicking on the container you need to run a command on and clicking the terminal tab.

# PHP Configuration
Files that are copied into the PHP container for configuration are opcache.ini, php.ini, and uploadsize.conf. These can be edited and will be copied the next time the container is built using the compose.yaml file.

# PHP Modules
Modules are defined in the Dockerfile and will be installed when the container is built using the `compose.yaml` file. This is done using the following lines:

RUN chmod +x /usr/local/bin/install-php-extensions && \
    install-php-extensions opcache mysqli gd bcmath zip xml intl xdebug php-memcached-dev/php-memcached@master @composer

The install-php-extensions command makes it easy to install modules and dependencies. To install a module simply edit the Dockerfile and add it to the list of extensions to be installed and then edit the php.ini file if necessary.