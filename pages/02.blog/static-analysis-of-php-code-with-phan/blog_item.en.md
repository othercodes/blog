---
title: 'Static Analysis of PHP Code with Phan'
twitterenable: true
twittercardoptions: summary
articleenabled: false
facebookenable: true
taxonomy:
    category:
        - blog
    tag:
        - php
        - 'static code analysis'
        - php-ast
        - phan
image:
    summary:
        enabled: '1'
    text:
        enabled: '1'
date: '01-01-2019 12:29'
publish_date: '07-01-2019 12:29'
metadata:
    description: 'Installation and use of the Phan code static analysis tool. Anticipate any incident by installing Phan and analyzing your code.'
    'og:url': 'https://othercode.io/blog/static-analysis-of-php-code-with-phan'
    'og:type': article
    'og:title': 'Static Analysis of PHP Code with Phan | otherCode'
    'og:description': 'Installation and use of the Phan code static analysis tool. Anticipate any incident by installing Phan and analyzing your code.'
    'og:image': 'https://othercode.io/user/pages/02.blog/static-analysis-of-php-code-with-phan/featured.jpeg'
    'og:image:type': image/jpeg
    'og:image:width': 800
    'og:image:height': 350
    'og:author': otherCode
    'twitter:card': summary_large_image
    'twitter:title': 'Static Analysis of PHP Code with Phan | otherCode'
    'twitter:description': 'Installation and use of the Phan code static analysis tool. Anticipate any incident by installing Phan and analyzing your code.'
    'twitter:site': '@othercode'
    'twitter:creator': '@othercode'
    'twitter:image': 'https://othercode.io/user/pages/02.blog/static-analysis-of-php-code-with-phan/featured.jpeg'
    'article:published_time': '2019-01-07T12:29:00+10:00'
    'article:modified_time': '2024-05-17T20:08:25+10:00'
    'article:author': otherCode
    keywords: 'phan, static analysis, php7, static code analysis, php issues'
aura:
    metadata:
        keywords: 'phan, static analysis, php7, static code analysis, php issues'
    pagetype: article
    image: featured.jpeg
    description: 'Installation and use of the Phan code static analysis tool. Anticipate any incident by installing Phan and analyzing your code.'
media_order: 'featured.jpeg,featured.webp'
---

