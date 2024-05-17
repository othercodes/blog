---
title: 'Configurando Apache con PHP-FPM en Ubuntu'
twitterenable: true
twittercardoptions: summary
articleenabled: false
facebookenable: true
taxonomy:
    category:
        - blog
    tag:
        - infraestructura
        - php
        - fpm
        - ubuntu
image:
    summary:
        enabled: '1'
    text:
        enabled: '1'
metadata:
    description: 'Instalación y configuración de PHP-FPM con Apache2 sobre Ubuntu.'
    'og:url': 'https://othercode.io/es/blog/configurando-apache-con-php-fpm-en-ubuntu'
    'og:type': article
    'og:title': 'Configurando Apache con PHP-FPM en Ubuntu | otherCode'
    'og:description': 'Instalación y configuración de PHP-FPM con Apache2 sobre Ubuntu.'
    'og:image': 'https://othercode.io/user/pages/02.blog/configuring-apache-with-php-fpm-on-ubuntu/featured.jpg'
    'og:image:type': image/jpeg
    'og:image:width': 800
    'og:image:height': 350
    'og:author': otherCode
    'twitter:card': summary_large_image
    'twitter:title': 'Configurando Apache con PHP-FPM en Ubuntu | otherCode'
    'twitter:description': 'Instalación y configuración de PHP-FPM con Apache2 sobre Ubuntu.'
    'twitter:site': '@othercode'
    'twitter:creator': '@othercode'
    'twitter:image': 'https://othercode.io/user/pages/02.blog/configuring-apache-with-php-fpm-on-ubuntu/featured.jpg'
    'article:published_time': '2019-12-14T10:00:00+10:00'
    'article:modified_time': '2024-05-17T20:04:10+10:00'
    'article:author': otherCode
    keywords: 'php, fpm, fastcgi, apache, apache2, ubuntu, instalación y configuración'
media_order: featured.jpg
date: '14-12-2019 10:00'
publish_date: '14-12-2019 10:00'
slug: configurando-apache-con-php-fpm-en-ubuntu
aura:
    metadata:
        keywords: 'php, fpm, fastcgi, apache, apache2, ubuntu, instalación y configuración'
    pagetype: article
    description: 'Instalación y configuración de PHP-FPM con Apache2 sobre Ubuntu.'
    image: featured.jpg
---

Como ya comente en el articulo [Configurando Apache con PHP-FPM en CentOS/RHEL 7](/blog/configurando-apache-con-php-fpm-en-centos-rhel-7), PHP-FPM es una manera mucho mas eficiente de manejar grandes volúmenes de consultas. En este articulo veremos como configurarlo en Ubuntu.

===

En este articulo asumiremos que ya tenemos instalado `apache2` en nuestro sistema. Primero instalaremos el paquete `php7.2-fpm`:

```bash
sudo apt install php7.2 php7.2-{fpm,common}
```

Ahora debemos instalar el modulo FastCGI de `apache2`, para ellos ejecutaremos los siguientes ordenes:

```bash
wget http://mirrors.kernel.org/ubuntu/pool/multiverse/liba/libapache-mod-fastcgi/libapache2-mod-fastcgi_2.4.7\~0910052141-1.2_amd64.deb
sudo dpkg -i libapache2-mod-fastcgi_2.4.7~0910052141-1.2_amd64.deb
```

Una vez instalado debemos activar varios módulos de `apache2` necesarios:

```bash
sudo a2enmod actions fastcgi alias proxy_fcgi
```

En caso de que el modulo php de `apache2` este activado deberemos desactivarlo, para que `fpm` funcione correctamente.

```bash
sudo a2dismod php7.2
```

Es momento de editar el archivo `/etc/apache2/sites-available/000-default.conf` para añadir la configuración del manejador FPM:

```bash
sudo vim /etc/apache2/sites-available/000-default.conf
```

Añadiremos las siguientes lineas:

```bash
<FilesMatch \.php$>
     SetHandler "proxy:unix:/var/run/php/php7.2-fpm.sock|fcgi://localhost/"
</FilesMatch>
```

Alternativamente podemos activar directamente la configuración de `php-fpm` con el comando:

```bash
sudo a2enconf php7.2-fpm
```

Finalmente reiniciaremos el servidor `apache2` y el servicio `fpm`:

```bash
sudo systemctl restart {apache2,php7.2-fpm}.service
```

Adicionalmente podemos realizar cambios en la configuración del pool de procesos en el archivo `/etc/php/7.2/fpm/php-fpm.conf`.