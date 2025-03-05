# ansible-role-lamp

An Ansible role that installs and configures an [Apache](https://apache.org/),
[PHP](https://www.php.net/) and [MariaDB](https://mariadb.org/) (LAMP)
stack on RHEL.

## Features

Each component can be independently configured by means of separated task
files.

### Apache

The virtual hosts are placed in to separate directories. Each one will contain
two subdirectories:

* `html` for the public documents
* `log` for the server logs

The path to the document root parent and its subdirectories is established by
the variable `apache_document`. Role's default variables generate a directory
tree like so:

```
/var/www
│
├── artemis.example
│   ├── html
│   │   ├── index.html
│   └── log
│       ├── error.log
│       └── requests.log
│
└── helios.example
    ├── html
    │   ├── index.html
    └── log
        ├── error.log
        └── requests.log
```

`apache_vhosts` defines the plain HTTP host(s) to be created. These are the
dictionary entries available for configuring such hosts:

| Name | Type | Required |
| - | - | - |
| server_name | `string` | yes |
| server_alias | `string` | no |

`apache_vhosts_ssl` defines the SSL-enabled vhost(s).

| Name | Type | Required |
| - | - | - |
| server_name | `string` | yes |
| server_alias | `string` | no |
| ssl_cert_filename | `string` | yes |
| ssl_cert_transfer | `boolean` | yes |

If `ssl_cert_transfer: true`, the certificate and key found in `files/`
(matching `ssl_cert_filename` variable plus .crt/.key) will be copied to the
server.

If false, a self-signed certificate will be created. Its subject properties
are defined after the `apache_ssl_cert_*` group of variables.

Each SSL virtual host will be paired with a plain vhost permanently
redirected to the SSL counterpart. The plain vhost definition share its own
file with the corresponding SSL `VirtualHost` block under `apache_conf_path`,
so they become easier to manage.

If no plain HTTP hosts are required (or the other way around) the array can
simply be declared empty:

```yaml
apache_vhosts: []
```

### PHP

The packages to be installed are defined in `vars/`. The interpreter version
can be overridden in `php_version`.

### MariaDB

The relational database engine will be given accounts as defined by the
`mariadb_users` array provided that `mariadb_users_create` allows it.

## Compatibility

Tested under AlmaLinux 8 and 9.

The playbook will handle the necessary SELinux security contexts.

## Example playbook

```yaml
---
- hosts: webservers
  tasks:
  - import_role:
      name: ansible-role-lamp
```

## License

This project is licensed under the MIT license - see the LICENSE file for
details.
