FROM node:12.22-alpine
WORKDIR /home/src/electricitymap/contrib/web

# Install dependencies
RUN apk add jq unzip curl
ADD web/package.json ./package.json
ADD web/yarn.lock ./yarn.lock
RUN yarn install --frozen-lockfile

# Generate map
RUN mkdir -p public/dist/
ADD web/third_party_maps ./third_party_maps
ADD web/generate-geometries.js web/topogen.sh ./
ADD web/src/world.json ./src/world.json
RUN time /bin/sh topogen.sh

ARG ELECTRICITYMAP_PUBLIC_TOKEN

# Build
# (note: this will override the world.json that was previously created)
ADD config /home/src/electricitymap/contrib/config
ADD web ./
RUN time yarn lint
RUN time yarn build-release

EXPOSE 8000
ENV PORT 8000
CMD node server.js

HEALTHCHECK CMD curl --fail http://localhost:${PORT}/ || exit 1
