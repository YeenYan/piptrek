# ---- Stage 1: Build ----
FROM alpine:3.21 AS build

RUN apk upgrade --no-cache \
    && apk add --no-cache nodejs npm

WORKDIR /app
COPY package.json package-lock.json* ./
RUN npm ci
COPY . .
RUN npm run build

# ---- Stage 2: Production ----
FROM alpine:3.21

RUN apk upgrade --no-cache \
    && apk add --no-cache nginx \
    && mkdir -p /run/nginx /usr/share/nginx/html

COPY nginx.conf /etc/nginx/http.d/default.conf
COPY --from=build /app/dist /usr/share/nginx/html

EXPOSE 80

CMD ["nginx", "-g", "daemon off;"]
