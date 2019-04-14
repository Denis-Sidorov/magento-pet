# Magento base environment

## Configuration

Most important parameters configure with environment variables specified in .env file. 
Variables pass into containers by docker-compose.

## Create project

    docker-compose exec app composer create-project --repository=https://repo.magento.com/ magento/project-community-edition /var/www/app/src

## Permissions

### Container

    docker-compose exec app chmod g+rwX -R .
    docker-compose exec app find var generated vendor pub/static pub/media app/etc -type d -exec chmod g+s {} +    
    docker-compose exec app chown -R :www-data .
    docker-compose exec app chmod 775 bin/magento

### Host (local)

To fix problem with file permissions on host, create group with gid `82` (www-data in container) and add it to your user:
    
    sudo groupadd -g 82 docker-app
    sudo usermod -a -G docker-app $USER

## SSL

    openssl req -newkey rsa:2048 -nodes -keyout docker/nginx/ssl/localhost.key -x509 -days 365 -out docker/nginx/ssl/localhost.crt

## Setup magento

### Helpers

    docker-compose exec app bin/magento info:language:list
    docker-compose exec app bin/magento info:currency:list
    docker-compose exec app bin/magento info:timezone:list

### Magento install

    docker-compose exec app bin/magento setup:install \
        --base-url=http://localhost/ \
        --base-url-secure=https://localhost \
        --use-secure=1 \
        --use-secure-admin=1 \
        --backend-frontname=admin \
        --db-host=mage-db \
        --db-name=mage \
        --db-user=magento \
        --db-password=Qwerty123! \
        --backend-frontname=admin \
        --admin-firstname=admin \
        --admin-lastname=admin \
        --admin-email=admin@admin.com \
        --admin-user=admin \
        --admin-password=Qwerty123! \
        --language=ru_RU \
        --currency=RUB \
        --timezone=Europe/Samara \
        --use-rewrites=1

### Dev mode

https://devdocs.magento.com/guides/v2.3/config-guide/cli/config-cli-subcommands-mode.html

    docker-compose exec app rm -rf  generated/code/* generated/metadata/*
    
    docker-compose exec app bin/magento deploy:mode:set developer

# TODO

1. Cron

1. LetsEncrypt