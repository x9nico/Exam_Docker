FROM nginx:alpine

RUN addgroup -S myuser && adduser -S myuser -G myuser

RUN sed -i 's/listen       80;/listen 8080;/g' /etc/nginx/conf.d/default.conf

RUN chown -R myuser:myuser /var/cache/nginx /var/run /var/log/nginx /etc/nginx /usr/share/nginx/html

USER myuser

EXPOSE 8080

CMD ["nginx", "-g", "daemon off;"]
