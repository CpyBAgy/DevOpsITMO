FROM nginx:alpine

RUN addgroup -S nginx-proxy && adduser -S nginx-proxy -G nginx-proxy

RUN rm /etc/nginx/conf.d/default.conf

COPY nginx.conf /etc/nginx/nginx.conf

RUN mkdir -p /var/cache/nginx /var/log/nginx && \
    chown -R nginx-proxy:nginx-proxy /var/cache/nginx /var/log/nginx /etc/nginx && \
    touch /var/run/nginx.pid && \
    chown nginx-proxy:nginx-proxy /var/run/nginx.pid && \
    rm -rf /var/cache/apk/*

USER nginx-proxy

EXPOSE 3000

CMD ["nginx", "-g", "daemon off;"]