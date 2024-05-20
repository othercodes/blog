---
title: Contacto
twitterenable: true
twittercardoptions: summary
articleenabled: false
facebookenable: true
form:
    name: contacto
    fields:
        name:
            label: Nombre
            placeholder: 'Introduce tu nombre'
            autocomplete: true
            type: text
            validate:
                required: true
        email:
            label: Email
            placeholder: 'Introduce tu email'
            type: email
            validate:
                required: true
        message:
            label: Mensaje
            placeholder: 'Introduce tu mensaje'
            type: textarea
            validate:
                required: true
        g-recaptcha-response:
            label: Captcha
            type: captcha
            recaptcha_not_validated: 'Captcha invalido!'
    buttons:
        submit:
            type: submit
            value: Enviar
        reset:
            type: reset
            value: Reiniciar
    process:
        captcha: true
        save:
            fileprefix: contact-
            dateformat: Ymd-His-u
            extension: txt
            body: '{% include ''forms/data.txt.twig'' %}'
        email:
            subject: '[Formulario de Contacto] {{ form.value.name|e }}'
            body: '{% include ''forms/data.html.twig'' %}'
        display: thankyou
        message: 'Gracias por ponerse en contacto!'
routes: {  }
slug: contacto
aura:
    pagetype: website
metadata:
    'og:url': 'https://othercode.io/es/contact'
    'og:type': website
    'og:title': 'Contacto | otherCode'
    'og:author': otherCode
    'twitter:card': summary_large_image
    'twitter:title': 'Contacto | otherCode'
    'twitter:site': '@othercode'
    'twitter:creator': '@othercode'
    'article:published_time': '2024-05-08T17:41:25+10:00'
    'article:modified_time': '2024-05-08T17:41:25+10:00'
    'article:author': otherCode
---

