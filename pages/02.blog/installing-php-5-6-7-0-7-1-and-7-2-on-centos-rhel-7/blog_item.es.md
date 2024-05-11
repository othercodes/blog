---
title: 'Instalación de PHP 5.6, 7.0, 7.1 y 7.2 en CentOS/RHEL 7'
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
media_order: featured.jpeg
image:
    summary:
        enabled: '1'
    text:
        enabled: '1'
metadata:
    description: 'Instalación de PHP 5.6, 7.0, 7.1 y 7.2 en CentOS/RHEL 7 usando repositorios Remi, EPEL y SCL. '
    'og:url': 'https://othercode.io/es/blog/instalacion-php-56-70-71-72-centos-rhel-7'
    'og:type': article
    'og:title': 'Instalación de PHP 5.6, 7.0, 7.1 y 7.2 en CentOS/RHEL 7 | otherCode'
    'og:description': 'Instalación de PHP 5.6, 7.0, 7.1 y 7.2 en CentOS/RHEL 7 usando repositorios Remi, EPEL y SCL. '
    'og:image': 'https://othercode.io/user/pages/02.blog/installing-php-5-6-7-0-7-1-and-7-2-on-centos-rhel-7/featured.jpeg'
    'og:image:type': image/jpeg
    'og:image:width': 800
    'og:image:height': 350
    'og:author': otherCode
    'twitter:card': summary_large_image
    'twitter:title': 'Instalación de PHP 5.6, 7.0, 7.1 y 7.2 en CentOS/RHEL 7 | otherCode'
    'twitter:description': 'Instalación de PHP 5.6, 7.0, 7.1 y 7.2 en CentOS/RHEL 7 usando repositorios Remi, EPEL y SCL. '
    'twitter:site': '@othercode'
    'twitter:creator': '@othercode'
    'twitter:image': 'https://othercode.io/user/pages/02.blog/installing-php-5-6-7-0-7-1-and-7-2-on-centos-rhel-7/featured.jpeg'
    'article:published_time': '2018-12-08T00:00:00+10:00'
    'article:modified_time': '2024-05-11T11:44:32+10:00'
    'article:author': otherCode
    keywords: 'Instalacion, CentOS7, RHEL7, PHP5.6, PHP7.0, PHP7.1, PHP7, 2, EPEL, Remi, SCL'
date: '08-12-2018 00:00'
publish_date: '08-12-2018 00:00'
slug: instalacion-php-56-70-71-72-centos-rhel-7
aura:
    metadata:
        keywords: 'Instalacion, CentOS7, RHEL7, PHP5.6, PHP7.0, PHP7.1, PHP7, 2, EPEL, Remi, SCL'
    pagetype: article
    image: featured.jpeg
    description: 'Instalación de PHP 5.6, 7.0, 7.1 y 7.2 en CentOS/RHEL 7 usando repositorios Remi, EPEL y SCL. '
---

La instalación de las últimas versiones de PHP en CentOS/RHEL puede no ser tan fácil como parece ya que hay que realizar ciertos pasos previos. Además, cada una de estas versiones tiene un directorio de instalación algo peculiar. En este artículo cubriremos todos los pasos para las instalación y uso de las versiones de PHP 5.6, 7.0, 7.1 y 7.2.

===

Antes de empezar debemos saber que CentOS y RHEL 7 sirven por defecto PHP 5.4 como versión LTS de PHP. Para instalar las diferentes versiones de PHP en nuestro sistema debemos habilitar ciertos repositorios extra.

### Remi

