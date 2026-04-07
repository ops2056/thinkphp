FROM debian:11.11-slim

RUN apt-get update && apt-get install -y --no-install-recommends \
    curl ca-certificates gnupg2 lsb-release \
    && curl -sS https://packages.sury.org/php/apt.gpg | gpg --dearmor > /usr/share/keyrings/php.gpg \
    && echo "deb [signed-by=/usr/share/keyrings/php.gpg] https://packages.sury.org/php/ $(lsb_release -cs) main" > /etc/apt/sources.list.d/php.list \
    && curl https://unit.nginx.org/keys/nginx-keyring.gpg > /usr/share/keyrings/nginx.gpg \
    && echo "deb [signed-by=/usr/share/keyrings/nginx.gpg] https://packages.nginx.org/unit/debian/ $(lsb_release -cs) unit" > /etc/apt/sources.list.d/unit.list \
    && apt-get update && apt-get install -y --no-install-recommends \
    procps unit unit-php libphp7.4-embed php7.4-cli php7.4-common php7.4-pdo php7.4-redis \
    php7.4-gd php7.4-mbstring php7.4-xml php7.4-curl php7.4-zip php7.4-bcmath \
    && rm -rf /var/lib/apt/lists/* && apt-get clean && apt-get autoremove -y


RUN mkdir -p /thinkphp /var/run/unit /var/lib/unit && \
    groupadd -r www-data 2>/dev/null || true && \
    useradd -r -g www-data www-data 2>/dev/null || true

COPY src/ /thinkphp/
COPY /docker/unit.json /var/lib/unit/conf.json

RUN chown -R www-data:www-data /thinkphp /var/lib/unit && chmod -R 755 /thinkphp && chmod -R 755 /var/lib/unit

WORKDIR /thinkphp
EXPOSE 80

CMD ["/bin/sh", "-c", "unitd --no-daemon --control unix:/var/run/control.unit.sock & sleep 2 && curl -X PUT --data-binary @/var/lib/unit/conf.json --unix-socket /var/run/control.unit.sock http://localhost/config && wait"]
