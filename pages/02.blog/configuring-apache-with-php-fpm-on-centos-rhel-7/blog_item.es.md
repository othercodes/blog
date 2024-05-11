---
title: 'Configurando Apache con PHP-FPM en CentOS/RHEL 7'
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
        - centos
        - rhel7
        - fpm
image:
    summary:
        enabled: '1'
    text:
        enabled: '1'
media_order: 'featured.jpg,php-fpm.png'
date: '25-11-2019 12:37'
publish_date: '25-11-2019 12:37'
metadata:
    'og:url': 'https://othercode.io/es/blog/configurando-apache-con-php-fpm-en-centos-rhel-7'
    'og:type': article
    'og:title': 'Configurando Apache con PHP-FPM en CentOS/RHEL 7 | otherCode'
    'og:image': 'https://othercode.io/user/pages/02.blog/configuring-apache-with-php-fpm-on-centos-rhel-7/featured.jpg'
    'og:image:type': image/jpeg
    'og:image:width': 800
    'og:image:height': 350
    'og:author': otherCode
    'twitter:card': summary_large_image
    'twitter:title': 'Configurando Apache con PHP-FPM en CentOS/RHEL 7 | otherCode'
    'twitter:site': '@othercode'
    'twitter:creator': '@othercode'
    'twitter:image': 'https://othercode.io/user/pages/02.blog/configuring-apache-with-php-fpm-on-centos-rhel-7/featured.jpg'
    'article:published_time': '2019-11-25T12:37:00+10:00'
    'article:modified_time': '2024-05-07T16:20:13+10:00'
    'article:author': otherCode
    description: 'Instalación y configuración de PHP-FPM con Apache2 sobre CentOS/RHEL7.'
    keywords: 'php, fpm, fastcgi, apache, apache2, centos/rhel7, instalación y configuración'
slug: configurando-apache-con-php-fpm-en-centos-rhel-7
aura:
    metadata:
        description: 'Instalación y configuración de PHP-FPM con Apache2 sobre CentOS/RHEL7.'
        keywords: 'php, fpm, fastcgi, apache, apache2, centos/rhel7, instalación y configuración'
    pagetype: article
---

PHP-FPM, o PHP FastCGI Process Manager, es una implementación alternativa de PHP FastCGI que puede manejar un gran volumen de consultas. Combinado con PHP 7, PHP-FPM logra un nivel de rendimiento más alto que cualquier versión anterior de PHP.

===

Cuando se ejecuta PHP como módulo de Apache2 (httpd), cada proceso worker es una copia del espacio de memoria del proceso maestro. Esto implica que en cada worker duplica el espacio de memoria usado para ejecutar PHP aunque no sea necesario.

Al utilizar PHP-FPM, el intérprete PHP se ejecuta como un servicio aparte, el cual puede recibir peticiones a través de un socket TCP/IP o UNIX tradicional. De esta forma se tienen dos servicios: Apache2 (httpd) para manejar el protocolo HTTP y PHP-FPM para interpretar código PHP. Esto resulta más eficiente, ya que PHP es ejecutado sólo cuando es necesario.

Ahora que conocemos un poco mas de PHP FPM es momento de instalarlo y configurarlo.

Primero debemos instalar la versión de PHP que desemos, para ello podemos ver el articulo [Instalación de PHP 5.6, 7.0, 7.1 y 7.2 en CentOS/RHEL 7](https://othercode.io/instalacion-php-56-70-71-72-centos-rhel-7), en este ejemplo usaremos la versión PHP `rh-php72`. También necesitaremos el servidor Apache2 (httpd).

```bash
yum install httpd mod_ssl
```

Una vez que tenemos todo instalado empezaremos configurando el módulo mpm de httpd en el archivo /etc/httpd/conf.modules.d/00-mpm.conf:

```bash
LoadModule mpm_event_module modulese/mod_mpm_event.so
```

Si queremos realizar algún cambio en el módulo **PHP FPM** solo tenemos que editar el archivo `/etc/opt/rh/rh-php72/php-fpm.conf`.

Adicionalmente podemos realizar cambios en la configuración del pool de procesos en el archivo `/etc/opt/rh/rh-php72/php-fpm.d/www.conf`.

Una vez realizados los cambios debemos reiniciar el servicio PHP FPM par aplicar los cambios.

```bash
systemctl restart rh-php72-php-fpm.service
```

También debemos reiniciar el servicio httpd para aplicar los cambios en el modulo mpm:

```bash
systemctl restart httpd
```

Finalmente debemos añadir la siguiente linea a nuestros vhosts:

```bash
<FilesMatch \.php$>
SetHandler "proxy:fcgi://127.0.0.1:9000"
</FilesMatch>
```

Podemos confirmar que estamos ejecutando PHP con PHP FPM con un simple `phpinfo()`;

![php-fpm](php-fpm.png "php-fpm")