Es un repositorio de software de terceros creado y mantenido por [Remi](https://blog.remirepo.net/), un chico Frances. En este repositorio podemos encontrar las últimas versiones de PHP. El repositorio Remi usa, a su vez, el repositorio [EPEL](https://fedoraproject.org/wiki/EPEL/es) más orientado a empresa. Para habilitar estos repositorios solo debemos ejecutar estas lineas:

```bash
yum install https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm
yum install http://rpms.remirepo.net/enterprise/remi-release-7.rpm
```

En este repositorio encontraremos las versiones 7.0, 7.1, 7.2 y 7.3 de PHP.

> Desde diciembre de 2018 la versión 5.6 ha sido oficialmente deprecada.

### SCL (Software Collections)

Es un repositorio diseñado para mantener la compatibilidad total durante la vida útil de la distribución del software, para habilitar el uso de [SCL](https://wiki.centos.org/AdditionalResources/Repositories/SCL) solo debemos usar las siguientes lineas:

```bash
yum install centos-release-scl-rh
```

En este repositorio tenemos disponibles las versiones 7.0, 7.1 y 7.2 de PHP.

> Desde diciembre de 2018 la versión 5.6 ha sido oficialmente deprecada.

Una vez habilitados estos repositorios podremos instalar las versiones de PHP que deseemos.

#### Instalación PHP v7.3 (Remi)

```bash
yum install php73 php73-php php73-php-common php73-php-fpm php73-php-mysql php73-php-pecl-memcache php73-php-pecl-memcached php73-php-gd php73-php-mbstring php73-php-mcrypt php73-php-xml php73-php-pecl-apc php73-php-cli php73-php-pear php73-php-pdo
```

* **Instalación**: `/opt/remi/php73/`
* **Binario**: `/opt/remi/php73/root/bin/php`

#### Instalación PHP v7.2 (Remi)

```bash
yum install php72 php72-php php72-php-common php72-php-fpm php72-php-mysql php72-php-pecl-memcache php72-php-pecl-memcached php72-php-gd php72-php-mbstring php72-php-mcrypt php72-php-xml php72-php-pecl-apc php72-php-cli php72-php-pear php72-php-pdo
```

* **Instalación**: `/opt/remi/php72/`
* **Binario**: `/opt/remi/php72/root/bin/php`

#### Instalación PHP v7.1 (Remi)

```bash
yum install php71 php71-php php71-php-common php71-php-fpm php71-php-mysql php71-php-pecl-memcache php71-php-pecl-memcached php71-php-gd php71-php-mbstring php71-php-mcrypt php71-php-xml php71-php-pecl-apc php71-php-cli php71-php-pear php71-php-pdo
```

* **Instalación**: `/opt/remi/php71/`
* **Binario**: `/opt/remi/php71/root/bin/php`

#### Instalación PHP v7.0 (Remi)

```bash
yum install php70 php70-php php70-php-common php70-php-fpm php70-php-mysql php70-php-pecl-memcache php70-php-pecl-memcached php70-php-gd php70-php-mbstring php70-php-mcrypt php70-php-xml php70-php-pecl-apc php70-php-cli php70-php-pear php70-php-pdo
```

* **Instalación**: `/opt/remi/php70/`
* **Binario**: `/opt/remi/php70/root/bin/php`

#### Instalación PHP v5.6 (Remi) EOL

```bash
yum install php56 php56-php php56-php-common php56-php-fpm php56-php-mysql php56-php-pecl-memcache php56-php-pecl-memcached php56-php-gd php56-php-mbstring php56-php-mcrypt php56-php-xml php56-php-pecl-apc php56-php-cli php56-php-pear php56-php-pdo
```

* **Instalación**: `/opt/remi/php56/`
* **Binario**: `/opt/remi/php56/root/bin/php`

#### Instalación PHP v7.2 (RH)

```bash
yum install rh-php72 rh-php72-php rh-php72-php-pdo rh-php72-php-soap rh-php72-php-xmlrpc rh-php72-php-curl rh-php72-php-mbstring rh-php72-php-readline rh-php72-php-intl rh-php72-php-process rh-php72-php-fpm
```

* **Instalación**: `/opt/rh/rh-php72`
* **Binario**: `/opt/rh/rh-php72/root/bin/php`

#### Instalación PHP v7.1 (RH)

```bash
yum install rh-php71 rh-php71-php rh-php71-php-pdo rh-php71-php-soap rh-php71-php-xmlrpc rh-php71-php-curl rh-php71-php-mbstring rh-php71-php-readline rh-php71-php-intl rh-php71-php-process rh-php71-php-fpm
```

* **Instalación**: `/opt/rh/rh-php71`
* **Binario**: ``/opt/rh/rh-php71/root/bin/php`

#### Instalación PHP v7.0 (RH)

```bash
yum install rh-php70 rh-php70-php rh-php70-php-pdo rh-php70-php-soap rh-php70-php-xmlrpc rh-php70-php-curl rh-php70-php-mbstring rh-php70-php-readline rh-php70-php-intl rh-php70-php-process rh-php70-php-fpm
```

* **Instalación**: `/opt/rh/rh-php70`
* **Binario**: `/opt/rh/rh-php70/root/bin/php`

#### Instalación PHP v5.6 (RH) EOL

```bash
yum install rh-php56 rh-php56-php rh-php56-php-pdo rh-php56-php-mysql rh-php56-php-soap rh-php56-php-xmlrpc rh-php56-php-curl rh-php56-php-mbstring rh-php56-php-readline rh-php56-php-intl rh-php56-php-process rh-php56-php-xdebug rh-php56-php-fpm
```

* **Instalación**: `/opt/rh/rh-php56`
* **Binario**: `/opt/rh/rh-php56/root/bin/php`