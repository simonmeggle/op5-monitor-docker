# Docker image for OP5 Monitor

OP5 Monitor is a software product for server, Network monitoring and management based on the Open Source project Nagios.

> This image is not a OP5 official release and therefore does not adhere to your support agreement you may have with OP5.

*Also available on: [Docker Hub](https://hub.docker.com/r/op5com/op5-monitor)*

## Features

 * Latest version of OP5 monitor to date (currently v7.3.15)
 * Pre-bundled with a trial license
 * Support for **triggering hooks** on prestart, poststart and poststop. **Slack** hook example is included.
 * Support for **importing OP5 backup files** to help quickly lanuch testing/development environments

## Install

Pull the docker image from Docker Hub:

```sh
$ docker pull op5com/op5-monitor
```

or, clone this repo to your docker server and build it:

```sh
$ git clone https://github.com/misiupajor/op5-monitor-docker.git
```

```sh
$ docker build --rm -t op5com/op5-monitor .
```

## Usage

Run the docker container:

```sh
$ docker run -tid -p 443:443 op5com/op5-monitor
```

Now you can reach OP5 Monitor on:

https://`<docker server>`:443

## Adding hooks

You can add custom hooks by adding any script to /entrypoint.d/hooks/ directory. Ensure that they are well defined in /entrypoint.d/hooks.json, something like this will work:

```json
{
        "prestart": [
            {
                "path": "/usr/libexec/entrypoint.d/hooks/slack.py",
                "args": ["prestart"]
            },
            {
                "path": "/usr/libexec/entrypoint.d/hooks/example.sh",
                "args": ["--action", "contained_started"]
            }
        ],
        "poststart": [
            {
                "path": "/usr/libexec/entrypoint.d/hooks/slack.py",
                "args": ["poststart"]
            },
            {
                "path": "/usr/libexec/entrypoint.d/hooks/example.sh",
                "args": ["--action", "contained_booted"]
            }
        ],
        "poststop":[
            {
                "path": "/usr/libexec/entrypoint.d/hooks/slack.py",
                "args": ["poststop"]
            },
            {
                "path": "/usr/libexec/entrypoint.d/hooks/example.sh",
                "args": ["--action", "container_stopped"]
            }
        ]
}
```

And then build:

```sh
$ docker build --rm -t op5com/op5-monitor .
```

## Importing OP5 backup files

This docker container supports importing and restoring of existing OP5 backups. This can be helpful when you need to spin up an identical copy of your production OP5 servers, say for testing or development purposes.

In order to do so, you first need to create a **compatible** backup for docker on one of your OP5 master or peer server, using:
```sh
$ op5-backup -- -ssh -sysconfig -op5-system
```

Then place the backup file generated by op5-backup (ends with .backup-extension) in entrypoint.d/backups/, and finally run your docker container as follows:

```sh
$ docker build --rm -t op5com/op5-monitor .
```

```sh
$ docker run -tid -e IMPORT_BACKUP=<name of backup file>.backup -p 443:443 op5com/op5-monitor
```

## Contributors

Thanks goes to these wonderful people:

* Caesar Ahlenhed ([@MrFriday AB](https://www.mrfriday.com))
* Ken Dobbins ([@OP5](https://www.op5.com))

## Author
**Misiu Pajor**

* [github/misiupajor](https://github.com/misiupajor)
