[![Ansible Galaxy](https://raw.githubusercontent.com/roles-ansible/ansible_role_gitea/main/.github/galaxy.svg?sanitize=true)](https://galaxy.ansible.com/do1jlr/gitea) [![MIT License](https://raw.githubusercontent.com/roles-ansible/ansible_role_gitea/main/.github/license.svg?sanitize=true)](https://github.com/roles-ansible/ansible_role_gitea/blob/main/LICENSE)

 ansible role gitea
===================

This role installs and manages [gitea](https://gitea.io) - Git with a cup of tea. A painless self-hosted Git service. Gitea is a community managed lightweight code hosting solution written in Go.
[Source code & screenshots](https://github.com/go-gitea/gitea).

## Sample example of use in a playbook

The following code has been tested with Debian 8, it should work on Ubuntu as well.

```yaml
- name: "Install gitea"
  hosts: all
  vars:
    gitea_user: "gitea"
    gitea_home: "/var/lib/gitea"
    # To limit your users to 30 repos
    gitea_user_repo_limit: 30
    # Don't use a public CDN for frontend assets
    gitea_offline_mode: true

    # Some 'rendering' options for your URLs
    gitea_http_domain: git.yourdomain.fr
    gitea_root_url: https://git.yourdomain.fr

    # Here we assume we are behind a reverse proxy that will
    # handle https for us, so we bind on localhost:3000 using HTTP
    gitea_protocol: http
    gitea_http_listen: 127.0.0.1
    gitea_http_port: 3000

    # SSH server configuration
    gitea_ssh_listen: 0.0.0.0
    gitea_ssh_port: 2222
    # For URLs rendering again
    gitea_ssh_domain: git.yourdomain.fr
    gitea_start_ssh: true

    gitea_secret_key: 3sp00ky5me
    gitea_disable_gravatar: true
    # To make at least your first user register
    gitea_disable_registration: false
    gitea_require_signin: true
    gitea_enable_captcha: true

    gitea_show_user_email: false
  roles:
    - gitea
```

 Variables
-----------
Here is a deeper insight into the variables of this gitea role. For the exact function of some variables and the possibility to add more options we recommend a look at the config cheat sheet.  For the exact function of some variables and the possibility to add more options we recommend a look at this [config cheat sheet](https://docs.gitea.io/en-us/config-cheat-sheet/).

### gitea version
| variable name | default value | description |
| ------------- | ------------- | ----------- |
| `gitea_version` | *(see [defaults/main.yml](defaults/main.yml#L3))* | The gitea version this role shoud install |
| `gitea_version_check` | `true` | Check if installed version != `gitea_version` before initiating binary download |
| `gitea_dl_url` | *(see [defaults/main.yml](defaults/main.yml#L5))* | The path from where this role downloads the gitea binary |
| `gitea_gpg_key` | `7C9E68152594688862D62AF62D9AE806EC1592E2` | the gpg key the gitea binary is signed with |
| `gitea_gpg_server` | `hkp://keyserver.ubuntu.com:80` | A gpg key server where this role can download the gpg key |
| `gitea_backup_on_upgrade` | `false` | Optionally a backup can be created with every update of gitea. |
| `gitea_backup_location` | `{{ gitea_home }}/backups/` | Where to store the gitea backup if one is created with this role. |
| `submodules_versioncheck` | `false` | a simple version check that can prevent you from accidentally running an older version of this role. *(recomended)* |

### gitea in the linux world
| variable name | default value | description |
| ------------- | ------------- | ----------- |
| `gitea_group` | `gitea` | UNIX group used by Gitea |
| `gitea_home` | `/var/lib/gitea` | Base directory to work |
| `gitea_shell` | `/bin/false` | UNIX shell used by gitea. Set it to `/bin/bash` if you don't use the gitea built-in ssh server. |
| `gitea_systemd_cap_net_bind_service` | `false` | Adds `AmbientCapabilities=CAP_NET_BIND_SERVICE` to systemd service file |

### Overall ([DEFAULT](https://docs.gitea.io/en-us/config-cheat-sheet/#overall-default))
| variable name | default value | description |
| ------------- | ------------- | ----------- |
| `gitea_app_name` | `Gitea` | Displayed application name |
| `gitea_user` | `gitea ` | UNIX user used by Gitea |
| `gitea_run_mode`| `prod`| Application run mode, affects performance and debugging. Either “dev”, “prod” or “test”. |

### Repository ([repository](https://docs.gitea.io/en-us/config-cheat-sheet/#repository-repository))
| variable name | default value | description |
| ------------- | ------------- | ----------- |
| `gitea_repository_root` | `{{ gitea_home }}/repos` |  Root path for storing all repository data. It must be an absolute path. |
| `gitea_force_private` | `false` | Force every new repository to be private. |
| `gitea_user_repo_limit` | `-1` | Limit how many repos a user can have *(`-1` for unlimited)* |
| `gitea_disable_http_git` | `false` | Disable the ability to interact with repositories over the HTTP protocol. (true/false) |
| `gitea_default_branch` | `main` | Default branch name of all repositories. |
| `gitea_repository_extra_config` | `''` | you can use this variable to pass additional config parameters in the `[repository]` section of the config. |

### UI ([ui](https://docs.gitea.io/en-us/config-cheat-sheet/#ui-ui))
| variable name | default value | description |
| ------------- | ------------- | ----------- |
| `gitea_show_user_email` | `false` | Do you want to display email addresses ? (true/false) |
| `gitea_theme_default` | `gitea` | Default theme |
| `gitea_themes` | `gitea,arc-green` | List of enabled themes |
| `gitea_ui_extra_config` | `''` | you can use this variable to pass additional config parameters in the `[ui]` section of the config. |

### Server ([server](https://docs.gitea.io/en-us/config-cheat-sheet/#server-server))
| variable name | default value | description |
| ------------- | ------------- | ----------- |
| `gitea_protocol`| `http` | Listening protocol [http, https, fcgi, unix, fcgi+unix] |
| `gitea_http_domain` | `localhost` | Domain name of this server. Set it to the FQDN where you can reach your gitea server |
| `gitea_root_url` | `http://localhost:3000` | Root URL used to access your web app (full URL) |
| `gitea_http_listen` | `127.0.0.1` | HTTP listen address |
| `gitea_http_port` | `3000` | Bind port *(redirect from `80` will be activated if value is `443`)* |
| `gitea_http_letsencrypt_mail` | `undefined` | Enable Let`s Encrypt if a email address is given |
| `gitea_start_ssh` | `true` | When enabled, use the built-in SSH server. |
| `gitea_ssh_domain` | `{{ gitea_http_domain ` |  Domain name of this server, used for displayed clone URL |
| `gitea_ssh_port` | `2222` | SSH port displayed in clone URL. |
| `gitea_ssh_listen` | `0.0.0.0` | Listen address for the built-in SSH server. |
| `gitea_offline_mode` | `true` | Disables use of CDN for static files and Gravatar for profile pictures. (true/false) |
| `gitea_lfs_enabled` | `false` | Enable GIT-LFS Support *(git large file storage: [git-lfs](https://git-lfs.github.com/))*. |
| `gitea_lfs_content_path` | `{{ gitea_home }}/data/lfs` |  LFS content path. *(if it is on local storage.)* |
| `gitea_lfs_jwt_secret` | `''` | LFS authentication secret. Can be generated with ``gitea generate secret JWT_SECRET``. Will be autogenerated if not defined |
| `gitea_server_extra_config` | `''` | you can use this variable to pass additional config parameters in the `[ui]` section of the config. |

### Database ([database](https://docs.gitea.io/en-us/config-cheat-sheet/#database-database))
| variable name | default value | description |
| ------------- | ------------- | ----------- |
| `gitea_db_type` | `sqlite3` | The database type in use `[mysql, postgres, mssql, sqlite3]`. |
| `gitea_db_host` | `127.0.0.0:3306` | Database host address and port or absolute path for unix socket [mysql, postgres] (ex: `/var/run/mysqld/mysqld.sock`). |
| `gitea_db_name` | `root` | Database name |
| `gitea_db_user` | `gitea` | Database username |
| `gitea_db_password` | `lel` | Database password. **PLEASE CHANGE** |
| `gitea_db_ssl` | `disable` | Configure SSL only if your database type supports it. Have a look into the [config-cheat-sheet](https://docs.gitea.io/en-us/config-cheat-sheet/#database-database) for more detailed information |
| `gitea_db_path` | `{{ gitea_home }}/data/gitea.db` | DB path, if you use `sqlite3`. |
| `gitea_db_log_sql` | `false` | Log the executed SQL. |
| `gitea_database_extra_config` | `''` | you can use this variable to pass additional config parameters in the `[database]` section of the config. |

### Indexer ([indexer](https://docs.gitea.io/en-us/config-cheat-sheet/#indexer-indexer))
| variable name | default value | description |
| ------------- | ------------- | ----------- |
| `gitea_repo_indexer_enabled` | `false` | Enables code search *(uses a lot of disk space, about 6 times more than the repository size).* |
| `gitea_repo_indexer_include` | `''` |Glob patterns to include in the index *(comma-separated list)*. An empty list means include all files. |
| `gitea_repo_indexer_exclude` | `''` | Glob patterns to exclude from the index (comma-separated list). |
| `gitea_repo_exclude_vendored` | `true` | Exclude vendored files from index. |
| `gitea_repo_indexer_max_file_size` | `1048576` | Maximum size in bytes of files to be indexed. |
| `gitea_indexer_extra_config` | `''` | you can use this variable to pass additional config parameters in the `[indexer]` section of the config. |

### Security ([security](https://docs.gitea.io/en-us/config-cheat-sheet/#security-security))
| variable name | default value | description |
| ------------- | ------------- | ----------- |
| `gitea_secret_key` | `''` | Global secret key. Will be autogenerated if not defined. Should be unique. |
| `gitea_internal_token` | `''` | Internal API token. Will be autogenerated if not defined. Should be unique. |
| `gitea_disable_git_hooks` | `true` | Set to false to enable users with git hook privilege to create custom git hooks. Can be dangerous. |
| `gitea_password_check_pwn` | `false` | Check [HaveIBeenPwned](https://haveibeenpwned.com/Passwords) to see if a password has been exposed. |
| `gitea_security_extra_config` | `''` | you can use this variable to pass additional config parameters in the `[security]` section of the config. |

### Service ([service](https://docs.gitea.io/en-us/config-cheat-sheet/#service-service))
| variable name | default value | description |
| ------------- | ------------- | ----------- |
| `gitea_disable_registration` | `false` | Do you want to disable user registration? (true/false) |
| `gitea_register_email_confirm` | `false` | Enable this to ask for mail confirmation of registration. Requires `gitea_mailer_enabled` to be enabled. |
| `gitea_require_signin` | `true` | Do you require a signin to see repo's (even public ones)? (true/false)|
| `gitea_enable_captcha` | `true` | Do you want to enable captcha's ? (true/false)|
| `gitea_show_registration_button` | `true` | Here you can hide the registration button. This will not disable registration! (true/false)|
| `gitea_only_allow_external_registration` | `false` | Set to true to force registration only using third-party services (true/false) |
| `gitea_enable_notify_mail` | `false` | Enable this to send e-mail to watchers of a repository when something happens, like creating issues (true/false) |
| `gitea_auto_watch_new_repos` | `true` | Enable this to let all organisation users watch new repos when they are created (true/false) |
| `gitea_service_extra_config` | `''` | you can use this variable to pass additional config parameters in the `[service]` section of the config. |

### Mailer ([mailer](https://docs.gitea.io/en-us/config-cheat-sheet/#mailer-mailer))
| variable name | default value | description |
| ------------- | ------------- | ----------- |
| `gitea_mailer_enabled` | `false` | Whether to enable the mailer. |
| `gitea_mailer_host` | `localhost:25` | SMTP server hostname and port |
| `gitea_mailer_skip_verify` | `false` | Skip SMTP TLS certificate verification (true/false) |
| `gitea_mailer_tls_enabled` | `true` | Forcibly use TLS to connect even if not on a default SMTPS port.  |
| `gitea_mailer_from` | `noreply@{{ gitea_http_domain }}` | Mail from address, RFC 5322. This can be just an email address, or the “Name” <email@example.com> format. |
| `gitea_mailer_user` | `''` | Username of mailing user *(usually the sender’s e-mail address)*. |
| `gitea_mailer_password` | `''` | SMTP server password |
| `gitea_subject_prefix` | `''` | Prefix to be placed before e-mail subject lines |
| `gitea_mailer_type` | `smtp` |  `[smtp, sendmail, dummy]` |
| `gitea_mailer_extra_config` | `''` | you can use this variable to pass additional config parameters in the `[mailer]` section of the config. |

### Session ([session](https://docs.gitea.io/en-us/config-cheat-sheet/#session-session))
| variable name | default value | description |
| ------------- | ------------- | ----------- |
| `gitea_session_provider` | `file` | Session engine provider |
| `gitea_session_extra_config` | `''` | you can use this variable to pass additional config parameters in the `[session]` section of the config. |

### Picture ([picture](https://docs.gitea.io/en-us/config-cheat-sheet/#picture-picture))
| variable name | default value | description |
| ------------- | ------------- | ----------- |
| `gitea_disable_gravatar` | `true` | Do you want to disable Gravatar ? (privacy and so on) (true/false) |
| `gitea_picture_extra_config` | `''` | you can use this variable to pass additional config parameters in the `[picture]` section of the config. |

### Issue and pull request attachments ([attachment](https://docs.gitea.io/en-us/config-cheat-sheet/#issue-and-pull-request-attachments-attachment))
| variable name | default value | description |
| ------------- | ------------- | ----------- |
| `attachment_enabled` | `true` | Whether issue and pull request attachments are enabled. |
| `gitea_attachment_extra_config` | `''` | you can use this variable to pass additional config parameters in the `[attachment]` section of the config. |

### Log ([log](https://docs.gitea.io/en-us/config-cheat-sheet/#log-log))
| variable name | default value | description |
| ------------- | ------------- | ----------- |
| `gitea_log_systemd` | `false` | Disable logging into `file`, use systemd-journald |
| `gitea_log_level` | `Warn` | General log level. `[Trace, Debug, Info, Warn, Error, Critical, Fatal, None]` |
| `gitea_log_extra_config` | `''` | you can use this variable to pass additional config parameters in the `[log]` section of the config. |

### Metrics ([metrics](https://docs.gitea.io/en-us/config-cheat-sheet/#metrics-metrics))
| variable name | default value | description |
| ------------- | ------------- | ----------- |
| `gitea_metrics_enabled`| `false` | Enable the metrics endpoint |
| `gitea_metrics_token`| `''` | Bearer token for the Prometheus scrape job |

### OAuth2 ([oauth2](https://docs.gitea.io/en-us/config-cheat-sheet/#oauth2-oauth2))
| variable name | default value | description |
| ------------- | ------------- | ----------- |
| `gitea_oauth2_enabled` | `true` | Enable the Oauth2 provider (true/false) |
| `gitea_oauth2_jwt_secret` | `''` | Oauth2 JWT secret. Can be generated with ``gitea generate secret JWT_SECRET``. Will be autogenerated if not defined. |
| `gitea_oauth2_extra_config` | `''` | you can use this variable to pass additional config parameters in the `[oauth2]` section of the config. |

### additional gitea config
| variable name | default value | description |
| ------------- | ------------- | ----------- |
| `gitea_extra_config` | `''` | Additional gitea configuration. Have a look at the [config-cheat-sheet](https://docs.gitea.io/en-us/config-cheat-sheet/) before using it! |

### Fail2Ban configuration

If enabled, this will deploy a fail2ban filter and jail config for Gitea as described in the [Gitea Documentation](https://docs.gitea.io/en-us/fail2ban-setup/).

As this will only deploy config files, fail2ban already has to be installed or otherwise the role will fail.

| variable name | default value | description |
| ------------- | ------------- | ----------- |
| `gitea_fail2ban_enabled` | `false` | Whether to deploy the fail2ban config or not |
| `gitea_fail2ban_jail_maxretry` | `10` | fail2ban jail `maxretry` setting. |
| `gitea_fail2ban_jail_findtime` | `3600` | fail2ban jail `findtime` setting. |
| `gitea_fail2ban_jail_bantime` | `900` | fail2ban jail `bantime` setting. |
| `gitea_fail2ban_jail_action` | `iptables-allports` | fail2ban jail `action` setting. |

## Contributing
Don't hesitate to create a pull request, and when in doubt you can reach me on
Mastodon [@l3d@chaos.social](https://chaos.social/@l3d).

I'm happy to fix any issue that's been opened, or even better, review your pull requests :)

## Testing
There is a test that is using [molecule](https://molecule.readthedocs.io/en/stable-1.22/usage.html). And some linting tests with github actions.
*For locale molecule testing docker is required.*
