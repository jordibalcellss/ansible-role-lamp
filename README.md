# ansible-role-lamp

An Ansible role that installs and configures an [Apache](https://apache.org/),
[PHP](https://www.php.net/) and [MariaDB](https://mariadb.org/) (LAMP)
stack on CentOS.

## Features

Each component can be independently configured thanks to separated task files.

### Apache

The virtual hosts are placed in to separate directories. Each one will contain
two subdirectories:

* `html` for the public documents
* `log` for the server logs

The path to the document root parent and its subdirectories is established by
the variable `apache_document`. The role default variables produce a directory
tree like so:

```
/var/www
│
├── artemis.example
│   ├── html
│   │   ├── index.php
│   └── log
│       ├── error.log
│       └── requests.log
│
└── helios.example
    ├── html
    │   ├── index.php
    └── log
        ├── error.log
        └── requests.log
```

`apache_vhosts` defines the plain HTTP host(s) to be created. At the moment,
only `servername` is required to define them.

`apache_vhosts_ssl` defines the SSL-enabled vhost(s). They require
`servername`, `ssl_cert_path` and `ssl_cert_key_path`. The SSL certificate can
be created setting

```yaml
apache_ssl_cert_generate: yes
```

and defining the subject properties editing the `apache_ssl_cert_*` group of
variables.

Each SSL virtual host will be provided with a plain vhost permanently
redirected to the SSL counterpart. The plain vhost definition share its own
file with its corresponding SSL `VirtualHost` block under `apache_conf_path`,
so it becomes easier to manage them.

If no plain HTTP hosts are required (or the other way around) the array can
simply be declared empty

```yaml
apache_vhosts: []
```

### PHP

The packages to be installed are also defined per-distribution via `vars/`. The
interpreter version can be selected in `php_version`.

### MariaDB

The relational database engine will be given accounts as defined by the
`mariadb_users` array provided that `mariadb_users_create` allows it.

In RHEL/CentOS deployments, the playbook will tweak the necessary
SELinux contexts and selectors.

## Example playbook

```yaml
---
- hosts: webservers
  tasks:

  - import_role:
      name: ansible-role-lamp
      vars_from: centos.yml

```

## License

This project is licensed under the MIT license - see the LICENSE file for
details.
