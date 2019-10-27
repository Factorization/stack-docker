# stack-docker

This example Docker Compose configuration demonstrates many components of the
Elastic Stack, all running on a single machine under Docker.

## Prerequisites

- Docker and Docker Compose.
  - Windows and Mac users get Compose installed automatically
with Docker for Windows/Mac.
  - Linux users can read the [install instructions](https://docs.docker.com/compose/install/#install-compose) or can install via pip:

    ```bash
    pip install docker-compose
    ```

- Windows Users must set the following 2 ENV vars:
  - `COMPOSE_CONVERT_WINDOWS_PATHS=1`
  - `PWD=/path/to/checkout/for/stack-docker`
    - For example I use the path: `/c/Users/nick/elastic/stack-docker`
    - Note: you're paths must be in the form of `/c/path/to/place` using `C:\path\to\place` will not work
  - You can set these two ways:
    1. Temporarily add an env var in powershell use:
      `$Env:COMPOSE_CONVERT_WINDOWS_PATHS=1`
    2. Permanently add an env var in powershell use:
      `[Environment]::SetEnvironmentVariable("COMPOSE_CONVERT_WINDOWS_PATHS", "1", "Machine")`
        > Note: you will need to refresh or create a new powershell for this env var to take effect
    3. In System Properties add the environment variables.

- At least 4GiB of RAM for the containers. Windows and Mac users ***must*** configure their Docker virtual machine to have more than the default 2 GB of RAM:
  ![Docker VM memory settings](screenshots/docker-vm-memory-settings.png)
- Linux Users must set the following configuration as `root`:

  ```bash
  sysctl -w vm.max_map_count=262144
  ```

  By default, the amount of Virtual Memory [is not enough](https://www.elastic.co/guide/en/elasticsearch/reference/current/vm-max-map-count.html).

## Starting the stack

First we need to:

1. Set default password
2. Create keystores to store passwords
3. Install dashboards, index patterns, etc.. for beats and apm

This is accomplished using the setup.yml file:

```bash
docker-compose -f setup.yml up
```

> Note: after the setup completes, it will output the password for the `elastic` user.

Now we can launch the stack with `docker-compose up -d` to create a demonstration Elastic Stack with Elasticsearch, Kibana, Logstash, Auditbeat, Metricbeat, Filebeat, Packetbeat and Heartbeat.

Point a browser at [`http://localhost:5601`](http://localhost:5601) to login.
> Note: Elastic (Kibana) is now setup with self-signed certs.

Log in with `elastic` and the auto generated elastic password from the setup.

## Some helpful commands

To rebuild the stack or to rerun after a failed setup:
> Note: This will delete all the data in your existing elasticsearch.

```bash
docker-compose -f docker-compose.yml -f docker-compose.setup.yml -f setup.yml down -v
docker-compose -f docker-compose.yml -f docker-compose.setup.yml -f setup.yml down --remove-orphans
docker-compose -f setup.yml up --force-recreate
```

Other ways to start the cluster (after the `setup.yml` completed successfully):

```bash
# Start stack in the background (detached)
docker-compose up -d
# Start stack in the foreground (shows all logs)
docker-compose up
```

To stop the stack:

```bash
# If the stack is running in the background
docker-compose down
# If the stack is running in the foreground
CTRL+C
```

## Troubleshooting

If your are having trouble with the stack, run `docker-compose ps` to see all of the containers and their state. If any of the containers are not running or show as not running, re-start the stack in the foreground with `docker-compose up` and look for errors in the output.
