# Contents
This repository contains the following Docker images:
- [flexget-docker-config](https://hub.docker.com/r/anthonyguerreiro/flexget-docker-config/) - my [Flexget configuration](https://github.com/AnthonyGuerreiro/flexget_config/) based on one of the [flexget-python](https://github.com/AnthonyGuerreiro/flexget-docker/) images.
- [rpi-transmission](https://hub.docker.com/r/anthonyguerreiro/rpi-transmission/) - Transmission 2.8+ for the Raspberry Pi based on [sdhibit/rpi-raspbian](https://hub.docker.com/r/sdhibit/rpi-raspbian/).
- [docker-compose](utils/docker-compose) files ready to run *flexget-docker-config* and *transmission* images.
- [default](utils/defaults) files used to build **flexget-docker-config**.

My Flexget configuration is heavily based on [Jonybat's](https://github.com/Jonybat/flexget_config/).


## flexget-docker-config

### build
**flexget-docker-config** is pre-populated with:
- [anime.yml](utils/defaults/anime.yml) (example file with one entry)
- [secrets.yml](utils/defaults/secrets.yml) (example file, values are replaced by *ENV variables*)
- [entrypoint.sh](utils/defaults/entrypoint.sh) (script to replace *ENV variables* in secrets.yml (first run) and startup the container)
- [db-config.sqlite](utils/defaults/db-config.sqlite) (empty *Flexget* database)
- [config.yml](https://github.com/AnthonyGuerreiro/flexget_config/blob/master/config.yml) (My *Flexget* configuration)

The image can be built with any other configuration preloaded or a pre-populated flexget database by passing the build args to the *Dockerfile*, such as:

```
docker build -t myimage --build-arg anime_yml=path/to/anime.yml \
                        --build-arg secrets_yml=path/to/secrets.yml \
                        --build-arg entrypoint=path/to/entrypoint.sh \
                        --build-arg flexget_db=path/to/db-config.sqlite \
                        --build-arg config_yml=path/to/config.yml .
```

The [entrypoint](utils/defaults/entrypoint.sh) uses the *ENV variables* provided in runtime to finish the configuration by replacing them in **secrets.yml**. It also builds up the folder structure used by my configuration and requests a [trakt](https://trakt.tv/) authentication token on startup.

### runtime
The *ENV variables* are required to run this flexget configuration:
- trakt_user (to fetch series/movies)
- mal_user (to fetch anime)
- pushbullet_token (pushbullet api key obtained from [pushbullet](https://www.pushbullet.com/#settings/account) to notify downloads)

It's also recommended to map a volume to the container */downloads* folder to be able to access the downloads directly from the host.

##### Running the container
Example:

`docker run --rm -v /tmp:/downloads -e trakt_user=myuser -e mal_user=myotheruser -e pushbullet_token=myapikey -it anthonyguerreiro/flexget-docker-config /bin/sh`

After starting the container for the first time, check logs (or start in interactive mode) to view flexget message requesting access to your trakt account:
```
Accessing trakt for authentication token..
Please visit https://trakt.tv/activate and authorize Flexget. Your user code is 4CD47ACE. Your code expires in 10.0 minutes.
```

Once Flexget receives the authorization, *Flexget daemon* will be started.



## docker-compose
The [docker-compose](utils/docker-compose) files provided start **flexget-docker-config** alongside a container running transmission.

The [v3.7](utils/docker-compose/docker-compose.yml) one is a standalone, but the [v1](utils/docker-compose/docker-compose.yml.v1) is provided with a [script](utils/docker-compose/docker-compose-v1.sh) to run it easier, because docker-compose v1 does not support variable substitution.

Both assume the *ENV variables* mentioned in [runtime](#runtime) and an extra *ENV variable* **download_folder** to map the downloads folder between *flexget-python*, *transmission* and the host.

##### docker-compose env variables
The easiest way to provide *ENV variables* to docker-compose is to have a `.env` file in the same directory when running docker-compose.

The script provided to run docker-compose v1 assumes this file exists.

An [example](utils/docker-compose/.env-example) is provided as to what the `.env` file is supposed to look like.

## transmission settings
The default settings use the default transmission ports: **9091** for the web interface and **51413** for DHT.

If authentication is required to access the web interface, the default username is **transmission_user** and the default password is **transmission_pw**.

## Special thanks
- [Jonybat](https://github.com/Jonybat) for his [Flexget Config](https://github.com/Jonybat/flexget_config) and all the time he sank to tweak it.