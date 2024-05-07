---
title: 'Análisis Estático de Código PHP con Phan'
twitterenable: true
twittercardoptions: summary
articleenabled: false
facebookenable: true
taxonomy:
    category:
        - blog
    tag:
        - php
        - 'analisis estatico'
        - phan
        - php-ast
media_order: featured.jpeg
image:
    summary:
        enabled: '1'
    text:
        enabled: '1'
date: '01-01-2019 12:29'
publish_date: '07-01-2019 12:29'
metadata:
    description: 'Instalación y uso de la herramienta de análisis estático de código Phan. Anticipate a cualquier incidencia instalando Phan y analizando tu código.'
    keywords: 'phan, análisis estático, php7, análisis de código estático, incidencias php'
slug: analisis-estático-de-codigo-php-con-phan
routes:
    aliases:
        - /blog/analisis-estático-de-codigo-php-con-phan
---

[Phan](https://github.com/phan/phan) es un analizador estático de código que nos permite evaluar el código sin ejecutarlo, identificando potenciales problemas en etapas tempranas del ciclo de vida del software. Esto nos proporciona importantes ahorros de costes, ya que nos anticipamos a posibles problemas antes de que estos se hagan realidad.

===

Actualmente existen varios analizadores estáticos de código para PHP, sin embargo, en este artículo nos centraremos en la instalación, configuración y uso de [Phan](https://github.com/phan/phan) con **PHP 7.2** sobe **Ubuntu**. Si no sabes como instalar diferentes versiones de PHP en tu sistema puedes ver el artículo [Instalación de PHP 7.1, 7.2 y 7.3 en Ubuntu](/blog/installing-php-7-3-7-4-8-0-on-ubuntu).

### Requisitos

Primero debemos instalar algunos programas que necesitaremos para compilar la extensión [php-ast](https://github.com/nikic/php-ast):

```bash
sudo apt install git gcc php7.2-dev
```

Compilamos e instalamos la extensión [php-ast](https://github.com/nikic/php-ast), para ello ejecutamos las siguientes ordenes:

```bash
git clone git@github.com:nikic/php-ast.git
cd php-ast
phpize7.2
./configure --with-php-config=php-config7.2
make
sudo make install
echo "extension=/usr/lib/php/20170718/ast.so" > /etc/php/7.2/cli/conf.d/10-ast.ini
```

### Instalación

Una vez hemos compilado e instalado la extensión [php-ast](https://github.com/nikic/php-ast) podemos instalar normalmente [Phan](https://github.com/phan/phan) usando [composer](https://getcomposer.org/):

```bash
composer require phan/phan
```

Otra manera de instalar [Phan](https://github.com/phan/phan) es descargarlo en formato [.phar](http://php.net/manual/es/phar.using.intro.php):

```bash
# Con wget
wget https://github.com/phan/phan/releases/download/1.1.9/phan.phar -O phan.phar 

# Con curl
curl -L https://github.com/phan/phan/releases/download/1.1.9/phan.phar -o phan.phar
```

### Configuración

Ahora que tenemos [Phan](https://github.com/phan/phan) instalado debemos configurarlo para que realice el análisis que nosotros deseemos, podemos hacerlo de dos maneras, **automática** y **manual**, veamos ambas opciones:

#### Automática

La configuración automática creará un archivo de configuración en **.phan/phan.php** basado en el nivel de inicialización que usemos como argumento, adicionalmente podemos establecer que directorios o archivos serán analizados o si nuestro proyecto es un proyecto **composer**. Estas son todas las opciones de inicialización con las que cuenta phan:

* **--init-level**: Establece el nivel de la severidad del análisis, va de 1 (muy estricto) a 5 (poco estricto)
* **--init-analyze-dir**: Es el directorio que phan analizará.
* **--init-analyze-file**: Archivo .php que phan analizará.
* **--init-no-composer**: Si se usa, phan sabrá que está analizando un proyecto no composer.

Veamos un sencillo ejemplo de análisis poco estricto:

```bash
php phan.phar --init --init-level=5
[info] Disabling xdebug: Phan is around five times as slow when xdebug is enabled (xdebug only makes sense when debugging Phan itself)
[info] To run Phan with xdebug, set the environment variable PHAN_ALLOW_XDEBUG to 1.
[info] To disable this warning, set the environment variable PHAN_DISABLE_XDEBUG_WARN to 1.
[info] To include function signatures of xdebug, see .phan/internal_stubs/xdebug.phan_php
[debug] Checking PHAN_ALLOW_XDEBUG
[debug] The xdebug extension is loaded (2.6.1)
[debug] Process restarting (PHAN_ALLOW_XDEBUG=internal|2.6.1|1|*|*)
[debug] Running '/usr/bin/php7.2' '-n' '-c' '/tmp/bdtbjv' '/usr/local/bin/phan' '--init' '--init-level=5'
Successfully initialized '/var/www/html/phan.sample/.phan/config.php' with the following contents
...
[debug] Restarted process exited 0
```

Esto creará un archivo con el siguiente contenido:

```php
<?php

use Phan\Issue;

return [
    'target_php_version' => '7.0',
    'allow_missing_properties' => true,
    'null_casts_as_any_type' => true,
    'null_casts_as_array' => true,
    'array_casts_as_null' => true,
    'scalar_implicit_cast' => true,
    'scalar_array_key_cast' => true, 
    'scalar_implicit_partial' => [],
    'strict_method_checking' => false,
    'strict_param_checking' => false,
    'strict_return_checking' => false,
    'ignore_undeclared_variables_in_global_scope' => true,
    'ignore_undeclared_functions_with_known_signatures' => true,
    'backward_compatibility_checks' => false,
    'check_docblock_signature_return_type_match' => false,
    'prefer_narrowed_phpdoc_param_type' => true,
    'prefer_narrowed_phpdoc_return_type' => true,
    'analyze_signature_compatibility' => false,
    'phpdoc_type_mapping' => [],
    'dead_code_detection' => false,
    'unused_variable_detection' => false,
    'quick_mode' => true,
    'simplify_ast' => true,
    'generic_types_enabled' => true,
    'globals_type_map' => [],
    'minimum_severity' => Issue::SEVERITY_NORMAL,
    'suppress_issue_types' => [],
    'exclude_file_regex' => '@^vendor/.*/(tests?|Tests?)/@',
    'exclude_file_list' => [],
    'exclude_analysis_directory_list' => [
        'vendor/',
    ],
    'enable_include_path_checks' => false,
    'analyzed_file_extensions' => [
        'php',
    ],
    'autoload_internal_extension_signatures' => [],
    'plugins' => [],
    'directory_list' => [
        'src',
        'vendor/doctrine/inflector/lib/Doctrine/Common/Inflector',
        'vendor/guzzlehttp/guzzle/src',
        'vendor/mockery/mockery/library',
        'vendor/phpunit/phpunit/src',
    ],
    'file_list' => [],
];
```

* **target_php_version**: Es la versión de PHP para la que quieres realizar el análisis.
* **allow_missing_properties**: Verifica la asignación dinámica de propiedades.
* **null_casts_as_any_type**: Genera una incidencia si cualquier tipo es casteado a nulo.
* **null_casts_as_array**: Genera una incidencia si un valor nulo es casteado a array.
* **array_casts_as_null**: Genera una incidencia si un array es casteado como nulo.
* **scalar_implicit_cast**: Solo permite que los tipos escalares sean casteados a ellos mismos.
* **scalar_array_key_cast**: Las claves de un array solo pueden ser casteadas de int a string y viceversa
* **scalar_implicit_partial**: Es la relación de casteos permitidos para los valores escalares.
* **strict_method_checking**: Si está habilitado, Phan avisará si cualquier tipo en la expresión de objeto de un método definitivamente no es un objeto, o si cualquier tipo en una expresión invocada no es un "llamable".
* **strict_param_checking**: Si está habilitado, Phan avisará si algún tipo de argumento no se puede convertir a un tipo de parámetros esperado.
* **strict_return_checking**: Si está habilitado, Phan avisará si algún tipo de valor de retorno no se puede convertir a un tipo de retorno declarado.
* **ignore_undeclared_variables_in_global_scope**: Si es true, las variables aparentemente no declaradas en el alcance global serán ignoradas.
* **ignore_undeclared_functions_with_known_signatures**: Verifica las las funciones conocidas por Phan.
* **backward_compatibility_checks**: Comprobación de compatibilidad hacia atrás.
* **check_docblock_signature_return_type_match**: Si es true, verifique que el tipo de devolución declarado en el bloque de documentos (si existe) coincida con el tipo de devolución declarado en la firma del método.
* **prefer_narrowed_phpdoc_param_type**: Si es true, los tipos restringidos de parámetros de phpdoc anulan los tipos reales de la firma, cuando estos últimos existen.
* **prefer_narrowed_phpdoc_return_type**: Si es true, los tipos reducidos de phpdoc devuelven los tipos reales de la firma, cuando estos existen.
* **analyze_signature_compatibility**: Si está habilitado, marque todos los métodos que invaliden un método padre para asegurarse de que su firma sea compatible con la del padre.
* **phpdoc_type_mapping**: Esta configuración asigna cadenas que no distinguen entre mayúsculas y minúsculas a tipos de unión. Si el valor correspondiente es la cadena vacía, Phan ignorará ese tipo de unión (por ejemplo, puede ignorar 'the' en @return the value)
* **dead_code_detection**: Si está activado intentará detectar código muerto.
* **unused_variable_detection**: Intentará detectar si hay alguna variable sin uso.
* **quick_mode**: Si es true, esto ejecuta una versión rápida de las comprobaciones que lleva menos tiempo al costo de no ejecutarse como un análisis exhaustivo.
* **simplify_ast**: Si es true, antes del análisis, intente simplificar la AST en una forma que mejore la inferencia de tipo de Phan en los casos de borde.
* **generic_types_enabled**: Habilita o deshabilita el soporte para tipos de clases de plantillas genéricas.
* **globals_type_map**: Reemplaza la existencia de códigos y tipos de globales (no incorporados) en el ámbito global. Los nombres de las clases deben tener el prefijo \.
* **minimum_severity**: El nivel mínimo de severidad para informar. Esto se puede establecer en Issue::SEVERITY_LOW, Issue::SEVERITY_NORMAL o Issue::SEVERITY_CRITICAL.
* **suppress_issue_types**: Agregue cualquier tipo de problema a esta lista negra para impedir que se informe.
* **exclude_file_regex**: Una expresión regular para hacer coincidir los archivos que se excluirán del análisis y no se leerá en absoluto.
* **exclude_file_list**: Una lista de archivos que define los archivos que se excluirán del análisis y análisis y no se leerán en absoluto.
* **exclude_analysis_directory_list**: Una lista de directorios que define los archivos que se excluirán del análisis estático, pero cuya información de clase y método debe incluirse.
* **enable_include_path_checks**: Habilite esto para habilitar las comprobaciones de declaraciones de requerimiento / inclusión que se refieren a rutas válidas.
* **analyzed_file_extensions**: Lista de extensiones de archivo que no distinguen entre mayúsculas y minúsculas compatibles con Phan.
* **autoload_internal_extension_signatures**: Puede poner rutas a los apéndices de extensiones internas en esta opción de configuración. Si la extensión correspondiente no está cargada, Phan usará los apéndices en su lugar.
* **plugins**: Una lista de archivos de complementos para ejecutar.
* **directory_list**: Es la lista de directorios que deben analizarse para obtener información sobre las clases y los métodos.
* **file_list**: Una lista de archivos individuales para incluir en el análisis con una ruta relativa al directorio raíz del proyecto.

#### Manual

Debemos crear un archivo de configuración, por defecto [Phan](https://github.com/phan/phan) busca dicho archivo en **.phan/config.php** pero podemos situarlo y llamarlo dónde y cómo queramos, en este ejemplo lo situaremos en el directorio raíz del proyecto y lo llamaremos **phan.cfg.php**.

Dependiendo de los resultado que busquemos podemos utilizar una configuración u otra, por ejemplo:

##### Verificar la compatibilidad de código PHP 5 para PHP 7

```php
<?php
 
return [
    'directory_list' => [
        'app/',
    ],
    'backward_compatibility_checks' => true,
    'ignore_undeclared_functions_with_known_signatures' => false,
    'whitelist_issue_types' => [
        'PhanCompatiblePHP7',
        'PhanDeprecatedFunctionInternal',
        'PhanUndeclaredFunction',
    ],
];
```

* **directory_list**: Es la lista de directorios que deben analizarse para obtener información sobre las clases y los métodos.
* **backward_compatibility_checks**: Comprobación de compatibilidad hacía atrás.
* **ignore_undeclared_functions_with_known_signatures**: Verifica las funciones conocidas por Phan.
* **whitelist_issue_types**: Solo procesa las incidencias listadas aquí. Lista completa de [incidencias](https://github.com/phan/phan/wiki/Issue-Types-Caught-by-Phan).

##### Análisis de severidad mínima

```php
<?php
 
return [
    'target_php_version' => '7.2',
    'minimum_severity' => 0,
    'enable_include_path_checks' => true,
    'file_list' => [
        'includes.php',
    ],
    'directory_list' => [
        'app/',
        'vendor/',
    ],
    'exclude_analysis_directory_list' => [
        'vendor/',
    ],
    'plugins' => [
        'AlwaysReturnPlugin',
        'UnreachableCodePlugin',
        'DuplicateArrayKeyPlugin',
        'PregRegexCheckerPlugin',
        'PrintfCheckerPlugin',
    ],
];
```

### Análisis

Es momento de ejecutar Phan.

```bash
php phan.phar -o analysis.log
# o 
php phan.phar -k phan.cfg.php -o analysis.log
```

El resultado del análisis se volcará a un archivo llamado **analysis.log**. Podemos empezar el análisis haciendo un recuento de las incidencias que hay, como por ejemplo:

```bash
cat analysis.log | cut -d ' ' -f2 | sort | uniq -c | sort -n -r

    546 PhanUndeclaredClassMethod
    118 PhanUndeclaredClassCatch
     98 PhanUndeclaredFunction
     77 PhanNonClassMethodCall
     59 PhanUndeclaredClassConstant
      7 PhanUndeclaredExtendedClass
      7 PhanContextNotObject
      5 PhanAccessPropertyPrivate
      3 PhanAccessPropertyProtected
      2 PhanParentlessClass
      1 PhanUndeclaredInterface
      1 PhanUndeclaredClassInstanceof
      1 PhanUndeclaredClass
```

Podemos buscar un tipo de incidencia en concreto:

```php
cat analysis.log | grep PhanTypeInvalidThrowsIsInterface

src/Client.php:71 PhanTypeInvalidThrowsIsInterface @throws annotation of characters has suspicious interface type \GuzzleHttp\Exception\GuzzleException for an @throws annotation, expected class (PHP allows interfaces to be caught, so this might be intentional)
```

La lista completa de incidencias gestionadas por Phan está disponible en: [Issue Types Caught by Phan](https://github.com/phan/phan/wiki/Issue-Types-Caught-by-Phan).

Algunas de las incidencias que nos interesará vigilar a la hora de verificar la compatibilidad del código en PHP 7 son:

* **PhanCompatiblePHP7**: Esta incidencia se lanzará si hay una expresión que se pueda tratar de manera diferente en PHP7 de la que se tenía en las versiones principales anteriores del tiempo de ejecución de PHP.
* **PhanDeprecatedFunctionInternal**: Cuando se llama a una función deprecada.
* **PhanUndeclaredFunction**: Esta incidencia se emitirá si hace referencia a una función que no existe.

Una vez tenemos identificadas las incidencias más problemáticas de nuestro código, solo debemos ir una a una, solucionando las todas.
