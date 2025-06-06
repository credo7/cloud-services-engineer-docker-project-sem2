FROM node:24-alpine AS build

ARG NODE_OPTIONS=--openssl-legacy-provider
ARG VUE_APP_API_URL=/api

WORKDIR /app

COPY package*.json ./

RUN npm ci

COPY . .

RUN npm run build

FROM nginxinc/nginx-unprivileged:1.27-alpine

USER nginx

COPY --from=build /app/dist /usr/share/nginx/html/momo-store

HEALTHCHECK CMD curl --fail http://localhost:80/ || exit 1

EXPOSE 80

CMD ["nginx", "-g", "daemon off;"]
