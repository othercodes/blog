---
title: 'Configuring Apache with PHP-FPM on Ubuntu'
twitterenable: true
twittercardoptions: summary
articleenabled: false
facebookenable: true
taxonomy:
    category:
        - blog
    tag:
        - infrastructure
        - php
        - fpm
        - ubuntu
image:
    summary:
        enabled: '1'
    text:
        enabled: '1'
metadata:
    description: 'Installation and configuration of PHP-FPM with Apache2 on Ubuntu.'
    'og:url': 'https://othercode.io/blog/configuring-apache-with-php-fpm-on-ubuntu'
    'og:type': article
    'og:title': 'Configuring Apache with PHP-FPM on Ubuntu | otherCode'
    'og:description': 'Installation and configuration of PHP-FPM with Apache2 on Ubuntu.'
    'og:image': 'https://othercode.io/user/pages/02.blog/configuring-apache-with-php-fpm-on-ubuntu/featured.webp'
    'og:image:type': image/webp
    'og:image:width': 800
    'og:image:height': 350
    'og:author': otherCode
    'twitter:card': summary_large_image
    'twitter:title': 'Configuring Apache with PHP-FPM on Ubuntu | otherCode'
    'twitter:description': 'Installation and configuration of PHP-FPM with Apache2 on Ubuntu.'
    'twitter:site': '@othercode'
    'twitter:creator': '@othercode'
    'twitter:image': 'https://othercode.io/user/pages/02.blog/configuring-apache-with-php-fpm-on-ubuntu/featured.webp'
    'article:published_time': '2019-12-14T10:00:00+10:00'
    'article:modified_time': '2024-05-17T20:04:43+10:00'
    'article:author': otherCode
    keywords: 'php, fpm, fastcgi, apache, apache2, ubuntu, installation and configuration'
date: '14-12-2019 10:00'
publish_date: '14-12-2019 10:00'
aura:
    metadata:
        keywords: 'php, fpm, fastcgi, apache, apache2, ubuntu, installation and configuration'
    pagetype: article
    description: 'Installation and configuration of PHP-FPM with Apache2 on Ubuntu.'
    image: featured.webp
media_order: featured.webp
---

As I already mentioned in the article [Configuring Apache with PHP-FPM in CentOS/RHEL 7](/blog/configuring-apache-with-php-fpm-on-centos-rhel-7), PHP-FPM is a much more efficient to handle large volumes of queries. In this article we will see how to configure it in Ubuntu.

===

In this article we will assume that we already have `apache2` installed on our system. First we will install the `php7.2-fpm` package:

```bash
sudo apt install php7.2 php7.2-{fpm,common}
```

Now we must install the FastCGI module of `apache2`, for them we will execute the following commands:

```bash
wget http://mirrors.kernel.org/ubuntu/pool/multiverse/liba/libapache-mod-fastcgi/libapache2-mod-fastcgi_2.4.7\~0910052141-1.2_amd64.deb
sudo dpkg -i libapache2-mod-fastcgi_2.4.7~0910052141-1.2_amd64.deb
```

Once installed we must activate several necessary `apache2` modules:

```bash
sudo a2enmod actions fastcgi alias proxy_fcgi
```

If the `apache2` php module is activated, we must deactivate it so that `fpm` works correctly.

```bash
sudo a2dismod php7.2
```

It's time to edit the file `/etc/apache2/sites-available/000-default.conf` to add the FPM handler configuration:

```bash
sudo vim /etc/apache2/sites-available/000-default.conf
```

We will add the following lines:

```bash
<FilesMatch \.php$>
     SetHandler "proxy:unix:/var/run/php/php7.2-fpm.sock|fcgi://localhost/"
</FilesMatch>
```

Alternatively we can directly activate the `php-fpm` configuration with the command:

```bash
sudo a2enconf php7.2-fpm
```

Finally we restart the `apache2` server and the `fpm` service:

```bash
sudo systemctl restart {apache2,php7.2-fpm}.service
```
Additionally, we can make changes to the process pool configuration in the file `/etc/php/7.2/fpm/php-fpm.conf`.
