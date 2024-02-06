# Using Xdebug in Docker Container with PHP 7.4 and XDebug 3.1.5

This repository provides a setup for using Xdebug in a Docker container for PHP development. Follow the steps below to get started.

## Prerequisites

- Docker installed on your machine
- Visual Studio Code
- PHP Debug extension on Visual Studio Code

## Configuration

### Docker Compose

Ensure that your `docker-compose.yaml` file is configured as follows:

```yaml
version: "3.1"
  php:
    build:
      context: .
      dockerfile: Dockerfile
    restart: always
    volumes:
      - ./docker/php/conf.d/xdebug.ini:/usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini
      - ./docker/php/conf.d/error_reporting.ini:/usr/local/etc/php/conf.d/error_reporting.ini
    extra_hosts:
      - 'host.internal.docker:host-gateway'
    ports:
      - "80:80"
      - "443:443"
```

## Dockerfile
Ensure that your Dockerfile contains the necessary commands to install Xdebug:

```Dockerfile
# This will go together with your dependencies
RUN apt-get update \
    && pecl install xdebug-3.1.5 \
    && docker-php-ext-enable xdebug
```

## Launch Configuration (launch.json)
Ensure that your launch.json is configured properly. This file is used for debugging with Visual Studio Code:\
Note: While I was doing my first use of xdebug in a container, I was making a mistake, in this json we have a line that setup the pathMappings, pay attention for the path to your project,
in my case, I need to define using /var/www/html/project
```json
{
    "version": "0.2.0",
    "configurations": [
        {
            "name": "Docker Listen for Xdebug",
            "type": "php",
            "request": "launch",
            "port": 9003,
            "pathMappings": {
                "/var/www/html/": "${workspaceFolder}"
            }
        }
    ]
}
```

## Xdebug Configuration (xdebug.ini)
Ensure that your xdebug.ini contains the necessary configurations for Xdebug:

```ini
zend_extension=xdebug

[XDebug]
xdebug.mode=debug
xdebug.idekey=vsc
xdebug.remote_autostart=off
xdebug.start_with_request=yes
xdebug.client_host=host.docker.internal
```

## Usage
1. Start the Docker containers:

````bash
docker-compose up -d
````
2. Open your project in Visual Studio Code.

3. Set breakpoints in your PHP code.

4. Start debugging by selecting the appropriate configuration in Visual Studio Code.

5. Access your application in a web browser.

6. Xdebug will trigger breakpoints set in your PHP code, allowing you to debug interactively.

## Additional Notes
* Ensure that your IDE is configured to listen for Xdebug connections on the specified port.
* Make sure your firewall allows connections to the Xdebug port.
* For troubleshooting, refer to the Xdebug documentation and your IDE's debugging setup guide.
