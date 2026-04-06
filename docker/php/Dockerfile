FROM node:22-bookworm-slim@sha256:80fdb3f57c815e1b638d221f30a826823467c4a56c8f6a8d7aa091cd9b1675ea AS node

FROM php:8.4-cli-bookworm@sha256:15bd32b1c20ec9bb8edd09849745b5345105001b9780c873af7adae4e5d0cf54

ARG WWWUSER=1000
ARG WWWGROUP=1000

COPY --from=composer:2 /usr/bin/composer /usr/bin/composer
COPY --from=node /usr/local/bin/node /usr/local/bin/node
COPY --from=node /usr/local/lib/node_modules /usr/local/lib/node_modules

RUN apt-get update \
    && apt-get install -y --no-install-recommends \
        git \
        libicu-dev \
        libzip-dev \
        unzip \
        zip \
    && docker-php-ext-install -j"$(nproc)" \
        intl \
        pcntl \
        zip \
    && ln -sf /usr/local/lib/node_modules/npm/bin/npm-cli.js /usr/local/bin/npm \
    && ln -sf /usr/local/lib/node_modules/npm/bin/npx-cli.js /usr/local/bin/npx \
    && ln -sf /usr/local/lib/node_modules/corepack/dist/corepack.js /usr/local/bin/corepack \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/*

RUN groupadd -g "${WWWGROUP}" workbench \
    && useradd --no-log-init -u "${WWWUSER}" -g "${WWWGROUP}" -m workbench

WORKDIR /var/www/html

COPY --chmod=755 entrypoint.sh /usr/local/bin/plugin-workbench-entrypoint

ENTRYPOINT ["plugin-workbench-entrypoint"]