[Phan](https://github.com/phan/phan) is a static code analyzer that allows us to evaluate the code without executing it, identifying potential problems in early stages of the software life cycle. This provides us with significant cost savings, as we anticipate potential problems before they become reality.

===

Currently there are several static code analyzers for PHP, however, in this article we will focus on the installation, configuration and use of [Phan](https://github.com/phan/phan) with **PHP 7.2** sobe **Ubuntu**. If you don't know how to install different versions of PHP on your system, you can see the article [Installing PHP 7.1, 7.2 and 7.3 on Ubuntu](/blog/installing-php-7-3-7-4-8-0-on-ubuntu).

### Requirements

First we must install some programs that we will need to compile the extension [php-ast](https://github.com/nikic/php-ast):

```bash
sudo apt install git gcc php7.2-dev
```

We compile and install the extension [php-ast](https://github.com/nikic/php-ast), to do this we execute the following commands:

```bash
git clone git@github.com:nikic/php-ast.git
cd php-ast
phpize7.2
./configure --with-php-config=php-config7.2
make
sudo make install
echo "extension=/usr/lib/php/20170718/ast.so" > /etc/php/7.2/cli/conf.d/10-ast.ini
```

### Installation

Once we have compiled and installed the [php-ast](https://github.com/nikic/php-ast) extension we can normally install [Phan](https://github.com/phan/phan) using [composer](https://getcomposer.org/):

```bash
composer require phan/phan
```

Another way to install [Phan](https://github.com/phan/phan) is to download it in [.phar](http://php.net/manual/es/phar.using.intro.php) format:

```bash
# Con wget
wget https://github.com/phan/phan/releases/download/1.1.9/phan.phar -O phan.phar 

# Con curl
curl -L https://github.com/phan/phan/releases/download/1.1.9/phan.phar -o phan.phar
```

### Configuration

Now that we have [Phan](https://github.com/phan/phan) installed we must configure it to perform the analysis that we want, we can do it in two ways, **automatic** and **manual**, let's see both options:

#### Automatic

The automatic configuration will create a configuration file in **.phan/phan.php** based on the initialization level that we use as an argument, additionally we can establish which directories or files will be analyzed or if our project is a **composer* project *. These are all the initialization options that phan has:

* **--init-level**: Sets the level of the severity of the scan, ranging from 1 (very strict) to 5 (loose)
* **--init-analyze-dir**: This is the directory that phan will analyze.
* **--init-analyze-file**: .php file that phan will analyze.
* **--init-no-composer**: If used, phan will know that it is parsing a non-composer project.

Let's look at a simple example of lax analysis:

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

This will create a file with the following content:

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
* **target_php_version**: This is the PHP version for which you want to perform the analysis.
* **allow_missing_properties**: Check for dynamic property assignment.
* **null_casts_as_any_type**: Raise an issue if any type is cast to null.
* **null_casts_as_array**: Generates an issue if a null value is cast to an array.
* **array_casts_as_null**: Generates an issue if an array is cast as null.
* **scalar_implicit_cast**: Only allows scalar types to be cast to themselves.
* **scalar_array_key_cast**: The keys of an array can only be cast from int to string and vice versa
* **scalar_implicit_partial**: This is the ratio of casts allowed for scalar values.
* **strict_method_checking**: If enabled, Phan will warn if any type in a method's object expression is definitely not an object, or if any type in an invoked expression is not a "callable".
* **strict_param_checking**: If enabled, Phan will warn if some argument type cannot be converted to an expected parameter type.
* **strict_return_checking**: If enabled, Phan will warn if any return value type cannot be converted to a declared return type.
* **ignore_undeclared_variables_in_global_scope**: If true, apparently undeclared variables in the global scope will be ignored.
* **ignore_undeclared_functions_with_known_signatures**: Check the functions known by Phan.
* **backward_compatibility_checks**: Backward compatibility check.
* **check_docblock_signature_return_type_match**: If true, check that the return type declared in the docblock (if any) matches the return type declared in the method signature.
* **prefer_narrowed_phpdoc_param_type**: If true, the restricted types of phpdoc parameters override the actual types of the signature, when the latter exist.
* **prefer_narrowed_phpdoc_return_type**: If true, phpdoc narrowed types return the signature's actual types, when these exist.
* **analyze_signature_compatibility**: If enabled, check all methods that override a parent method to ensure that their signature is compatible with that of the parent.
* **phpdoc_type_mapping**: This setting maps case-insensitive strings to union types. If the corresponding value is the empty string, Phan will ignore that union type (e.g. you can ignore 'the' in @return the value)
* **dead_code_detection**: If enabled it will try to detect dead code.
* **unused_variable_detection**: Will try to detect if there are any unused variables.
* **quick_mode**: If true, this runs a quick version of the checks that takes less time at the cost of not running as a deep scan.
* **simplify_ast**: If true, before parsing, try to simplify the AST in a way that improves Phan type inference in edge cases.
* **generic_types_enabled**: Enables or disables support for generic template class types.
* **globals_type_map**: Overrides the existence of (non-builtin) globals tags and types in the global scope. Class names must be prefixed with \.
* **minimum_severity**: The minimum level of severity to report. This can be set to Issue::SEVERITY_LOW, Issue::SEVERITY_NORMAL or Issue::SEVERITY_CRITICAL.
* **suppress_issue_types**: Add any issue type to this blacklist to prevent it from being reported.
* **exclude_file_regex**: A regular expression to match files that will be excluded from scanning and not read at all.
* **exclude_file_list**: A file list defining files that will be excluded from scanning and analysis and not read at all.
* **exclude_analysis_directory_list**: A list of directories defining files that will be excluded from static analysis, but whose class and method information must be included.
* **enable_include_path_checks**: Enable this to enable checks for require/include statements that refer to valid paths.
* **analyzed_file_extensions**: List of case-insensitive file extensions supported by Phan.
* **autoload_internal_extension_signatures**: You can put paths to internal extension stubs in this configuration option. If the corresponding extension is not loaded, Phan will use the stubs instead.
* **plugins**: A list of plugin files to run.
* **directory_list**: It is the list of directories that must be analyzed to obtain information about classes and methods.
* **file_list**: A list of individual files to include in the analysis with a path relative to the project's root directory.

#### Manual

We must create a configuration file, by default [Phan](https://github.com/phan/phan) looks for said file in **.phan/config.php** but we can place it and call it wherever and how we want, in We will place this example in the root directory of the project and call it **phan.cfg.php**.

Depending on the results we are looking for, we can use one configuration or another, for example:

##### Check PHP 5 Code Compatibility for PHP 7

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

* **directory_list**: It is the list of directories that must be analyzed to obtain information about classes and methods.
* **backward_compatibility_checks**: Backward compatibility check.
* **ignore_undeclared_functions_with_known_signatures**: Check for functions known to Phan.
* **whitelist_issue_types**: Only processes the issues listed here. Full list of [issues](https://github.com/phan/phan/wiki/Issue-Types-Caught-by-Phan).

##### Minimum severity analysis

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

### Analysis

It's time to execute Phan.

```bash
php phan.phar -o analysis.log
# o 
php phan.phar -k phan.cfg.php -o analysis.log
```

The analysis result will be dumped to a file called **analysis.log**. We can begin the analysis by counting the incidents that exist, such as:

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

We can search for a specific type of incident:

```php
cat analysis.log | grep PhanTypeInvalidThrowsIsInterface

src/Client.php:71 PhanTypeInvalidThrowsIsInterface @throws annotation of characters has suspicious interface type \GuzzleHttp\Exception\GuzzleException for an @throws annotation, expected class (PHP allows interfaces to be caught, so this might be intentional)
```

The complete list of issues handled by Phan is available at: [Issue Types Caught by Phan](https://github.com/phan/phan/wiki/Issue-Types-Caught-by-Phan).

Some of the incidents that we will be interested in monitoring when verifying the compatibility of the code in PHP 7 are:

* **PhanCompatiblePHP7**: This issue will be raised if there is an expression that can be treated differently in PHP7 than it was in previous major versions of the PHP runtime.
* **PhanDeprecatedFunctionInternal**: When a deprecated function is called.
* **PhanUndeclaredFunction**: This issue will be raised if you reference a function that does not exist.

Once we have identified the most problematic incidents in our code, we only have to go one by one, solving them all.
