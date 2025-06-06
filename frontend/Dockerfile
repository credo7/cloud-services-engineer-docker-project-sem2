FROM node:20-alpine AS builder

RUN apk update && apk upgrade && apk add --no-cache make python3 g++

WORKDIR /app

COPY package*.json ./

RUN npm install

COPY . .

ENV NODE_ENV=production
ENV VUE_APP_API_URL=/api
ENV NODE_OPTIONS=--openssl-legacy-provider

RUN npm run build

FROM nginx:1.27-alpine

RUN apk update && apk upgrade && apk add --no-cache curl

RUN addgroup -g 1001 -S appgroup && adduser -u 1001 -S appuser -G appgroup

COPY --chown=appuser:appgroup nginx.conf /etc/nginx/conf.d/default.conf
COPY --from=builder --chown=appuser:appgroup /app/dist /usr/share/nginx/html

RUN touch /var/run/nginx.pid && chown -R appuser:appgroup /var/cache/nginx /var/log/nginx /etc/nginx/conf.d /var/run/nginx.pid

EXPOSE 80

USER appuser

HEALTHCHECK --interval=30s --timeout=3s --start-period=5s --retries=3 CMD curl -f http://localhost || exit 1

CMD ["nginx", "-g", "daemon off;"]