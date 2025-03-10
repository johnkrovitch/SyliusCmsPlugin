## Installation


##### 1. *We work on stable, supported and up-to-date versions of packages. We recommend you to do the same.*

```bash
$ composer require bitbag/cms-plugin --no-scripts
```

##### 2. Add plugin dependencies to your `config/bundles.php` file:

```php
return [
    ...
    BitBag\SyliusCmsPlugin\BitBagSyliusCmsPlugin::class  => ['all' => true],
];
```

##### 3. Configure a WYSIWYG editor.

The plugin supports two WYSIWYG editors: [FOS CKEditor](https://symfony.com/doc/master/bundles/FOSCKEditorBundle/usage/ckeditor.html) by default and [Trix](https://trix-editor.org/) as an alternative. 

You can choose which one you want to use by following one of the guides below:

- [Trix WYSIWYG config](./trix-config.md)*
- [CKeditor WYSIWYG config](./ckeditor-config.md)*

##### 4. Import required config in your `config/packages/_sylius.yaml` file:
```yaml
# config/packages/_sylius.yaml

imports:
    ...
    
    - { resource: "@BitBagSyliusCmsPlugin/Resources/config/config.yml" }
```

##### 5. Import routing in your `config/routes.yaml` file:

```yaml

# config/routes.yaml
...

bitbag_sylius_cms_plugin:
    resource: "@BitBagSyliusCmsPlugin/Resources/config/routing.yml"
```
If You have installed https://github.com/stefandoorn/sitemap-plugin according to its installation instructions
import optional sitemap providers:
```yaml
# config/services.yaml
...
imports:
...
    - { resource: "@BitBagSyliusCmsPlugin/Resources/config/services/sitemap_provider.yml" }
```

and plugin dependency to your `config/bundles.php` file:
```php
return [
    ...

    SitemapPlugin\SitemapPlugin::class => ['all' => true], // Sitemap support
];
```

you will probably need to change the extension of the imported file in 

```yaml
# config/packages/sitemap_plugin.yaml

    imports:
        - { resource: "@SitemapPlugin/Resources/config/config.yaml" }
```

##### 6. Finish the installation by updating the database schema and installing assets:

```bash
$ bin/console cache:clear

# If you used migrations in your project...
$ bin/console doctrine:migrations:migrate
# ... or if you use doctrine schema tool.
$ bin/console doctrine:schema:update --dump-sql # and --force switch when you're ready :)

$ bin/console assets:install --symlink
$ bin/console sylius:theme:assets:install --symlink
```

**Note.** In some cases, the `--symlink` option [may throw some errors](https://github.com/Sylius/SyliusThemeBundle/issues/91). If you consider running the commands without the `--symlink` option, please keep in mind to run them on every potential plugin update.

##### 7. Add plugin assets to your project

We recommend you to use Webpack (Encore), for which we have prepared four different instructions on how to add this plugin's assets to your project:

- [Import webpack config](./01.1-webpack-config.md)*
- [Add entry to existing config](./01.2-webpack-entry.md))
- [Import entries in your entry.js files](./01.3-import-entry.md))
- [Your own custom config](./01.4-custom-solution.md))

<small>* Default option for plugin development</small>


However, if you are not using Webpack, here are instructions on how to add optimized and compressed assets directly to your project templates:

- [Non webpack solution](./01.5-non-webpack.md)

##### 8. Passing required "backend" values to "frontend"

In order to make plugin finally work you need to declare "route", in admin _scripts.html.twig you can pass:

```
<script>
    const route = "{{ path('bitbag_sylius_cms_plugin_admin_ajax_media_by_name_phrase')|escape('js') }}";
</script>
```

Any other approach, that will allow cms pages to read this value in js, under "route" key, will work. 

## Testing & running the plugin
```bash
$ composer install
$ cd tests/Application
```
Copy file `package.json.~1.XX.0.dist` to `package.json` where `~1.XX.0` is the Sylius version you are using.

```bash
$ cp package.json.~1.12.0.dist package.json
```

```bash
$ yarn install
$ yarn encore dev
$ APP_ENV=test bin/console assets:install
$ APP_ENV=test bin/console doctrine:schema:create
$ APP_ENV=test symfony server:start --port=8080 -d
$ cd ../..
$ open http://localhost:8080
$ vendor/bin/behat
$ vendor/bin/phpspec run
```
