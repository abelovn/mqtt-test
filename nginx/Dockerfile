FROM nginx:stable-alpine
COPY nginx.conf /etc/nginx/nginx.conf
#COPY server.crt /etc/ssl/certs/server.crt
#COPY server.key /etc/ssl/private/server.key
#COPY index.html /usr/share/nginx/html/index.html
#COPY mosquitto-1.1.js /usr/share/nginx/html/
#COPY mqttws31.js /usr/share/nginx/html/
#COPY ws.html /usr/share/nginx/html/
EXPOSE 80
CMD [ "nginx", "-g", "daemon off;" ]
