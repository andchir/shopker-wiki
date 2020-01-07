Для оптимизации JS и CSS файлов используется [Symfony Webpack Encore](http://symfony.com/doc/3.4/frontend.html).
Для работы нужно установить [Node.js](https://nodejs.org/en/download/) и [NPM](https://www.npmjs.com/get-npm).

Для установки необходимых компонентов в терминале перейти в папку ``public`` и вызвать:
~~~
npm install
~~~
В папку ``/public/node_modules/`` будут загружены все пакеты из списка файла ``/public/package.json``.

Для сборки JS и CSS файлов нужно вызвать команду:
~~~
npm run build
~~~
В режиме разработки можно использовать команду:
~~~
npm run build:dev
~~~
В этом случае файлы в папке ``/public/app_build/`` будут автоматически обновляться при изменении исходных файлов.

Для сборки используется конфигурационный файл ``/public/webpack.config.js``.
Содержание по умолчанию:
~~~
var Encore = require('@symfony/webpack-encore');

Encore
    .setOutputPath('app_build/')
    .setPublicPath('/app_build/')
    .setManifestKeyPrefix('app_build')
    .addEntry('app', './js/app.js')
    .addStyleEntry('app_styles', [
        './node_modules/bootstrap/dist/css/bootstrap.min.css',
        './node_modules/nouislider/distribute/nouislider.min.css',
        './css/icomoon/style.css',
        './css/shopkeeper.css',
        './css/app.css'
    ])
    .cleanupOutputBeforeBuild()
    .autoProvidejQuery()
    .enableSourceMaps(!Encore.isProduction())
    .disableSingleRuntimeChunk();

module.exports = Encore.getWebpackConfig();
~~~

Используемые JavaScript библиотеки импортируются в файле ``/public/js/app.js``.

Таким образом все необходимые компоненты JS и CSS собираются в два файла: ``/public/app_build/app.js`` и ``/public/app_build/app_styles.css``.

В шаблон страницы они вставляются с помощью такого кода:
~~~
{% block stylesheets -%}
    <link href="{{ asset('app_build/app_styles.css') }}" rel="stylesheet">
{% endblock %}

{% block javascripts %}
    <script src="{{ asset('app_build/app.js') }}"></script>
{% endblock %}
~~~

Если вы не хотите использовать **Webpack Encore**, то в шаблоне вашего сайта все необходимые компоненты, которые используются в стандартном шаблоне, можно подключить так:

~~~
{% block stylesheets -%}
    <link href="{{ asset('node_modules/bootstrap/dist/css/bootstrap.min.css') }}" rel="stylesheet">
    <link href="{{ asset('node_modules/nouislider/distribute/nouislider.min.css') }}" rel="stylesheet">
    <link href="{{ asset('css/icomoon/style.css') }}" rel="stylesheet">
    <link href="{{ asset('css/app.css') }}" rel="stylesheet">
{% endblock %}

{% block javascripts %}
    <script src="{{ asset('node_modules/jquery/dist/jquery.min.js') }}"></script>
    <script src="{{ asset('node_modules/popper.js/dist/umd/popper.min.js') }}"></script>
    <script src="{{ asset('node_modules/bootstrap/dist/js/bootstrap.min.js') }}"></script>
    <script src="{{ asset('node_modules/wnumb/wNumb.js') }}"></script>
    <script src="{{ asset('node_modules/nouislider/distribute/nouislider.min.js') }}"></script>
    <script src="{{ asset('js/shopkeeper.js') }}"></script>
    <script>
        var shk = new Shopkeeper();
    </script>
{% endblock %}
~~~

Дополнительные материалы
------------------------
- [Документация Symfony Webpack Encore](https://symfony.com/doc/current/frontend.html)
