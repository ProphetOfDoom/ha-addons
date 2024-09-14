# set base image (host OS)
FROM python:3.11-rc-alpine
#FROM python:alpine3.15

RUN apk add mosquitto
RUN apk add --update npm
RUN apk add git
RUN apk add tzdata
RUN apk add musl-utils
RUN apk add xsel
RUN apk add redis

RUN apk add nginx && mkdir -p /run/nginx
RUN npm install -g serve

# set the working directory in the container
WORKDIR /app

# copy the dependencies file to the working directory
COPY requirements.txt .
RUN pip install -r requirements.txt

COPY givtcp-vuejs/package.json /app/ingress/package.json

RUN cd /app/ingress && npm install
COPY givtcp-vuejs ./ingress
RUN cd /app/ingress && npm run build && mv dist/index.html dist/config.html && cp -a dist/. /app/ingress/

COPY ingress.conf /etc/nginx/http.d/
COPY ingress_no_ssl.conf /app/ingress_no_ssl.conf
RUN rm /etc/nginx/http.d/default.conf

# copy the content of the local src directory to the working directory
COPY GivTCP/ ./GivTCP
COPY WebDashboard ./WebDashboard
# COPY givenergy_modbus/ /usr/local/lib/python3.11/site-packages/givenergy_modbus
COPY GivTCP/givenergy_modbus_async/ /usr/local/lib/python3.11/site-packages/givenergy_modbus_async

COPY api.json ./GivTCP/api.json
COPY startup_2.py startup.py
COPY redis.conf redis.conf
COPY settings.json ./settings.json
COPY ingress/ ./ingress

EXPOSE 1883 6379 8099

CMD ["python3", "/app/startup.py"]
