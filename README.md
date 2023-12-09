# nxs-support-bot

nxs-support-bot lets you interact with your task tracker (at present supported Redmine only) via Telegram.

## Introduction

### Features

- Get in Telegram messages when issue is created or updated in Redmine
- Select only interesting issues you wish to follow
- Answer to Redmine issues by the replying to message in Telegram chat
- Create new issues in your Redmine using Telegram. Select a project and priority you need for a new issue
- Operate with files and media
- `Feedback` project to communicate with unauthorized users and get feedback
- Fully localizable interface in appropriate with user selected language
- Compatible with Redmine 4.2 and 5.0

### Who use this tool

Any teams who use Redmine to interact with other team members and customers:
- Developers
- Support
- Sales
- Marketing
- etc...

## Quickstart

### Install

There are two ways described below to install the Bot into your infrastructure.

First you need to do is to clone the repo and go to `.deploy/docker-compose` or `.deploy/kubernetes` directory in accordance to the way you choose to install:
```
git clone git@github.com:nixys/nxs-support-bot.git
```

Modify the `docker-compose.yml` or `kubernetes manifests` according to your infrastructure to make sure you have configured MySQL and Redis before setting up the Bot.

#### Docker-compose

Do the following steps:
- Configure nxs-support-bot (see [Configure](#configure) section for details)
- Launch the Bot with command:
  ```
  docker-compose up -d
  ``` 

#### Kubernetes

Do the following steps:
- Install [nxs-universal-chart](https://github.com/nixys/nxs-universal-chart) (`Helm 3` is required):
  ```
  helm repo add nixys https://registry.nixys.ru/chartrepo/public
  ```
- Configure nxs-support-bot (see [Configure](#configure) section for details)
- Launch the Bot with command:
  ```
  helm -n nxs-support-bot install nxs-support-bot nixys/universal-chart -f values.yaml
  ```

### Settings

Default configuration file path: `/nxs-support-bot.conf`. File represented in yaml.

#### General settings

| Option         | Type   | Required | Default value | Description                                                      |
|---             | :---:  | :---:    | :---:         |---                                                               |
| `logfile`      | String | No       | `stdout`      | Log file path. Also you may use `stdout` and `stderr`                                                                                                                |
| `loglevel`     | String | No       | `info`        | Log level. Available values: `debug`, `warn`, `error` and `info` |
| `pidfile`      | String | No       | -             | Pid file path. If `pidfile` is not set it will not be created                                                                                                                 |
| `api`          | [API](#api-settings) | Yes      | -             | API settings                               |
| `telegram`     | [Telegram](#telegram-settings) | Yes      | -             | Telegram settings                          |
| `redmine`      | [Redmine](#redmine-settings) | Yes      | -             | Redmine settings                            |
| `mysql`        | [MySQL](#mysql-settings) | Yes      | -             | MySQL settings                                |
| `redis`        | [Redis](#redis-settings) | No       | -             | Redis settings                                |
| `localization` | [Localization](#localization-settings) | No       | -             | Localization settings                  |
| `cache`        | [Cache](#cache-settings) | No       | -             | Cache settings                                |

##### API settings

| Option         | Type   | Required | Default value | Description           |
|---             | :---:  | :---:    | :---:         |---                    |
| `bind` | String | No | `0.0.0.0:80` | Address (with port) the bot will listen for a connections from Redmine with an issue updates   |
| `tls` | [API TLS](#api-tls-settings) | No | - | TLS settings for an Bot API |
| `clientMaxBodySize` | String | No | `36m` | Max body size for a requests from Redmine |
| `secretToken` | String | Yes | - | This value used to authorize your Redmine in Bot API. You may use any generator of random strings you want to create a secret token |

###### API TLS settings

| Option         | Type   | Required | Default value | Description           |
|---             | :---:  | :---:    | :---:         |---                    |
| `certfile` | String | Yes | - | TLS certificate file path |
| `keyfile` | String | Yes | - | TLS key file path |

##### Telegram settings

Note that the Bot uses the [getUpdates](https://core.telegram.org/bots/api#getupdates) method to receive an updates so you only need one option to configure to connect with the Telegram. 

| Option         | Type   | Required | Default value | Description                     |
|---             | :---:  | :---:    | :---:         |---                              |
| `apiToken`     | String | Yes      | -             | [Telegram API token for your bot](https://core.telegram.org/bots/tutorial#obtain-your-bot-token) |

##### Redmine settings

| Option    | Type   | Required | Default value | Description             |
|---        | :---:  | :---:    | :---:         |---                      |
| `host`    | String | Yes      | -             | Redmine host to connect (e.g.: `https://your.redmine.org`) |
| `key`     | String | Yes      | -             | Redmine API key for account with admin permissions. This account is used to get necessary data (such as accounts, projects, priorities, etc) from Redmine          |
| `feedback` | [Feedback](#feedback-settings) | No       | -             | Settings to enable `feedback` mode in Support bot. Disabled if this setting is not set |

###### Feedback settings

`Feedback` is used to communicate with users unauthorized in your Redmine. Also useful as a feedback form for customers.

If this module is enabled every user who doesn't have account in your Redmine may write a message to bot. This messages will either create a new issue in specified project in Redmine or add a comment. All your messages in that issues (exclude private comments) will be sent to user via Telegram.

| Option         | Type   | Required | Default value | Description           |
|---             | :---:  | :---:    | :---:         |---                    |
| `projectIdentifier` | String | Yes | - | Identifier of the project where feedback issues will be created |
| `userID` | Int | Yes | - | ID of user a messages will be created from in feedback issues |

##### MySQL settings

| Option     | Type   | Required | Default value | Description               |
|---         | :---:  | :---:    | :---:         |---                        |
| `host`     | String | No       | `127.0.0.1`   | Host to connect     |
| `port`     | Int    | No       | `3306`        | Port to connect     |
| `db`       | String | Yes      | -             | DB name to connect  |
| `user`     | String | Yes      | -             | User to connect     |
| `password` | String | Yes      | -             | Password to connect |

##### Redis settings

| Option | Type   | Required | Default value | Description           |
|---     | :---:  | :---:    | :---:         |---                    |
| `host` | String | No       | `127.0.0.1`   | Host to connect |
| `port` | Int    | No       | `6379`        | Port to connect |

##### Localization settings

| Option         | Type   | Required | Default value | Description           |
|---             | :---:  | :---:    | :---:         |---                    |
| `path` | String | No | `/localization` | Path to directory with localization files |

##### Cache settings

| Option         | Type   | Required | Default value | Description           |
|---             | :---:  | :---:    | :---:         |---                    |
| `ttl` | String | No | `5m` | Redmine data cache TTL |

### Configure

To complete the Bot installation you need to do some actions described in this section. 

#### Redmine

After you've installed and configured the Redmine, do the following to take the nxs-support-bot collaboration.

##### General

Check the option `Enable REST web service` on `/settings?tab=api` page in your Redmine is enabled.

Then create a new one (or take an existing) account with administrator permissions. In the account settings page look for an `API access key` and use this value as a `key` option in [Redmine settings section](#redmine-settings).

##### Feedback project

If you want to have an ability to use Feedback issues in your Bot do the following.

First create a new one (or take an existing) project to store that issues. Take an identifier of this project and use this value as a `projectIdentifier` option in [Redmine feedback settings section](#feedback-settings).

Last you need to do is to create a new one (or take an existing) feedback account with no administrator permissions and use its ID as an `userID` option in [Redmine feedback settings section](#feedback-settings).

#### nxs-chat-redmine plugin

Install and configure [nxs-chat-redmine](https://github.com/nixys/nxs-chat-redmine) plugin in your Redmine.

Choose the version in accordance with compatibility table:

| `nxs-support-bot` | [nxs-chat-redmine](https://github.com/nixys/nxs-chat-redmine) |
| --- | --- |
| v1.0.0 - v1.2.0 | v1.5 - v3.2.0 |
| v1.3.0 | v4.0.0 |
| v1.4.0 | v4.1.0 |

#### nxs-support-bot

Now you need to set up nxs-support-bot config file (see options description in [settings section](#settings)). To configure the Bot you need to change the file located according to the way you choose to install:
- For `Docker Compose`: file `.deploy/docker-compose/.env`
- For `Kubernetes`: file `.deploy/kubernetes/values.yaml`, secret `nxs-support-bot-env` and configmap `nxs-support-bot-config`

If you didn't use [nxs-chat-srv](https://github.com/nixys/nxs-chat-srv) skip this section and back to installation in accordance with method you selected (either [Docker-compose](#docker-compose) or [Kubernetes](#kubernetes)).

Otherwise you need to migrate your old data to new version of Bot with [nxs-support-bot-migrate](https://github.com/nixys/nxs-support-bot-migrate). See the section below for details.

##### Update from nxs-chat-srv

*Only for users of old version*

Choose the way for your installation method.

**Docker Compose**

Do the following steps:
- Fill the `.deploy/docker-compose/.env_migration` file with settings to connect to `nxs-chat-srv` databases (MySQL and Redis). See [nxs-support-bot-migrate settings section](https://github.com/nixys/nxs-support-bot-migrate#settings) for details
- Stop the `nxs-chat-srv`
- Start the data migration process from `.deploy/docker-compose` directory:
  ```
  docker compose -f docker-compose-migrate.yml up -d
  ```
- After process is completed make sure the nxs-support-bot successfully started
- Stop and delete the data for migration process:
  ```
  docker compose -f docker-compose-migrate.yml down
  rm docker-compose-migrate.yml .env_migrate
  ```
Go back to the [Docker-compose](#docker-compose) and follow the instructions to complete the nxs-support-bot installation.

**Kubernetes**

Do the following steps:
- Fill the `.deploy/kubernetes/migrate_values.yaml` file with settings to connect to `nxs-chat-srv` databases (MySQL and Redis) and `nxs-support-bot` database (MySQL). See [nxs-support-bot-migrate settings section](https://github.com/nixys/nxs-support-bot-migrate#settings) for details
- Stop the `nxs-chat-srv`
- Start the data migration process from `.deploy/kubernetes` directory:
  ```
  helm -n nxs-support-bot install migrate nixys/universal-chart -f migrate_values.yaml
  ```
- Wait for job `nxs-support-bot-migrate` been completed
- Uninstall migration job:
  ```
  helm -n nxs-support-bot uninstall migrate
  ```

Go back to the [Kubernetes](#kubernetes) and follow the instructions to complete the nxs-support-bot installation.

## Roadmap

Following features are already in backlog for our development team and will be released soon:
- [x] Notifications to the mention users
- [ ] Default language in accordance with Telegram lang settings for user accounts
- [ ] Ability to specify trackers and other options for new issues
- [ ] Ability to follow/unfollow to an issue from Telegram
- [ ] PgSQL support

## Feedback

For support and feedback please contact me:
- [Issues](https://github.com/nixys/nxs-support-bot/issues)
- Telegram: [@borisershov](https://t.me/borisershov)
- E-mail: b.ershov@nixys.io

## License

nxs-support-bot is released under the [Apache License 2.0](LICENSE).
