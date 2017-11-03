Yii 2 Composer Installer
========================

This is the composer installer for Yii 2 extensions. It implements a new composer package 
type named `yii2-extension`, which should be used by all Yii 2 extensions if they are 
distributed as composer packages.


Usage
-----

To use Yii 2 composer installer, simply set the package `type` to be `yii2-extension` in your `composer.json`,
like the following:

```json
{
    "type": "yii2-extension",
    "require": {
        "yiisoft/yii2": "*"
    },
    ...
}
```

You may specify a bootstrapping class in the `extra` section. The `init()` method of the class will be executed each time
the Yii 2 application is responding to a request. For example,

```json
{
    "type": "yii2-extension",
    ...,
    "extra": {
        "bootstrap": "yii\\jui\\Extension"
    }
}
```

The `Installer` class also implements a static method `postCreateProject()` that can be called after
a Yii 2 project is created, through the `post-create-project-cmd` composer script.
A similar method exists for running tasks after each `composer install` call, which sis `postInstall()`.
These methods allow to run other `Installer` class methods like `setPermission()` or `generateCookieValidationKey()`, 
depending on the corresponding parameters set in the `extra` section of the `composer.json` file.
For example,

```json
{
    "name": "yiisoft/yii2-app-basic",
    "type": "project",
    ...
    "scripts": {
        "post-create-project-cmd": [
            "futuretek\\composer\\Installer::postCreateProject"
        ],
        "post-install-cmd": [
            "futuretek\\composer\\Installer::postInstall"
        ]
    },
    "extra": {
        "futuretek\\composer\\Installer::postCreateProject": {
            "setPermission": [
                {
                    "runtime": "0777",
                    "web/assets": "0777",
                    "yii": "0755"
                }
            ]
        },
        "futuretek\\composer\\Installer::postInstall": {
            "copyFiles": [
                {
                    "config/templates/console-local.php": "config/console-local.php",
                    "config/templates/web-local.php": "config/web-local.php",
                    "config/templates/db-local.php": "config/db-local.php",
                    "config/templates/cache.json": ["runtime/cache.json", true]
                }
            ],
            "generateCookieValidationKey": [
                "config/web-local.php"
            ]
        }
    }
}
```

Added features
--------------
* Auto registering translations from extension to main application PHPMessageSource
* Extensions can propagate settings to yii config via `/vendor/futuretek/*.php` files

TODO
----
* Better documentation