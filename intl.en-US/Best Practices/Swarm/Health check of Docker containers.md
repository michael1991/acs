# Health check of Docker containers {#concept_kjl_551_ydb .concept}

In a distributed system, the service availability is frequently checked by using the health check to avoid exceptions when being called by other services. Docker introduced native health check implementation after version 1.12. This document introduces the health check of Docker containers.

Process-level health check checks whether or not the process is alive and is the simplest health check for containers. Docker daemon automatically monitors the PID1 process in the container. If the `docker run` command specifies the restart policy, closed containers can be restarted automatically according to the restart policy. In many real scenarios, process-level health check alone is far from enough. For example, if a container process is still alive, but is locked by an app deadlock and fails to respond to user requests, such problems won't be discovered by process monitoring.

Kubernetes provides Liveness and Readness probes to check the container and its service health respectively. Alibaba Cloud Container Service also provides a similar [Service health check](../../../../intl.en-US/User Guide/Swarm cluster/Service orchestrations/probe.md#).

## Docker native health check capability {#section_tjt_551_ydb .section}

Docker introduced the native health check implementation after version 1.12. The health check configurations of an application can be declared in the Dockerfile. `The HEALTHCHECK` instruction declares the health check command that can be used to determine whether or not the service status of the container master process is normal. This can reflect the real status of the container.

`HEALTHCHECK` instruction format:

-   `HEALTHCHECK [option] CMD <command>`: The command that sets the container health check.
-   `HEALTHCHECK NONE`: If the basic image has a health check instruction, this line can be used to block it.

**Note:** The `HEALTHCHECK` can only appear once in the Dockerfile. If multiple HEALTHCHECK instructions exist, only the last one takes effect.

Images built by using Dockerfiles that contain `HEALTHCHECK` instructions can check the health status when instantiating Docker containers. Health check is started automatically after the container is started.

`HEALTHCHECK` supports the following options:

-   `--interval=<interval>`: The time interval between two health checks. The default value is 30 seconds.
-   `--timeout=<interval>`: The timeout for running the health check command. The health check fails if the timeout is exceeded. The default value is 30 seconds.
-   `--retries=<number of times>`: The container status is regarded as unhealthy if the health check fails continuously for a specified number of times. The default value is 3.
-   `--start-period=<interval>`: The initialization time of application startup. Failed health check during the startup is not counted. The default value is 0 second \(introduced since version 17.05\).

The command after `HEALTHCHECK [option] CMD` follows the same format as `ENTRYPOINT`, in either the shell or the exec format. The returned value of the command determines the success or failure of the health check:

-   0: Success.
-   1: Failure.
-   2: Reserved value. Do not use.

After a container is started, the initial status is `starting`. Docker Engine waits for a period of `interval` to regularly run the health check command.  If the returned value of a single check is not 0 or the running lasts longer than the specified `timeout` time, the health check is considered as failed. If the health check fails continuously for `retries` times, the health status changes to `unhealthy`.

-   If the health check succeeds once, Docker changes the container status back to Healthy.
-   Docker Engine issues a health\_status event if the container health status changes.

Assume that an image is a simple Web service. To enable health check to determine whether or not its Web service is working normally, `curl` can be used to help with the determination and the `HEALTHCHECK` instruction in its Dockerfile can be written as follows:

```
FROM elasticsearch:5.5
HEALTHCHECK --interval=5s --timeout=2s --retries=12 \
  CMD curl --silent --fail localhost:9200/_cluster/health || exit 1
```

```
docker build -t test/elasticsearch:5.5 .
docker run --rm -d \
    --name=elasticsearch \
    test/elasticsearch:5.5
```

You can use `docker ps`. After several seconds, the Elasticsearch container changes from the Starting status to Healthy status.

```
$ docker ps
CONTAINER ID IMAGE COMMAND CREATED STATUS PORTS NAMES
c9a6e68d4a7f test/elasticsearch:5.5 "/docker-entrypoin..." 2 seconds ago Up 2 seconds (health: starting) 9200/tcp, 9300/tcp elasticsearch
$ docker ps
CONTAINER ID IMAGE COMMAND CREATED STATUS PORTS NAMES
c9a6e68d4a7f test/elasticsearch:5.5 "/docker-entrypoin..." 14 seconds ago Up 13 seconds (healthy) 9200/tcp, 9300/tcp elasticsearch
```

Another method is to directly specify the health check policy in the `docker run` command.

```
$ docker run --rm -d \
    --name=elasticsearch \
    --health-cmd="curl --silent --fail localhost:9200/_cluster/health || exit 1" \
    --health-interval=5s \
    --health-retries=12 \
    --health-timeout=2s \
    elasticsearch:5.5
```

To help troubleshoot the issue, all output results of health check commands \(including stdout and stderr\) are stored in health status and you can view them with the docker inspect command. Use the following commands to retrieve the health check results of the past five containers.

```
docker inspect --format='{{json . State.Health}}' elasticsearch
```

Or

```
docker inspect elasticsearch | jq ".[]. State.Health"
```

The sample result is as follows:

```
{
  "Status": "healthy",
  "FailingStreak": 0,
  "Log": [
    {
      "Start": "2017-08-19T09:12:53.393598805Z",
      "End": "2017-08-19T09:12:53.452931792Z",
      "ExitCode": 0,
      "Output": "..."
    },
    ...
}
```

Generally, we recommend that you declare the corresponding health check policy in the Dockerfile to facilitate the use of images because application developers know better about the application SLA. The application deployment and Operation & Maintenance personnel can adjust the health check policies as needed for deployment scenarios by using the command line parameters and REST API.

The Docker community provides some instance images that contain health check. Obtain them in the following project: [https://github.com/docker-library/healthcheck](https://github.com/docker-library/healthcheck).

**Note:** 

-   Alibaba Cloud Container Service supports Docker native health check and Alibaba Cloud extension health check.
-   Currently, Kubernetes does not support Docker native health check.

