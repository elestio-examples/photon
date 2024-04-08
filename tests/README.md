<a href="https://elest.io">
  <img src="https://elest.io/images/elestio.svg" alt="elest.io" width="150" height="75">
</a>

[![Discord](https://img.shields.io/static/v1.svg?logo=discord&color=f78A38&labelColor=083468&logoColor=ffffff&style=for-the-badge&label=Discord&message=community)](https://discord.gg/4T4JGaMYrD "Get instant assistance and engage in live discussions with both the community and team through our chat feature.")
[![Elestio examples](https://img.shields.io/static/v1.svg?logo=github&color=f78A38&labelColor=083468&logoColor=ffffff&style=for-the-badge&label=github&message=open%20source)](https://github.com/elestio-examples "Access the source code for all our repositories by viewing them.")
[![Blog](https://img.shields.io/static/v1.svg?color=f78A38&labelColor=083468&logoColor=ffffff&style=for-the-badge&label=elest.io&message=Blog)](https://blog.elest.io "Latest news about elestio, open source software, and DevOps techniques.")

# Photon, verified and packaged by Elestio

[photon](https://phtn.app/) is a replacement for lemmy-ui with more features, a better design, and more customizability.

<img src="https://github.com/elestio-examples/photon/raw/main/photon.png" alt="photon" width="800">

Deploy a <a target="_blank" href="https://elest.io/open-source/photon">fully managed Photon</a> on <a target="_blank" href="https://elest.io/">elest.io</a> if you want automated backups, reverse proxy with SSL termination, firewall, automated OS & Software updates, and a team of Linux experts and open source enthusiasts to ensure your services are always safe, and functional.

[![deploy](https://github.com/elestio-examples/photon/raw/main/deploy-on-elestio.png)](https://dash.elest.io/deploy?source=cicd&social=dockerCompose&url=https://github.com/elestio-examples/photon)

# Why use Elestio images?

- Elestio stays in sync with updates from the original source and quickly releases new versions of this image through our automated processes.
- Elestio images provide timely access to the most recent bug fixes and features.
- Our team performs quality control checks to ensure the products we release meet our high standards.

# Usage

## Git clone

You can deploy it easily with the following command:

    git clone https://github.com/elestio-examples/photon.git

Copy the .env file from tests folder to the project directory

    cp ./tests/.env ./.env

Edit the .env file with your own values.

Run the project with the following commands

    ./scripts/preInstall.sh
    docker-compose up -d

You can access the Web UI at: `http://your-domain:6580`

## Docker-compose

Here are some example snippets to help you get started creating a container.

    version: "3"

    services:
      proxy:
        image: nginx:1-alpine
        ports:
            # only ports facing any connection from outside
            - 172.17.0.1:6580:80
            # - 1236:1236
            # - 8536:8536
        volumes:
            - ./nginx.conf:/etc/nginx/nginx.conf:ro,Z
            # setup your certbot and letsencrypt config
            # - ./certbot:/var/www/certbot
            # - ./letsencrypt:/etc/letsencrypt/live
        restart: always
        depends_on:
            - pictrs
            - web

      lemmy:
        image: dessalines/lemmy:${LEMMY_VERSION_TAG}
        hostname: lemmy
        restart: always
        environment:
            - RUST_LOG="warn,lemmy_server=info,lemmy_api=info,lemmy_api_common=info,lemmy_api_crud=info,lemmy_apub=info,lemmy_db_schema=info,lemmy_db_views=info,lemmy_db_views_actor=info,lemmy_db_views_moderator=info,lemmy_routes=info,lemmy_utils=info,lemmy_websocket=info"
        volumes:
            - ./lemmy.hjson:/config/config.hjson:Z
        depends_on:
            - postgres
            - pictrs

      pictrs:
        image: asonix/pictrs:0.3.1
        # this needs to match the pictrs url in lemmy.hjson
        hostname: pictrs
        # we can set options to pictrs like this, here we set max. image size and forced format for conversion
        # entrypoint: /sbin/tini -- /usr/local/bin/pict-rs -p /mnt -m 4 --image-format webp
        environment:
            - PICTRS__API_KEY=${API_KEY}
        user: 991:991
        volumes:
            - ./pictrs:/mnt
        restart: always

      postgres:
        image: postgres:15-alpine
        # this needs to match the database host in lemmy.hson
        hostname: postgres
        environment:
          - POSTGRES_USER=${POSTGRES_USER}
          - POSTGRES_PASSWORD=${POSTGRES_PASSWORD}
          - POSTGRES_DB=lemmy
        volumes:
          - ./pgdata:/var/lib/postgresql/data
        restart: always

      web:
        image: ghcr.io/xyphyn/photon:${SOFTWARE_VERSION_TAG}
        restart: always
        environment:
          - PUBLIC_INSTANCE_URL=${DOMAIN}
          - PUBLIC_LOCK_TO_INSTANCE="true"
        ports:
          - "172.17.0.1:9009:3000"

### Environment variables

|       Variable       |   Value (example)   |
| :------------------: | :-----------------: |
|       API_KEY        |    your-password    |
|  POSTGRES_PASSWORD   |    your-password    |
|    POSTGRES_USER     |      postgres       |
|    ADMIN_PASSWORD    |    your-password    |
|     ADMIN_EMAIL      |   admin@email.com   |
|        DOMAIN        |     your.domain     |
|       BASE_URL       | https://your.domain |
| SOFTWARE_VERSION_TAG |       latest        |
|  LEMMY_VERSION_TAG   |       0.19.3        |
| PUBLIC_INSTANCE_URL  |     your.domain     |

# Maintenance

## Logging

The Elestio Photon Docker image sends the container logs to stdout. To view the logs, you can use the following command:

    docker-compose logs -f

To stop the stack you can use the following command:

    docker-compose down

## Backup and Restore with Docker Compose

To make backup and restore operations easier, we are using folder volume mounts. You can simply stop your stack with docker-compose down, then backup all the files and subfolders in the folder near the docker-compose.yml file.

Creating a ZIP Archive
For example, if you want to create a ZIP archive, navigate to the folder where you have your docker-compose.yml file and use this command:

    zip -r myarchive.zip .

Restoring from ZIP Archive
To restore from a ZIP archive, unzip the archive into the original folder using the following command:

    unzip myarchive.zip -d /path/to/original/folder

Starting Your Stack
Once your backup is complete, you can start your stack again with the following command:

    docker-compose up -d

That's it! With these simple steps, you can easily backup and restore your data volumes using Docker Compose.

# Links

- <a target="_blank" href="https://github.com/Xyphyn/photon">Photon Github repository</a>

- <a target="_blank" href="https://github.com/elestio-examples/photon">Elestio/Photon Github repository</a>
