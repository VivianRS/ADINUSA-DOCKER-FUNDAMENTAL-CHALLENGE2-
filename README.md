# Laravel Perpustakaan - Docker Setup

Panduan ini akan membantu Anda men-setup project Laravel Perpustakaan menggunakan Docker.

## 1. Persiapan Project
```bash
mkdir challenge2 && cd challenge2
git clone https://github.com/academynusa/perpus-laravel.git
```

## 2. Membuat Dockerfile dan Start Script
Buat file `Dockerfile` dan `start.sh` sesuai isi di bawah ini.

### Dockerfile
```dockerfile
FROM php:7.2-apache


RUN sed -i 's|http://deb.debian.org/debian|http://archive.debian.org/debian|g' /etc/apt/sources.list &&     sed -i 's|http://security.debian.org/debian-security|http://archive.debian.org/debian-security|g' /etc/apt/sources.list &&     echo 'Acquire::Check-Valid-Until "false";' > /etc/apt/apt.conf.d/99no-check-valid-until

RUN sed -i '/<Directory \/var\/www\/>/,/<\/Directory>/ s/AllowOverride None/AllowOverride All/' /etc/apache2/apache2.conf


RUN apt-get update && apt-get install -y \
    git unzip curl libzip-dev libpng-dev libonig-dev libxml2-dev zip \
    && docker-php-ext-install pdo pdo_mysql zip


RUN a2enmod rewrite


COPY --from=composer:latest /usr/bin/composer /usr/bin/composer


WORKDIR /var/www/html


COPY perpus-laravel/ .


RUN sed -i 's|DocumentRoot /var/www/html|DocumentRoot /var/www/html/public|' /etc/apache2/sites-available/000-default.conf
RUN sed -i '/<Directory \/var\/www\/>/,/<\/Directory>/ s/AllowOverride None/AllowOverride All/' /etc/apache2/apache2.conf


COPY start.sh /start.sh
RUN chmod +x /start.sh


CMD ["/start.sh"]
```

### start.sh
```bash
#!/bin/bash


sleep 10


chown -R www-data:www-data /var/www/html
chmod -R 775 /var/www/html/storage /var/www/html/bootstrap/cache


cp .env.example .env
composer update
php artisan key:generate


sed -i 's/DB_DATABASE=.*/DB_DATABASE=perpusku_gc/' .env
sed -i 's/DB_USERNAME=.*/DB_USERNAME=root/' .env
sed -i 's/DB_PASSWORD=.*/DB_PASSWORD=/' .env
sed -i 's/127.0.0.1/mysql/' .env


php artisan migrate --force
php artisan db:seed --force


apache2-foreground
```
## 3. login docker
```
docker login
```
## 4. Build & Jalankan Container
```bash
docker build -t img-perpus-username .
```
```
docker run -d -p 8000:80 --name perpus-username img-perpus-uername
```

## 5. Testing
Buka browser dan akses:
```
http://localhost:8000
```
## IP NODE2
```
http://10.10.10.12:8000

