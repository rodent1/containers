FROM node:18 AS build

ARG TARGETPLATFORM
ARG VERSION
ARG CHANNEL

USER root
WORKDIR /opt/node_app

#hadolint ignore=DL3008
RUN \
    curl -fsSL "https://github.com/excalidraw/excalidraw/archive/refs/tags/v${VERSION}.tar.gz" \
    | tar xzf - -C /opt/node_app --strip-components 1

COPY . .
RUN yarn --ignore-optional --network-timeout 600000

ARG NODE_ENV=production

RUN yarn build:app:docker

RUN chown -R 101:101 /opt/node_app/build && chmod -R 755 /opt/node_app/build

FROM nginxinc/nginx-unprivileged:1.21-alpine

COPY --from=build /opt/node_app/build /usr/share/nginx/html

USER nginx

HEALTHCHECK CMD wget -q -O /dev/null http://localhost || exit 1

LABEL org.opencontainers.image.source="https://github.com/excalidraw/excalidraw"
