FROM node:16-bullseye as build-deps

ENV DOCKERIZE_VERSION v0.6.1

RUN apt-get update && apt-get install -y wget \
    && wget https://github.com/jwilder/dockerize/releases/download/$DOCKERIZE_VERSION/dockerize-linux-amd64-$DOCKERIZE_VERSION.tar.gz \
    && tar -C /usr/local/bin -xzvf dockerize-linux-amd64-$DOCKERIZE_VERSION.tar.gz \
    && rm dockerize-linux-amd64-$DOCKERIZE_VERSION.tar.gz

WORKDIR /usr/src/app
COPY package*.json ./
RUN npm install
COPY . .
RUN npm run build

FROM node:16-bullseye
WORKDIR /usr/src/app
COPY --from=build-deps /usr/local/bin/dockerize /usr/local/bin/dockerize
COPY --from=build-deps /usr/src/app/node_modules ./node_modules
COPY --from=build-deps /usr/src/app/dist ./dist
COPY --from=build-deps /usr/src/app/package.json ./
COPY --from=build-deps /usr/src/app/public ./public
COPY --from=build-deps /usr/src/app/tsconfig.json ./

EXPOSE 3000
CMD ["node", "dist/server.js"]
