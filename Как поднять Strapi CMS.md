#### Источники:
- https://docs.strapi.io/dev-docs/deployment/digitalocean
- https://docs.digitalocean.com/developer-center/deploying-a-next.js-application-on-a-digitalocean-droplet/
- https://www.digitalocean.com/community/tutorials/how-to-install-and-use-postgresql-on-ubuntu-22-04

## Процесс установки

1. Установка [nvm](https://github.com/nvm-sh/nvm) и nodejs
```bash
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.7/install.sh | bash

export NVM_DIR="$([ -z "${XDG_CONFIG_HOME-}" ] && printf %s "${HOME}/.nvm" || printf %s "${XDG_CONFIG_HOME}/nvm")"
[ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh" # This loads nvm
```

```bash
nvm install --lts
nvm use --lts
```

2. Установка nginx, npm, yarn
```bash
sudo apt update && sudo apt upgrade -y
sudo apt install -y nodejs npm nginx
```

```bash
npm install --global yarn
```

3. Установка и настройка базы данных PostgreSQL
Сама установка происходит по [этому](https://www.digitalocean.com/community/tutorials/how-to-install-and-use-postgresql-on-ubuntu-22-04) руководству.

Установить postgresql и postgresql-contrib, который добавляет функциональности и утилит.

```bash
sudo apt install postgresql postgresql-contrib
```

Проверить, что сервис postgresql запустился

```bash
sudo systemctl start postgresql.service
sudo systemctl status postgresql.service
```

Переключаемся на на аккаунт postgres

```bash
sudo -i -u postgres
```

Теперь можно войти в PostgreSQL командой `psql` и выйти, прописав `\q`

Создадим нового пользователя, прописав:

```bash
createuser --interactive
```

```bash
==Output==
Enter name of role to add: fractal
Shall the new role be a superuser? (y/n) y
```

Создаем таблицу

```bash
createdb sammy
```

Заходим в postgres (`psql`), и меняем пароль пользователя.

```bash
postgres=# ALTER USER fractal PASSWORD 'examplepass';
```

4. Создаем Strapi проект
```bash
yarn create strapi-app strapi
```

![[Screenshot 2024-01-19 130345.png]]

5. Настраиваем nginx, проксируем порт 1337
```nginx
server {
  listen 80;
  server_name YOUR_IP_ADDRESS;
  location / {
    proxy_pass http://localhost:1337;
    proxy_http_version 1.1;
    proxy_set_header Upgrade $http_upgrade;
    proxy_set_header Connection 'upgrade';
    proxy_set_header Host $host;
    proxy_cache_bypass $http_upgrade;
  }
}
```

6. Запускаем дев версию через pm2

```bash
sudo npm install -g pm2
```

```
pm2 start yarn --name "strapi-dev" -- develop
```

7. Устанавливаем ssl через [certbot](https://certbot.eff.org/instructions?ws=nginx&os=ubuntufocal)
```
sudo snap install --classic certbot
sudo ln -s /snap/bin/certbot /usr/bin/certbot
sudo certbot --nginx
```
