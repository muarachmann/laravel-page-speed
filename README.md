# Laravel Page Speed
[![Project on GitScrum](https://gitscrum.com/badges/project.svg?project=gitscrum/bulls-eye-gitscrum-37)](https://gitscrum.com)
[![Build Status](https://travis-ci.org/renatomarinho/laravel-page-speed.svg?branch=master)](https://travis-ci.org/renatomarinho/laravel-page-speed)
[![License](https://poser.pugx.org/renatomarinho/laravel-page-speed/license)](https://packagist.org/packages/renatomarinho/laravel-page-speed)
[![Latest Stable Version](https://poser.pugx.org/renatomarinho/laravel-page-speed/version)](https://packagist.org/packages/renatomarinho/laravel-page-speed)
[![Total Downloads][icon-downloads]][link-downloads]

### Simple package to minify HTML output on demand which results in a 35%+ optimization.

## Installation

> **Requires:**
- **[PHP 7.1.3+](https://php.net/releases/)**
- **[Laravel 5.5+](https://github.com/laravel/laravel)**

You can install the package via composer:

```sh
composer require renatomarinho/laravel-page-speed
```

This package supports Laravel [Package Discovery][link-package-discovery].
 
### Publish configuration file

 `php artisan vendor:publish --provider="RenatoMarinho\LaravelPageSpeed\ServiceProvider"`

## Do not forget to register middlewares

Next, the `\RenatoMarinho\LaravelPageSpeed\Middleware\CollapseWhitespace::class` and other middleware must be registered in the kernel, for example:

```php
//app/Http/Kernel.php

protected $middleware = [
    ...
    \RenatoMarinho\LaravelPageSpeed\Middleware\InlineCss::class,
    \RenatoMarinho\LaravelPageSpeed\Middleware\ElideAttributes::class,
    \RenatoMarinho\LaravelPageSpeed\Middleware\InsertDNSPrefetch::class,
    \RenatoMarinho\LaravelPageSpeed\Middleware\RemoveComments::class,
    \RenatoMarinho\LaravelPageSpeed\Middleware\TrimUrls::class,
    \RenatoMarinho\LaravelPageSpeed\Middleware\RemoveQuotes::class,
    \RenatoMarinho\LaravelPageSpeed\Middleware\CollapseWhitespace::class, // Note: This middleware invokes "RemoveComments::class" before it runs.
]
```

## Middlewares Details

### \RenatoMarinho\LaravelPageSpeed\Middleware\RemoveComments::class

The **RemoveComments::class** filter eliminates HTML, JS and CSS comments.
The filter reduces the transfer size of HTML files by removing the comments. Depending on the HTML file, this filter can significantly reduce the number of bytes transmitted on the network.

### \RenatoMarinho\LaravelPageSpeed\Middleware\CollapseWhitespace::class

The **CollapseWhitespace::class** filter reduces bytes transmitted in an HTML file by removing unnecessary whitespace.
This middleware invoke **RemoveComments::class** filter before executation.

> **Note**: Do not register the "RemoveComments::class" filter with it. Because it will be called automatically by "CollapseWhitespace::class"

#### Before

![Before of Laravel Page Speed][link-before]

#### After

![After of Laravel Page Speed][link-after]

### \RenatoMarinho\LaravelPageSpeed\Middleware\RemoveQuotes::class

The **RemoveQuotes::class** filter eliminates unnecessary quotation marks from HTML attributes. While required by the various HTML specifications, browsers permit their omission when the value of an attribute is composed of a certain subset of characters (alphanumerics and some punctuation characters).

Quote removal produces a modest savings in byte count on most pages.

### \RenatoMarinho\LaravelPageSpeed\Middleware\ElideAttributes::class

The **ElideAttributes::class** filter reduces the transfer size of HTML files by removing attributes from tags when the specified value is equal to the default value for that attribute. This can save a modest number of bytes, and may make the document more compressible by canonicalizing the affected tags.

### \RenatoMarinho\LaravelPageSpeed\Middleware\InsertDNSPrefetch::class

The **InsertDNSPrefetch::class** filter Injects <link rel="dns-prefetch" href="//www.example.com"> tags in the HEAD to enable the browser to do DNS prefetching.

DNS resolution time varies from <1ms for locally cached results, to hundreds of milliseconds due to the cascading nature of DNS. This can contribute significantly towards total page load time. This filter reduces DNS lookup time by providing hints to the browser at the beginning of the HTML, which allows the browser to pre-resolve DNS for resources on the page.

### \RenatoMarinho\LaravelPageSpeed\Middleware\TrimUrls::class,

The **TrimUrls::class** filter trims URLs by resolving them by making them relative to the base URL for the page.

> **Warning**: **TrimUrls::class** is considered **medium risk**. It can cause problems if it uses the wrong base URL. This can happen, for example, if you serve HTML that will be pasted verbatim into other HTML pages. If URLs are trimmed on the first page, they will be incorrect for the page they are inserted into. In this case, just disable the middleware.

### \RenatoMarinho\LaravelPageSpeed\Middleware\InlineCss::class

The **InlineCss::class** filter transforms the inline "style" attribute of tags into classes by moving the CSS to the header.

<hr>

## Configuration

After installing package, you may need to configure some options.

### Disable Service

You would probably like to set up the local environment to get a readable output.

```php
//config/laravel-page-speed.php

//Set this field to false to disable the laravel page speed service.
'enable' => env('LARAVEL_PAGE_SPEED_ENABLE', true),
```
### Skip routes

You would probably like to configure the package to skip some routes.

```php
//config/laravel-page-speed.php

//You can use * as wildcard.
'skip' => [
    '*.pdf', //Ignore all routes with final .pdf
    '*/downloads/*',//Ignore all routes that contain 'downloads'
    'assets/*', // Ignore all routes with the 'assets' prefix
];
```

By default this field comes configured with some options, so feel free to configure according to your needs...

> *Notice*: This package skip automatically 'binary' and 'streamed' responses. See [File Downloads][link-file-download].

## Testing

```sh
$ composer test
```

## Contributing

Please see [CONTRIBUTING](CONTRIBUTING.md) for details.

## Credits

- [Renato Marinho][link-author]
- [João Roberto P. Borges][link-maintainer]
- [All Contributors][link-contributors]

## Inspiration 

#### Mod Page Speed (https://www.modpagespeed.com/)

## License

The MIT License (MIT). Please see [License File](LICENSE.md) for more information.

[icon-downloads]: https://poser.pugx.org/renatomarinho/laravel-page-speed/downloads

[link-laravel]: https://laravel.com
[link-downloads]: https://packagist.org/packages/renatomarinho/laravel-page-speed
[link-before]: https://i.imgur.com/cN3MWYh.png
[link-after]: https://i.imgur.com/IKWKLkL.png
[link-author]: https://github.com/renatomarinho
[link-maintainer]: https://github.com/joaorobertopb
[link-contributors]: ../../contributors
[link-file-download]: https://laravel.com/docs/6.0/responses#file-downloads
[link-package-discovery]: https://laravel.com/docs/6.0/packages#package-discovery
