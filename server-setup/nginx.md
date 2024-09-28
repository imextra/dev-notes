# NGINX

The latest Nginx package is available in the default APT repositories on Ubuntu 24.04. Follow the steps below to update the server packages and install the Nginx web server application.

## Install Nginx

Update & upgrade Ubuntu Repo's

    sudo apt update && apt upgrade -y

Install Nginx

    sudo apt install nginx -y

View the installed Nginx version on your server.

    sudo nginx -version

## Manage the Nginx System Service

Enable the Nginx web server to start automatically at boot time.

    sudo systemctl enable nginx

Check the result:

    sudo systemctl is-enabled nginx

Если в ответ получили «enabled», значит nginx успешно добавлен в автозагрузку.

Start the Nginx service.

    sudo systemctl start nginx

Stop the Nginx service.

    sudo systemctl stop nginx

Restart the Nginx service.

    sudo systemctl restart nginx

View the Nginx service status and verify that it's running.

    sudo systemctl status nginx

## Nginx settings

Main configuration file is: 

    /etc/nginx/nginx.conf

Nginx virtual host's configuration is located in the `/etc/nginx/sites-available` directory. For example, `app.example.com` is located in `/etc/nginx/sites-available/app.example.com`

To activate host's configuration on the server you should create s sim-link in directory `/etc/nginx/sites-enabled` to your config.

`/etc/nginx/sites-enabled/app.example.com` ->> `/etc/nginx/sites-available/app.example.com`

### Nginx reverse proxy settings

Create directory `extra-include-conf` with includes:
- ssl.conf
- proxy.conf

`/etc/nginx/extra-include-conf/ssl.conf`:

```bash
ssl_certificate      /path-to-certificate/file.crt;
ssl_certificate_key  /path-to-certificate-key/file..key;
ssl_session_cache    shared:SSL:1m;
ssl_session_timeout  5m;
ssl_protocols        TLSV1.1 TLSV1.2 TLSV1.3;
ssl_ciphers          HIGH:!aNULL:!MD5;
ssl_prefer_server_ciphers  on;
```

`/etc/nginx/extra-include-conf/proxy.conf`:

```bash
proxy_http_version  1.1;
proxy_cache_bypass  $http_upgrade;
proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
proxy_set_header X-Real-IP $remote_addr;
proxy_set_header X-Forwarded-Host  $host;
proxy_set_header X-Forwarded-Proto $scheme;
proxy_set_header X-Forwarded-Port  $server_port;
```

Add new host's configurations:

`/etc/nginx/sites-available/site1.com`

```bash
server {
    server_name site1.com;
    listen       443 ssl;
    listen [::]:443 ssl;
    include /etc/nginx/extra-include-conf/ssl.conf;
    location / {
        proxy_pass https://internal-ip;
        include /etc/nginx/extra-include-conf/proxy.conf;
    }
}
```

Check configuration

    sudo nginx -t

Add sim-link to configuration:

    sudo ln -s /etc/nginx/sites-available/site1.com /etc/nginx/sites-enabled/site1.com

You can add as many configs as you want. If you wasnt to disable one site, just delete sim-link in `/etc/nginx/sites-enabled` directory.

Restart Nginx to apply the new virtual host configurations on your server.

    sudo systemctl restart nginx

View the Nginx service status and verify that it's running.

    sudo systemctl status nginx

#### Расшифровка настроек

[Источник](https://habr.com/ru/amp/publications/765536/)

Блок http { server {
    
`server_name` - Ожидаемое доменное имя по которому будет доступен сервер. Например site1.com

`listen` - Указывает на порт по которому будет доступен сервер. В случае с https/443 необходимо контролировать наличие подписи ssl в строке.

`ssl_certificate/ssl_certificate_key` - доменнные сертификаты

`ssl_session_cache` - Кеширование сессий. Необходимо для повторного использования ключей, чтобы не повторять хэндшейк.

`ssl_protocols` - Определение возможных TLS протоколов для работы с сервером.

Блок http { server { location / {

```
ВАЖНО
В случае если у вас есть фронт с url testn.domain.ru/front не получится использовать другие location кроме как корневой (/). Связано с передачей url от nginx на сервер во время proxy. istio будет отбивать такие запросы и rewrite не поможет
```

`proxy_pass` - Сервер на которое осуществляется проксирование домена, указанного в server_name блока server. 

`proxy_http_version` - Позволяет использовать HTTP/1.1 вместо дефолтного параметра HTTP/1.0

`proxy_cache_bypass` - В нашем случае не отдаются данные об изменении http запроса ($http_upgrade)

`proxy_set_header` - Записывает заголовки для передачи на сервер назначения proxy_pass

Подробнее можете почитать в [доках nginx](https://nginx.org/en/docs/).

Должен отметить что по незнанию к такому конфигу я шел достаточно долго. 3 рабочих дня если быть точным.
Я долго не мог заставить дружить istio и nginx и перелопатил кучу информации. И что в итоге? Самый важный параметр всего этого конфига - это `proxy_cache_bypass`.

В чем же суть этого `proxy_cache_bypass`?
`proxy_cache_bypass` — Задает условия, при которых nginx не станет брать ответ из кэша, а сразу перенаправит запрос на бэкенд. Если хотя бы один из параметров не пустой и не равен “0”. Подробнее почитайте вот [тут](https://habr.com/ru/articles/428127/)

В итоге у нас получается достаточно гибкая конфигурация прокси сервера которую легко администрировать. 


## More info

[Selectel: Установка и настройка nginx](https://selectel.ru/blog/install-nginx/)

[Настройка Nginx multiple reverse proxy для k3s+istio](https://habr.com/ru/amp/publications/765536/)

[Nginx cache: всё новое — хорошо забытое старое](https://habr.com/ru/articles/428127/)

[How to Install Nginx Web Server on Ubuntu 24.04](https://docs.vultr.com/how-to-install-nginx-web-server-on-ubuntu-24-04)

[Ubuntu tutorials: Install and configure Nginx](https://ubuntu.com/tutorials/install-and-configure-nginx#1-overview)

[Рег.ру: Как настроить виртуальные хосты Nginx на Ubuntu](https://help.reg.ru/support/servery-vps/oblachnyye-servery/ustanovka-programmnogo-obespecheniya/kak-nastroit-virt-khosty-nginx-v-ubuntu)
