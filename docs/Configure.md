# Devilbox Documentation

[Overview](README.md) |
[Quickstart](Quickstart.md) |
[Install](Install.md) |
[Update](Update.md) |
Configure |
[Run](Run.md) |
[Usage](Usage.md) |
[OS](OS.md) |
[Backups](Backups.md) |
[Examples](Examples.md) |
[Technical](Technical.md) |
[Hacking](Hacking.md) |
[FAQ](FAQ.md)

---

## Configure

1. [Overview](#1-overview)
    1. [The devilbox `.env` file](#11-the-devilbox-env-file)
    2. [The devilbox `cfg/` directory](#12-the-devilbox-cfg-directory)
    3. [The devilbox `mod/` directory](#13-the-devilbox-mod-directory)
        1. [Custom PHP module example](#134-custom-php-module-example)
    4. [The devilbox `bash/` directory](#14-the-devilbox-bash-directory)
    5. [The operating system `hosts` file](#15-the-operating-system-hosts-file)
    6. [The operating system `resolv.conf` file](#16-the-operating-system-resolvconf-file)
2. [Devilbox general settings](#2-devilbox-general-settings)
    1. [Verbosity](#21-verbosity)
    2. [Devilbox base path](#22-devilbox-base-path)
    3. [Host computer listening address](#23-host-computer-listening-address)
3. [Project settings](#3-project-settings)
    1. [Project domain](#31-project-domain)
    2. [Project path](#32-project-path)
    3. [Project htdocs directory](#33-project-htdocs directory)
4. [Container settings](#4-container-settings)
    1. [General](#41-general)
        1. [Timezone](#411-timezone)
        2. [User id](#412-user-id)
        3. [Group id](#413-group-id)
    2. [PHP / HHVM](#42-php--hhvm)
        1. [Select PHP version](#421-select-php-version)
        2. [Xdebug](#422-xdebug)
        3. [php.ini](#423-phpini)
        4. [HHVM](#424-hhvm)
        5. [Custom PHP modules](#425-custom-php-modules)
        6. [Customize bash and other tools](#426-customize-bash-and-other-tools)
		7. [Custom environment variables](#427-custom-environment-variables)
    3. [Apache / Nginx](#43-apache--nginx)
        1. [Select Httpd version](#431-select-httpd-version)
        2. [Host port](#432-host-port)
		3. [Customize the vhost configuration](#433-customize-the-vhost-configuration)
    4. [MySQL](#44-mysql)
        1. [Select MySQL version](#441-select-mysql-version)
        2. [Root password](#442-root-password)
        3. [General Log](#443-general-log)
        4. [Host port](#444-host-port)
        5. [Data path](#445-data-path)
        6. [my.cnf](#446-my-cnf)
    5. [PostgreSQL](#45-postgresql)
        1. [Select PostgreSQL version](#451-select-postgresql-version)
        2. [Root user](#452-root-user)
        3. [Root password](#453-root-password)
        4. [Host port](#454-host-port)
        5. [Data path](#455-data-path)
    6. [Redis](#46-redis)
        1. [Select Redis version](#461-select-redis-version)
        2. [Host port](#462-host-port)
    7. [Memcached](#47-memcached)
        1. [Select Memcached version](#471-select-memcached-version)
        2. [Host port](#472-host-port)
    8. [MongoDB](#48-mongodb)
        1. [Select MongoDB version](#481-select-mongodb-version)
        2. [Host port](#482-host-port)
        3. [Data path](#483-data-path)
    9. [Bind](#49-bind)
        1. [Upstream resolver](#491-upstream-resolver)
        2. [Host port](#492-host-port)
5. [Intranet settings](#5-intranet-settings)
    1. [DNS check timeout](#51-dns-check-timeout)
    2. [Password protection](#52-password-protection)
	3. [Disable Intranet](#53-disable-intranet)
6. [Host computer](#6-host-computer)
    1. [/etc/hosts](#61-etchosts)
    2. [Auto-DNS](#62-auto-dns)

---

## 1. Overview

There are only four things you can configure on your host computer.

#### 1.1 The devilbox `.env` file

All docker-compose configuration is done inside the `.env` file which simply defines key-value variables parsed to `docker-compose.yml`.

First thing to do after cloning this repository is creating this file from the `env-example`:

```shell
$ cp env-example .env
```

When you update the devilbox git directory, you should always check if `env-example` introduces new configuration options. You can do that by comparing the `env-example` file with your current `.env` file.

```shell
$ vimdiff env-example .env
```

You can get more information here:

> [What is the `.env` file?](https://docs.docker.com/compose/env-file/)

#### 1.2 The devilbox `cfg/` directory

Inside the devilbox root directory you will find a folder called `cfg/`. This will contain subdirectories in the form of `<SERVICE>-<VERSION>`. Those folders will be mounted into the appropriate location into the respective docker container in order to overwrite service configuration.

Currently only MySQL/MariaDB, PHP/HHVM and Apache/Nginx overrides are supported.

The folder structure looks like this:
```
cfg/
  apache-2.2/
  apache-2.4/
  nginx-mainline/
  nginx-stable/

  hhvm-latest/
  php-fpm-5.4/
  php-fpm-5.5/
  php-fpm-5.6/
  php-fpm-7.0/
  php-fpm-7.1/
  php-fpm-7.2/

  mariadb-5.5/
  mariadb-10.0/
  mariadb-10.1/
  mariadb-10.2/
  mariadb-10.3/

  mysql-5.5/
  mysql-5.6/
  mysql-5.7/
  mysql-8.0/

  percona-5.5/
  percona-5.6/
  percona-5.7/
```

Each of the folders will contain an example file in the following format:
```
devilbox-custom.<ext>-example
```

Only files which have the correct file extensions will be read, all others such as `*.<ext>-example` will be ignored.

* Valid PHP config extension: `.ini`
* Valid MySQL config extension: `.cnf`
* Valid Httpd config extension: `.conf`

#### 1.3 The devilbox `mod/` directory

Inside the devilbox root directory you will find a folder called `mod/`. This will contain subdirectories in the form of `(php-fpm|hhvm)-<VERSION>`. Those folders will be mounted into the appropriate location into the respective PHP docker container under `/usr/lib64/php/custom-modules/` in order to provide custom PHP modules.

The folder structure looks like this:
```
cfg/
  hhvm-latest/
  php-fpm-5.4/
  php-fpm-5.5/
  php-fpm-5.6/
  php-fpm-7.0/
  php-fpm-7.1/
  php-fpm-7.2/
```

Each of the folders are empty by default. Place any PHP modules (`*.so`) inside the versioned folder of your choice.

Modules placed in the above folders are not loaded by default. You will also have to enable them via your custom configuration as described in the above section.

##### 1.3.4 Custom PHP module example

The following example will add a custom [ioncube](https://www.ioncube.com) module for PHP 7.0:

1. Copy `ioncube_loader_lin_7.0.so` to `mod/php-fpm-7.0/ioncube_loader_lin_7.0.so`
2. Create custom config to enable ioncube in: `cfg/php-fpm-7.0/00-ioncube.ini`
```shell
zend_extension = /usr/lib64/php/custom-modules/ioncube_loader_lin_7.0.so
```

**Note:** PHP configuration files are loaded by file names in alphabetical order and the ioncube zend extension needs to be loaded before any other zend extension. This is the reason why its configuration file name starts with `00-`.

#### 1.4 The devilbox `bash/` directory

Inside the devilbox root directory you will find a folder called `bash/`. Every file inside this folder ending by `*.sh` will be source by your bash, allowing for a customized bash configuration. All files not ending by `*.sh` will be ignored and can be used to create config files for other programs.

The `bash/` folder will be mounted into the PHP/HHVM container to `/etc/bashrc-devilbox.d/`.

#### 1.5 The operating system `hosts` file

On Linux and OSX your hosts file is located at `/etc/hosts` on Windows it will be at `C:\Windows\System32\drivers\etc`. Use this file to setup custom DNS entries if you are not using Auto-DNS.

Read up on it below at `/etc/hosts` or `Auto-DNS` section.

#### 1.6 The operating system `resolv.conf` file

This file is used to add the devilbox DNS server for Auto-DNS.

Read up on it below at `/etc/hosts` or `Auto-DNS` section.


## 2. Devilbox general settings

#### 2.1 Verbosity

| `.env` file variable name | Default | Note |
|---------------------------|---------|------|
| DEBUG_COMPOSE_ENTRYPOINT  | `0`     | Set it to `1` or `0` |

If set to `1`, it will show all executed commands during docker startup.

#### 2.2 Devilbox base path

| `.env` file variable name | Default | Note |
|---------------------------|---------|------|
| DEVILBOX_PATH             | `.`     | Relative or absolute path allowed.<br/><strong>Note:</strong> Container need to be re-created after changing this value. |

This is the base path that will be prepended to all mount paths specified in `.env`.
You will usually not need to change this value..

**IMPORTANT:** When changing this path, it will affect the paths of all other mounted DATA directories. You must therefore also remove any stopped container so that they will be re-created with new different mount points during the next run. In order to accomplish this obey the following precedure:

```shell
# Stop the container
$ docker-compose stop

# Remove the stopped container (IMPORTANT!)
# After the removal it will be re-created during next run
$ docker-compose rm -f

# Edit any path variables
$ vim .env

# Start your stack
$ docker-compose up -d
```

#### 2.3 Host computer listening address

| `.env` file variable name | Default | Note |
|---------------------------|---------|------|
| LOCAL_LISTEN_ADDRESS      | `127.0.0.1:`   | Address for every service to listen on the Docker host.<br/>Pay attention to the **`:`** after the IP address |

This determines the Host address your webserver and all other daemons should listen on.

In case it is not `127.0.0.1` (because you are using a VirtualBox Docker setup) change it to the IP address of the VirtualBox host. Otherwise you will not need to change this value.

1. When you remove it completely, it will listen on all interfaces.
2. When you use specific address, you must add a **`:`** at the end.

You can for example change it to `0.0.0.0:` or make it empty in order to listen on all interfaces. This enables it for other people inside your network to access the devilbox. Or you can even install the devilbox on a different computer and access it remotely.

## 3. Project settings

#### 3.1 Project domain

| `.env` file variable name | Default | Note |
|---------------------------|---------|------|
| TLD_SUFFIX                | `loc`   | Domain suffix for all your project. Can also be a subdomain such `work.loc` |

Each project will be served by `http://<project-folder>.<TLD_SUFFIX>`. If you want to change the default `loc` domain suffix to something else such as local, adjust this variable. Here are a few examples:

| Project Folder | TLD_SUFFIX | URL |
|----------------|------------|-----|
| my-cake        | loc        | http://my-cake.loc      |
| my-cake        | local      | http://my-cake.local    |
| my-cake        | dev        | http://my-cake.dev      |
| my-cake        | work.loc   | http://my-cake.work.loc |
| test           | foo        | http://test.foo         |
| project1       | bar        | http://project1.bar     |

The above examples should make it clear enough.

#### 3.2 Project path

| `.env` file variable name | Default | Note |
|---------------------------|---------|------|
| HOST_PATH_HTTPD_DATADIR   | `./data/www`   | Can be absolute or relative path. A relative path starts inside the devilbox git directory.<br/><strong>Note:</strong> Container need to be re-created after changing this value. |

This is the file system path on your host computer which will hold the Project Folders.

**IMPORTANT:** When changing this path, you must re-create any affected Docker container explicitly. This can be accomplished by doing the following:

```shell
# Stop the container
$ docker-compose stop

# Remove the stopped container (IMPORTANT!)
# After the removal it will be re-created during next run
$ docker-compose rm -f

# Start your stack
$ docker-compose up -d
```

#### 3.3 Project htdocs directory

| `.env` file variable name | Default | Note |
|---------------------------|---------|------|
| HTTPD_DOCROOT_DIR         | `htdocs` | A sub directory inside your project directory from which the web server will serve your files.<br/>The full path to the document root would be: `${HOST_PATH_HTTPD_DATADIR}/<project-name>/${HTTPD_DOCROOT_DIR}` |

If you rather would use a directory called `www`, just change it to this. Be aware that this will affect all your projects.

**Note:** When changing this value, you must restart the devilbox.

```shell
# Stop the container
$ docker-compose stop

# Start your stack
$ docker-compose up -d
```

## 4. Container settings

#### 4.1 General

##### 4.1.1 Timezone

| `.env` file variable name | Default | Note |
|---------------------------|---------|------|
| TIMEZONE                  | `Europe/Berlin`| Set timezone of the Docker container |

Use this variable to control the timezone of the container. 

**Note:** This currently only works on the devilbox self-provided container:

* DNS
* MySQL/MariaDB
* PHP/HHVM
* Apache/Nginx

The official containers do not support this setting.

##### 4.1.1 User id

| `.env` file variable name | Default | Note |
|---------------------------|---------|------|
| NEW_UID                   | `1000`| Change the Docker containers user id (uid) |

This setting is used to change the user id of the PHP/HHVM container to the one you specify. As you will work insisde the PHP/HHVM container, you will have to set it to the same user id of your Host computers user id. It will make sure that files created inside the container have the same access permissions as outside the container.

To find out your user id, type the following on your Host system:
```shell
$ id
```

**Note:** If your Host computers user id and the containers user id do not match, files will have different access rights inside and outside which might result in permission errors like `access denied`. So make sure to set this value.


##### 4.1.2 Group id

| `.env` file variable name | Default | Note |
|---------------------------|---------|------|
| NEW_GID                   | `1000`| Change the Docker containers group id (gid) |

This setting is used to change the group id of the PHP/HHVM container to the one you specify. As you will work inside the PHP/HHVM container, you will have to set it to the same group id of your Host computers group id. It will make sure that files created inside the container have the same access permissions as outside the container.

To find out your group id, type the following on your Host system:
```shell
$ id
```

**Note:** If your Host computers group id and the containers group id do not match, files will have different access rights inside and outside which might result in permission errors like `access denied`. So make sure to set this value.

**Note:** Files created by the webserver such as uploads, tmp and cache files are still created by the webservers group id and you will probably have to `chmod` them. This issues will be addressed shortly and you will also be able to change the uid/gid of the webserver in the next devilbox release.

#### 4.2 PHP / HHVM

##### 4.2.1 Select PHP version

| `.env` file variable name | Default | Note |
|---------------------------|---------|------|
| PHP_SERVER                | `php-fpm-7.0`| Change the PHP Docker container |

1. Open the `.env` file in your favorite editor
2. Find the `PHP_SERVER=` block

You can choose between different PHP versions and HHVM.

**Important:** Keep in mind that if you have a custom php.ini config at `./cfg/php-*/`, it is only effective for one version. Custom php configurations are separted per version.

##### 4.2.2 Xdebug

| `.env` file variable name | Default | Note |
|---------------------------|---------|------|
| PHP_XDEBUG_ENABLE         | `1` | Enable Xdebug for PHP |
| PHP_XDEBUG_REMOTE_PORT    | `9000` | PHP Xdebugs remote port - where your editor/IDE is receiving Xdebug data |
| PHP_XDEBUG_REMOTE_HOST    | `192.168.0.215` | PHP Xdebugs remote IP address - where your editor/IDE is listening for Xdebug connections (make sure to change this to the IP of your Docker Host) |

Xdebug is turned on by default and also using the official Xdebug default port. The only thing you will have to adjust is the Xdebug remote host address so that your editor/IDE can actually receive Xdebug data.

##### 4.2.3 php.ini

`php.ini` settings can be configured for each PHP/HHVM version separately. Container-based configuration is done inside the `./cfg/` directory.

```shell
$ ls -l ./cfg/ | grep -E 'php|hhvm'
drwxrwxr-x 2 cytopia 4096 Jun 21 08:47 hhvm-latest/
drwxrwxr-x 2 cytopia 4096 Jun 21 08:43 php-fpm-5.4/
drwxrwxr-x 2 cytopia 4096 Jun 21 08:43 php-fpm-5.5/
drwxrwxr-x 2 cytopia 4096 Jun 21 08:43 php-fpm-5.6/
drwxrwxr-x 2 cytopia 4096 Jun 21 08:44 php-fpm-7.0/
drwxrwxr-x 2 cytopia 4096 Jun 21 08:44 php-fpm-7.1/
drwxrwxr-x 2 cytopia 4096 Jun 21 08:44 php-fpm-7.2/
```

Each of the above folders will hold an example configuration file named `devilbox-custom.ini-example` which shows some example settings but will **not have** any effect yet. Only files ending by **`.ini`** will be sourced and applied, so you must copy it (or create a new file) to something that ends by `*.ini`.

In order to edit settings for PHP 5.6, go into that folder, copy the example file and adjust ist:

```shell
# Copy to file ending by *.ini
$ cd cfg/php-fpm-5.6
$ cp devilbox-custom.ini-example devilbox-custom.ini

# Edit settings
$ vi devilbox-custom.ini
```

Change will take effect after restarting the devilbox.

##### 4.2.4 HHVM

HHVM can just be configured as all other PHP versions. However it has a special option to change between **PHP-5.6** mode and **PHP-7** mode. This example is addressed in `cfg/hhvm-latest/devilbox.ini-example`.

```shell
$ cat cfg/hhvm-latest/devilbox.ini-example
```
```ini
; Use PHP 5.6 or PHP 7 mode for HHVM
; PHP 5.6 Mode
;hhvm.php7.all = 0
; PHP 7 Mode (default)
;hhvm.php7.all = 1
```

By default, HHVM is using **PHP-7** mode, you can change this setting to **PHP-5.6** by enabling `hhvm.php7.all = 0`.

**Note:** You must then also copy the file to something that ends by `*.ini`.

##### 4.2.5 Custom PHP modules

The devilbox supports to load custom PHP modules for each version without having to rebuild the PHP containers. There are only two things to be done in order to load them:

1. Copy your custom module to `mod/(hhvm|php-fpm)-<VERSION>/*.so`
2. Create a config file that loads the module in `cfg/(hhvm|php-fpm)-<VERSION>/*.ini`

As you will need to state the Path of the module to be loaded in your configuration file (`*.ini`), you will need to know where those modules are placed inside the docker container. Have a look at the following table to find out:

| Docker  | Module host path           | Path inside Docker container             |
|---------|----------------------------|------------------------------------------|
| PHP 5.4 | `mod/php-fpm-5.4/<mod>.so` | `/usr/lib64/php/custom-modules/<mod>.so` |
| PHP 5.5 | `mod/php-fpm-5.5/<mod>.so` | `/usr/lib64/php/custom-modules/<mod>.so` |
| PHP 5.6 | `mod/php-fpm-5.6/<mod>.so` | `/usr/lib64/php/custom-modules/<mod>.so` |
| PHP 7.0 | `mod/php-fpm-7.0/<mod>.so` | `/usr/lib64/php/custom-modules/<mod>.so` |
| PHP 7.1 | `mod/php-fpm-7.1/<mod>.so` | `/usr/lib64/php/custom-modules/<mod>.so` |
| PHP 7.2 | `mod/php-fpm-7.2/<mod>.so` | `/usr/lib64/php/custom-modules/<mod>.so` |
| HHVM    | `mod/hhvm-latest/<mod>.so` | `/usr/lib64/php/custom-modules/<mod>.so` |

As you can see from the above table, modules are always available in `/usr/lib64/php/custom-modules/` and you can just copy/paste your configuration files for each PHP version.

The following will show an example how to load [ioncube](https://www.ioncube.com) module for PHP 7.0:

1. Copy `ioncube_loader_lin_7.0.so` to `mod/php-fpm-7.0/ioncube_loader_lin_7.0.so`
2. Create custom config to enable ioncube in: `cfg/php-fpm-7.0/00-ioncube.ini`
```shell
zend_extension = /usr/lib64/php/custom-modules/ioncube_loader_lin_7.0.so
```

**Note:** PHP configuration files are loaded by file names in alphabetical order and the ioncube zend extension needs to be loaded before any other zend extension. This is the reason why its configuration file name starts with `00-`.

##### 4.2.6 Customize bash and other tools

The devilbox supports to load custom configuration files for your Docker containers bash. Put any file ending by `*.sh` into the `bash/` folder and they will automatically be sourced by your container's bash. It is also possible to add any other configuration files into that folder and start your app with the appended configuration path. To better understand how that works, have a look at the paths:

| Docker  | bash host path  | Path inside Docker container      |
|---------|----------------------------|------------------------|
| PHP 5.4 | `bash/<cfg>.sh` | `/etc/bashrc-devilbox.d/<cfg>.sh` |
| PHP 5.5 | `bash/<cfg>.sh` | `/etc/bashrc-devilbox.d/<cfg>.sh` |
| PHP 5.6 | `bash/<cfg>.sh` | `/etc/bashrc-devilbox.d/<cfg>.sh` |
| PHP 7.0 | `bash/<cfg>.sh` | `/etc/bashrc-devilbox.d/<cfg>.sh` |
| PHP 7.1 | `bash/<cfg>.sh` | `/etc/bashrc-devilbox.d/<cfg>.sh` |
| PHP 7.2 | `bash/<cfg>.sh` | `/etc/bashrc-devilbox.d/<cfg>.sh` |
| HHVM    | `bash/<cfg>.sh` | `/etc/bashrc-devilbox.d/<cfg>.sh` |

So lets assume you also want to change your vim configuration and have vim always use that specific config file during startup. This is achieved by placing the vim config file into that directory and create a bash alias, that always starts vim with that config file:

On your host system do the following:

```shell
# Create your vim config in the devilbox bash directory `my-vimrc`
$ vim bash/my-vimrc
```
```vim
" You vim
set encoding=utf-8              " The encoding displayed
set nocompatible                " Use vim defaults instead of vi defaults
set autoread                    " Automatically reload file contents when changed from outside
set backspace=indent,eol,start  " Allow backspacing over everything in insert mode
set hidden                      " Start new file with :e without having to save current
set history=50                  " Remember commands entered in :
set undolevels=100              " Use many levels of undo
set shell=$SHELL                " Set Shell
set more                        " to show pages using `more` in command outpouts
set title                       " show vim in terminal title
```

Now add a custom bash config and create an alias to always start vim with the above created config:
```shell
$ vim bash/my-bash.sh
```
```shell
alias vim='vim -u /etc/bashrc-devilbox.d/my-vimrc'
```

The next time you open `vim` within the PHP/HHVM docker container, it will automatically source your `my-vimrc`.

##### 4.2.7 Custom environment variables

If you projects require custom environment variables to be available to PHP or HHVM, you can simply add any variable to the `.env` file. All of them are automatically available to the container.

If your application requires are variable to determine if it is run under development or production, for example: `APPLICATION_ENV`, you can just add this to the `.env` file:

```
$ cat .env

...
APPLICATION_ENV=1
```


#### 4.3 Apache / Nginx

##### 4.3.1 Select Httpd version

| `.env` file variable name | Default | Note |
|---------------------------|---------|------|
| HTTPD_SERVER              | `nginx-stable`| Change the Httpd Docker container |

1. Open the `.env` file in your favorite editor
2. Find the `HTTPD_SERVER=` block

You can choose between Apache and Nginx in different version. All of them are configured to work the same, there is nothing to worry about when changing them.

##### 4.3.2 Host port

| `.env` file variable name | Default | Note |
|---------------------------|---------|------|
| HOST_PORT_HTTPD           | `80`| Host computer listening port for the webserver (Apache or Nginx) |

By default the webserver will listen on port 80 (on your Host computer). You can change this to any other port (in case port 80 is already taken).

If you also want to change the listening address (default: 127.0.0.1) to something else, see above or search this document for `LOCAL_LISTEN_ADDRESS`.

##### 4.3.3 Customize the vhost configuration

| `.env` file variable name | Default | Note |
|---------------------------|---------|------|
| HTTPD_TEMPLATE_DIR        | `.devilbox`| A sub directory inside your project directory where the web server will look for web server templates in order to load a custom configuration for this specific project. |

If no such directory exists or no templates are available, the chosen web server will use the standard configuration for this virtual host. You can however overwrite each virtual host with custom settings by adding **[vhost-gen](https://github.com/devilbox/vhost-gen)** templates.

Copy all available templates from [vhost-gen templates directory](https://github.com/devilbox/vhost-gen/tree/master/etc/templates) to a a sub directory in your projects dir (By default `.devilbox/`) and adjust those template files to your needs.


#### 4.4 MySQL

##### 4.4.1 Select MySQL version

| `.env` file variable name | Default | Note |
|---------------------------|---------|------|
| MYSQL_SERVER              | `mariadb-10.1`| Change the MySQL Docker container |

1. Open the `.env` file in your favorite editor
2. Find the `MYSQL_SERVER=` block

**Important:** Each version has a different data directory. This is a security precautions. Imagine you startup MySQL 5.5 for the first time. New databases will be created. Now you startup MySQL 8. All existing databases would be upgraded to work flawlessly with MySQL 8, however this is not downwards compatible. So by startup up MySQL 5.5 again, it would say the database is corrupt.

##### 4.4.2 Root password

| `.env` file variable name | Default | Note |
|---------------------------|---------|------|
| MYSQL_ROOT_PASSWORD | `` | Root user password for MySQL |

If you start a MySQL container for the first time, it will setup MySQL itself with the specified password. If you do change the root password to something else, make sure to also set it accordingly in `.env`, otherwise the devilbox will not be able to connect to MySQL and will not be able to display information inside the bundled intranet.

##### 4.4.3 General Log

| `.env` file variable name | Default | Note |
|---------------------------|---------|------|
| MYSQL_GENERAL_LOG | `1` | Turn on or off MySQL general query log |

https://dev.mysql.com/doc/refman/5.7/en/query-log.html:
> The general query log is a general record of what mysqld is doing. The server writes information to this log when clients connect or disconnect, and it logs each SQL statement received from clients. The general query log can be very useful when you suspect an error in a client and want to know exactly what the client sent to mysqld.

This logging behavior is turned on by default in order to provide all information during development. Set it to `0` to turn it off.

##### 4.4.4 Host port

| `.env` file variable name | Default | Note |
|---------------------------|---------|------|
| HOST_PORT_MYSQL           | `3306`| Host computer listening port for the MySQL server (MySQL or MariaDB) |

By default the MySQL server will listen on port 3306 (on your Host computer). You can change this to any other port (in case port 3306 is already taken).

If you also want to change the listening address (default: 127.0.0.1) to something else, see above or search this document for `LOCAL_LISTEN_ADDRESS`.

##### 4.4.5 Data path

| `.env` file variable name | Default | Note |
|---------------------------|---------|------|
| HOST_PATH_MYSQL_DATADIR   | `./data/mysql`   | Can be absolute or relative path. A relative path starts inside the devilbox git directory.<br/><strong>Note:</strong> Container need to be re-created after changing this value. |

This is the file system path on your host computer which will hold the MySQL data.

**Note:** A sub directory will be created inside this path for each MySQL version. This separation is there to make sure that higher versions do not upgrade the database irrevocably. (e.g.: MySQL 8.0 can read data from MySQL 5.5, but not the other way round).

The automatic folder structure will look something like this:

```shell
$ ls -l ./data/mysql/
drwxrwxr-x 6 48 48 4096 Jun 21 08:47 mariadb-10.0/
drwxrwxr-x 6 48 48 4096 Jun 21 08:47 mariadb-10.1/
drwxrwxr-x 6 48 48 4096 Jun 21 08:47 mariadb-10.2/
drwxrwxr-x 6 48 48 4096 Jun 21 08:47 mariadb-10.3/
drwxrwxr-x 6 48 48 4096 Jun 21 08:47 mysql-5.5/
drwxrwxr-x 6 48 48 4096 Jun 21 08:47 mysql-5.6/
drwxrwxr-x 6 48 48 4096 Jun 21 08:47 mysql-5.7/
drwxrwxr-x 6 48 48 4096 Jun 21 08:47 mysql-8.0/
drwxrwxr-x 6 48 48 4096 Jun 21 08:47 percona-5.5/
drwxrwxr-x 6 48 48 4096 Jun 21 08:47 percona-5.6/
drwxrwxr-x 6 48 48 4096 Jun 21 08:47 percona-5.7/
```

**IMPORTANT:** When changing this path, you must re-create any affected Docker container explicitly. This can be accomplished by doing the following:

```shell
# Stop the container
$ docker-compose stop

# Remove the stopped container (IMPORTANT!)
# After the removal it will be re-created during next run
$ docker-compose rm -f

# Start your stack
$ docker-compose up -d
```


##### 4.4.6 my.cnf

`my.cnf` settings can be configured for each MySQL/MariaDB version separately. Container-based configuration is done inside the `./cfg/` directory.

```shell
$ ls -l ./cfg/ | grep -E 'mysql|maria'
drwxrwxr-x 2 cytopia 4096 Jun  1 08:44 mariadb-10.0/
drwxrwxr-x 2 cytopia 4096 Jun  1 08:44 mariadb-10.1/
drwxrwxr-x 2 cytopia 4096 Jun  1 08:44 mariadb-10.2/
drwxrwxr-x 2 cytopia 4096 Jun  1 08:44 mariadb-10.3/
drwxrwxr-x 2 cytopia 4096 Jun 13 13:18 mysql-5.5/
drwxrwxr-x 2 cytopia 4096 Jun  1 08:44 mysql-5.6/
drwxrwxr-x 2 cytopia 4096 Jun  1 08:44 mysql-5.7/
drwxrwxr-x 2 cytopia 4096 Jun  1 08:44 mysql-8.0/
drwxrwxr-x 2 cytopia 4096 Jun  1 08:44 percona-5.5/
drwxrwxr-x 2 cytopia 4096 Jun  1 08:44 percona-5.6/
drwxrwxr-x 2 cytopia 4096 Jun  1 08:44 percona-5.7/
```

Each of the above folders will hold an example configuration file named `devilbox-custom.cnf-example` which shows some example settings but will **not have** any effect yet. Only files ending by **`.cnf`** will be sourced and applied, so you must copy it (or create a new file) to something that ends by `*.cnf`.

In order to edit settings for MySQL 5.5, go into that folder, copy the example file and adjust it:

```shell
# Copy to file ending by *.ini
$ cd cfg/mysql-5.5
$ cp devilbox-custom.cnf-example devilbox-custom.cnf

# Edit settings
$ vi devilbox-custom.cnf
```

Change will take effect after restarting the devilbox.

#### 4.5 PostgreSQL

##### 4.5.1 Select PostgreSQL version

| `.env` file variable name | Default | Note |
|---------------------------|---------|------|
| PGSQL_SERVER              | `9.6` | Change the PostgreSQL Docker container |

1. Open the `.env` file in your favorite editor
2. Find the `PGSQL_SERVER=` block

**Important:** Each version has a different data directory. This is a security precautions. Imagine you startup PostgreSQL 9.1 for the first time. New databases will be created. Now you startup PostgreSQL 9.6. All existing databases would be upgraded to work flawlessly with PostgreSQL 9.6, however this is not downwards compatible. So by startup up PostgreSQL 9.1 again, it would say the database is corrupt.

##### 4.5.2 Root user

| `.env` file variable name | Default | Note |
|---------------------------|---------|------|
| PGSQL_ROOT_USER | `postgres` | Root username for PostgreSQL |

If you start a PostgreSQL  container for the first time, it will setup PostgreSQL itself with a specified username and password. If you do change the root username or password to something else, make sure to also set it accordingly in `.env`, otherwise the devilbox will not be able to connect to PostgreSQL and will not be able to display information inside the bundled intranet.

See also: Root password

##### 4.5.3 Root password

| `.env` file variable name | Default | Note |
|---------------------------|---------|------|
| PGSQL_ROOT_PASSWORD | `` | Root user password for PostgreSQL |

If you start a PostgreSQL  container for the first time, it will setup PostgreSQL itself with a specified username and password. If you do change the root username or password to something else, make sure to also set it accordingly in `.env`, otherwise the devilbox will not be able to connect to PostgreSQL and will not be able to display information inside the bundled intranet.

See also: Root user

##### 4.5.4 Host port

| `.env` file variable name | Default | Note |
|---------------------------|---------|------|
| HOST_PORT_PGSQL           | `5432`| Host computer listening port for the PostgreSQL server |

By default the PostgreSQL server will listen on port 5432 (on your Host computer). You can change this to any other port (in case port 5432 is already taken).

If you also want to change the listening address (default: 127.0.0.1) to something else, see above or search this document for `LOCAL_LISTEN_ADDRESS`.

##### 4.5.5 Data path

| `.env` file variable name | Default | Note |
|---------------------------|---------|------|
| HOST_PATH_PGSQL_DATADIR   | `./data/pgsql`   | Can be absolute or relative path. A relative path starts inside the devilbox git directory.<br/><strong>Note:</strong> Container need to be re-created after changing this value. |

This is the file system path on your host computer which will hold the PostgreSQL data.

**Note:** A sub directory will be created inside this path for each PostgreSQL version. This separation is there to make sure that higher versions do not upgrade the database irrevocably. (e.g.: PostgreSQL 9.6 can read data from PostgreSQL 9.1, but maybe not the other way round).

The automatic folder structure will look something like this:

```shell
$ ls -l ./data/pgsql/
drwxrwxr-x 6 48 48 4096 Jun 21 08:47 9.1/
drwxrwxr-x 6 48 48 4096 Jun 21 08:47 9.2/
drwxrwxr-x 6 48 48 4096 Jun 21 08:47 9.3/
drwxrwxr-x 6 48 48 4096 Jun 21 08:47 9.4/
drwxrwxr-x 6 48 48 4096 Jun 21 08:47 9.5/
drwxrwxr-x 6 48 48 4096 Jun 21 08:47 9.6/
```

**IMPORTANT:** When changing this path, you must re-create any affected Docker container explicitly. This can be accomplished by doing the following:

```shell
# Stop the container
$ docker-compose stop

# Remove the stopped container (IMPORTANT!)
# After the removal it will be re-created during next run
$ docker-compose rm -f

# Start your stack
$ docker-compose up -d
```


#### 4.6 Redis

##### 4.6.1 Select Redis version

| `.env` file variable name | Default | Note |
|---------------------------|---------|------|
| REDIS_SERVER              | `3.2` | Change the Redis Docker container |

1. Open the `.env` file in your favorite editor
2. Find the `REDIS_SERVER=` block

There is nothing to pay attention to here.

##### 4.6.2 Host port

| `.env` file variable name | Default | Note |
|---------------------------|---------|------|
| HOST_PORT_REDIS           | `6379`| Host computer listening port for the Redis server |

By default the Redis server will listen on port 6379 (on your Host computer). You can change this to any other port (in case port 6379 is already taken).

If you also want to change the listening address (default: 127.0.0.1) to something else, see above or search this document for `LOCAL_LISTEN_ADDRESS`.

#### 4.7 Memcached

##### 4.7.1 Select Memcached version

| `.env` file variable name | Default | Note |
|---------------------------|---------|------|
| MEMCD_SERVER              | `1.4.21` | Change the Memcached Docker container |

1. Open the `.env` file in your favorite editor
2. Find the `MEMCD_SERVER=` block

There is nothing to pay attention to here.

##### 4.7.2 Host port

| `.env` file variable name | Default | Note |
|---------------------------|---------|------|
| HOST_PORT_MEMCD           | `11211`| Host computer listening port for the Memcached server |

By default the Memcached server will listen on port 11211 (on your Host computer). You can change this to any other port (in case port 11211 is already taken).

If you also want to change the listening address (default: 127.0.0.1) to something else, see above or search this document for `LOCAL_LISTEN_ADDRESS`.

#### 4.8 MongoDB

##### 4.8.1 Select MongoDB version

| `.env` file variable name | Default | Note |
|---------------------------|---------|------|
| MONGO_SERVER              | `3.4` | Change the MongoDB Docker container |

1. Open the `.env` file in your favorite editor
2. Find the `MONGO_SERVER=` block

**Important:** Each version has a different data directory. This is a security precautions. Imagine you startup MongoDB 2.8 for the first time. New databases will be created. Now you startup MongoDB 3.5. All existing databases would be upgraded to work flawlessly with MongoDB 3.5, however this is not downwards compatible. So by startup up MongoDB 2.8 again, it would say the database is corrupt.

##### 4.8.2 Host port

| `.env` file variable name | Default | Note |
|---------------------------|---------|------|
| HOST_PORT_MONGO           | `27017`| Host computer listening port for the MongoDB server |

By default the Memcached server will listen on port 27017 (on your Host computer). You can change this to any other port (in case port 27017 is already taken).

If you also want to change the listening address (default: 127.0.0.1) to something else, see above or search this document for `LOCAL_LISTEN_ADDRESS`.

##### 4.8.3 Data path

| `.env` file variable name | Default | Note |
|---------------------------|---------|------|
| HOST_PATH_MONGO_DATADIR   | `./data/mongo`   | Can be absolute or relative path. A relative path starts inside the devilbox git directory.<br/><strong>Note:</strong> Container need to be re-created after changing this value. |

This is the file system path on your host computer which will hold the MongoDB data.

**Note:** A sub directory will be created inside this path for each MongoDB version. This separation is there to make sure that higher versions do not upgrade the database irrevocably. (e.g.: MongoDB 3.5 can read data from MongoDB 2.8, but maybe not the other way round).

The automatic folder structure will look something like this:

```shell
$ ls -l ./data/mongo/
drwxrwxr-x 6 48 48 4096 Jun 21 08:47 2.8/
drwxrwxr-x 6 48 48 4096 Jun 21 08:47 3.0/
drwxrwxr-x 6 48 48 4096 Jun 21 08:47 3.2/
drwxrwxr-x 6 48 48 4096 Jun 21 08:47 3.4/
drwxrwxr-x 6 48 48 4096 Jun 21 08:47 3.5/
```

**IMPORTANT:** When changing this path, you must re-create any affected Docker container explicitly. This can be accomplished by doing the following:

```shell
# Stop the container
$ docker-compose stop

# Remove the stopped container (IMPORTANT!)
# After the removal it will be re-created during next run
$ docker-compose rm -f

# Start your stack
$ docker-compose up -d
```


#### 4.9 Bind

##### 4.9.1 Upstream resolver

| `.env` file variable name | Default | Note |
|---------------------------|---------|------|
| BIND_DNS_RESOLVER         | `8.8.8.8,8.8.4.4`   | Comma separated list of IP addresses of DNS servers. By default using Google's DNS server as they are pretty fast. |

The devilbox is using its own DNS server internally (your host computer can also use it for Auto-DNS) in order to resolve custom project domains defined by `TLD_SUFFIX`. To also be able to reach the internet from within the Container there must be some kind of upstream DNS server to ask for queries.

If you don't trust the Google DNS server, then set it to something else. If you already have a DNS server inside your LAN and also want your custom DNS (if any) to be available inside the containers, set the value to its IP address.

##### 4.9.2 Host port

| `.env` file variable name | Default | Note |
|---------------------------|---------|------|
| HOST_PORT_BIND           | `1053`| Host computer listening port for the Bind DNS server |

By default the Bind DNS server will listen on port 1053 (on your Host computer). You can change this to any other port (in case port 1053 is already taken).

If you also want to change the listening address (default: 127.0.0.1) to something else, see above or search this document for `LOCAL_LISTEN_ADDRESS`.


## 5. Intranet settings

#### 5.1 DNS check timeout

| `.env` file variable name | Default | Note |
|---------------------------|---------|------|
| DNS_CHECK_TIMEOUT         | `1`     | DNS timeout in seconds for unresolvable Domains |

`TLD_SUFFIX` domains are checked if they are set in the host computer /etc/hosts or available via attached DNS server. Timeout is done on vhosts.php (intranet) via ajax calls. In order to keep performance, set this to a low value. DNS checks might not succeed in time on slow machines. If DNS is valid, but timeout is expired, set this to a higher value.

`DNS_CHECK_TIMEOUT` value is how many seconds to time out.

#### 5.2 Password protection

| `.env` file variable name | Default | Note |
|---------------------------|---------|------|
| DEVILBOX_UI_PROTECT       | `0`     | Enable or disable devilbox's Intranet password protection |
| DEVILBOX_UI_PASSWORD      | ``      | Choose the password to login. (Default username: `devilbox`) |

If you wish, you can password-protect the devilbox intranet (not the vhost projects).

> You could for example allow people from your local network to access the projects hosted on your devilbox (When changing `LOCAL_LISTEN_ADDRESS` to also listen on your external LAN side).
> However, the devilbox intranet might give away too much information, what other people should not see, such as databases or others.
> In that case, you should enable password protection.

#### 5.3 Disable Intranet

| `.env` file variable name | Default | Note |
|---------------------------|---------|------|
| DEVILBOX_UI_DISABLE       | `0`     | Enable or disable devilbox's Intranet completely. |

If you wish, you can disable the built-in intranet completely. Set this variable to `1` which will remove the vhost that servers the Intranet. All that will be left are your custom project based virtual hosts.


## 6. Host computer

#### 6.1 /etc/hosts

In the `/etc/hosts` file you will have to configure your project DNS.

Each project is available in your browser via `http://<project-folder>.<TLD_SUFFIX>`. However your Host computer is not aware of this DNS name by default. In order to tell it to what IP address the domain should be resolved, you will need to make one entry per project in the `/etc/hosts` file on your host computer.

These entries typically look like this:

```shell
$ cat /etc/hosts

127.0.0.1 localhost
```

If you have set `TLD_SUFFIX` to `loc` and your project folder is named `my-project`, you will need to add the following entry:
```
127.0.0.1 my-project.loc
```

As you can see, the following general structure for all project applies:
```
<LOCAL_LISTEN_ADDRESS>   <project-folder>.<TLD_SUFFIX>
```

See **Project settings** for more detail.


**Note:** On Windows the file can be found under: `C:\Windows\System32\drivers\etc`


If you do not want to create this entry everytime for a new project, skip to the next section to see how to setup Auto-DNS.

#### 6.2 Auto-DNS

The devilbox provides its own DNS nameserver that automatically configures itself based on the values of `TLD_SUFFIX`. That means each `<project-folder>.<TLD_SUFFIX>` domain will always point to `127.0.0.1`.

You can make advantage of that by adding this DNS nameserver to your host computers DNS config in `/etc/resolv.conf`. Then everytime you make a request (in your Browser for example) to a devilbox project domain, the bundled DNS name server will successfully answer the request and direct you to the project. This will remove the need to make custom `/etc/hosts` entries for each project.


**What is required for this to work?**

By default, the Bind port (`.env`: `HOST_PORT_BIND=1053`) is set to a non standard DNS port in order to avoid failed devilbox startups in case you already have a local DNS resolver running. So edit your `.env` file and set the Bind port to `53`:
```
$ vi .env

HOST_PORT_BIND=53
```

Then you need to make sure that nothing on your host computer is listening on port 53. Do this before starting up the devilbox.

```shell
$ netstat -tuln | grep ':53'
```

If there is already something listening on that port, you will need to stop whatever is listening in port 53 (TCP and UDP).

If nothing is listening anymore on port 53, edit your `/etc/resolv.conf` and add the following line above all other `nameserver` entries:

```
nameserver 127.0.0.1
```

**Note:** Keep all other `nameserver` entries intact. The devilbox DNS nameserver will only work once the devilbox is running. If you remove all others and don't run the devilbox, you won't be able to resolve any DNS names anymore.

Afterwards `/etc/resolv.conf` will look similar to this:
```
# Dynamic resolv.conf(5) file for glibc resolver(3) generated by resolvconf(8)
#     DO NOT EDIT THIS FILE BY HAND -- YOUR CHANGES WILL BE OVERWRITTEN
nameserver 127.0.0.1
nameserver 192.168.0.10
search local
```

**Note:** This section needs to be improved with more detail. If anything is unclear for now, drop an issue at Github.

#### 6.2 For This to Work on Macs

Mac does not have Docker support for bridged network.
Therefore, you need to set the WILDCARD_ADDRESS in docker-compose.yml to 127.0.0.1 .
Kind of like here: https://github.com/cytopia/devilbox/pull/138/files

Afterwards, modifying resolv.conf does not work on Macs either, so edit using the Network tool in System Preferences instead.

System Preferences
Network
Click on what you use for your internet.
Click Advanced...
Click DNS
Add 127.0.0.1 along with your default DNS server.
