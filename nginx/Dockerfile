FROM nginx:stable-alpine
#COPY nginx.conf /etc/nginx/conf.d/default.conf
COPY index.html /usr/share/nginx/html/index.html
COPY mosquitto-1.1.js /usr/share/nginx/html/
COPY mqttws31.js /usr/share/nginx/html/
COPY ws.html /usr/share/nginx/html/
EXPOSE 80
CMD [ "nginx", "-g", "daemon off;" ]
