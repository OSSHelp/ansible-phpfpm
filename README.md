# php-fpm

[![Build Status](https://drone.osshelp.ru/api/badges/ansible/php-fpm/status.svg)](https://drone.osshelp.ru/ansible/php-fpm)

Role for Ansible, which installs php-fpm, php modules, generate configs from Ubuntu official repo or from ppa

## Usage (example)

### Common example

```yaml
    - role: php-fpm
      phpfpm_modules:
        - bz2
        - mysql
      phpfpm_php_params:
        "output_buffering": "4096"
      phpfpm_pools:
        - name: test1
          user: nobody
          group: nogroup
          request_slowlog_timeout: 2
          php_params:
            - {name: sendmail_path, value: '/usr/sbin/sendmail -t -i -f noreply@test.com'}
            - {name: log_errors, admin: true, type: flag, value: 'on'}
            - {name: error_log, admin: true, value: /var/log/php/test_error.log}
        - name: test2
          user: nobody
          group: nogroup
          listen: 9000
          max_children: 20
          start_servers: 3
          min_spare_servers: 3
          max_spare_servers: 10
```

### Installation from ppa

```yaml
    - role: php-fpm
      phpfpm_version: ppa-5.6
      phpfpm_modules: [ ... ]
      phpfpm_php_params: { ... }
      phpfpm_pools: [ ... ]
```

### Configure only

```yaml
    - role: php-fpm
      phpfpm_version: ppa-5.6
      phpfpm_setup: configure
      phpfpm_php_params: { ... }
      phpfpm_pools: [ ... ]
```

## Available parameters

### Main

| Param | Default | Description |
| -------- | -------- | -------- |
| `php-fpm_setup` | `full` | Setup mode. See [OSSHelp KB article](https://oss.help/kb4895) |
| `phpfpm_version` | `default` | See version list bellow |
| `phpfpm_modules` | `[]` | List of PHP modules |
| `phpfpm_php_params` | `{}` | List of global PHP parameters |
| `phpfpm_pools` | `[]` | List of php-fpm pools |
| `phpfpm_disable_pools` | `false` | If set to `true`, pools will be created in `fpm/pool.disabled` directory. Make sure to add proper post-deploy tests. |

### Pool parameters

| Param | Default | Description |
| -------- | -------- | -------- |
| `name` | - | Pool name |
| `user` | - | Pool user |
| `listen` | `'/run/php/php' ~ phpfpm_version_number ~ '-fpm_' ~ pool.name ~ '.sock'` | Listen. Unix socket path or tcp addr:port |
| `listen_owner` | `www-data` | Listen owner. For unix socket only |
| `listen_group` | `www-data` | Listen group. For unix socket only |
| `pm` | `dynamic` | Child processes management |

All supported options you can see in [pool template](templates/pool.j2).
More information about parameters in [official pool example](https://github.com/php/php-src/blob/master/sapi/fpm/www.conf.in).

### Misc

These parameters shouldn't be changed.

| Param | Default | Description |
| -------- | -------- | -------- |
| `phpfpm_ppa` | `ppa:ondrej/php` | PHP-FPM ppa |
| `phpfpm_service`| `php{{ phpfpm_version_number }}-fpm` | PHP-FPM service name |
| `phpfpm_conf_dir` | `/etc/php/{{ phpfpm_version_number }}` | PHP-FPM conf directory |
| `phpfpm_pools_dir` | `{{ phpfpm_conf_dir }}/fpm/pool.d` | PHP-FPM pools directory |
| `phpfpm_php_log_dir` | `/var/log/php` | PHP logs directory |
| `phpfpm_mail_log_path` | `{{ phpfpm_php_log_dir }}/mail.log` | PHP mail() function log file |

Other parameters [here](default/main.yml).

## Available versions

- default
- ppa-5.6
- ppa-7.0
- ppa-7.1
- ppa-7.2
- ppa-7.3
- ppa-7.4
- ppa-8.0

## FAQ

...

## Useful links

- [Official documentation](https://www.php.net/manual/en/index.php)
- [PPA](https://launchpad.net/~ondrej/+archive/ubuntu/php)
- [OSSHelp Articles](https://rm.osshelp.ru/projects/support-servers/search?utf8=%E2%9C%93&q=php-fpm&scope=&all_words=&titles_only=&titles_only=1&kb_articles=1&attachments=0&options=0&commit=%D0%9F%D1%80%D0%B8%D0%BD%D1%8F%D1%82%D1%8C)

## TODO

- enable/disable modules functional
- move default pool parameters from pool.j2 to defaults
- ExecStartPre - create logdir and log files

## License

GPL3

## Author

OSSHelp Team, see <https://oss.help>
