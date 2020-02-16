#FROM anthonyguerreiro/flexget-python:2.11.21
#FROM anthonyguerreiro/flexget-python-armv7l:2.11.21
#FROM anthonyguerreiro/flexget-python3:2.11.21
FROM anthonyguerreiro/flexget-python3-armv7l:3.0.28

ARG TRANSMISSIONRPC_VERSION=0.11
ARG SUBLIMINAL_VERSION=2.0.5

ARG anime_yml=https://raw.githubusercontent.com/AnthonyGuerreiro/flexget-docker-config/master/utils/defaults/anime.yml
ARG secrets_yml=https://raw.githubusercontent.com/AnthonyGuerreiro/flexget-docker-config/master/utils/defaults/secrets.yml
ARG entrypoint=https://raw.githubusercontent.com/AnthonyGuerreiro/flexget-docker-config/master/utils/defaults/entrypoint.sh
ARG flexget_db=https://raw.githubusercontent.com/AnthonyGuerreiro/flexget-docker-config/master/utils/defaults/db-config.sqlite
ARG config_yml=https://raw.githubusercontent.com/AnthonyGuerreiro/flexget_config/master/config.yml

WORKDIR /.flexget

ADD ${anime_yml} anime.yml
ADD ${secrets_yml} secrets.yml
ADD ${entrypoint} /entrypoint
ADD ${flexget_db} db-config.sqlite
ADD ${config_yml} config.yml

ADD https://raw.githubusercontent.com/AnthonyGuerreiro/flexget_config/ag/plugins/log_filter.py plugins/log_filter.py
ADD https://raw.githubusercontent.com/AnthonyGuerreiro/flexget_config/ag/plugins/myanimelist.py plugins/myanimelist.py

RUN chmod +x /entrypoint && \
    pip install --no-cache-dir transmissionrpc==${TRANSMISSIONRPC_VERSION} \
                               subliminal==${SUBLIMINAL_VERSION}

ENTRYPOINT ["/entrypoint"]
