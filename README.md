## Installing Jenkins

```bash
docker build -t myjenkins-blueocean:2.414.2 .
```

## Create the network 'jenkins'

```bash
docker network create jenkins
```

## Running the container

```bash
docker run --name jenkins-blueocean --restart=on-failure --detach `
  --network jenkins --env DOCKER_HOST=tcp://docker:2376 `
  --env DOCKER_CERT_PATH=/certs/client --env DOCKER_TLS_VERIFY=1 `
  --volume jenkins-data:/var/jenkins_home `
  --volume jenkins-docker-certs:/certs/client:ro `
  --publish 8080:8080 --publish 50000:50000 myjenkins-blueocean:2.414.2
```

## Getting the initial password

```bash
docker exec jenkins-blueocean cat /var/jenkins_home/secrets/initialAdminPassword
```

## Open the Jenkins Docker bash
```bash
docker exec -it jenkins-blueocean bash
```

## Connect to the Jenkins UI
```bash
http://127.0.0.1:8080
```


## Setting up Cloud Jenkins Agent (alpine/socat)

alpine/socat is a lightweight container that can be used to create a TCP connection to the Docker daemon.

```bash
docker run -d --restart=always -p 127.0.0.1:2376:2375 --network jenkins -v /var/run/docker.sock:/var/run/docker.sock alpine/socat tcp-listen:2375,fork,reuseaddr unix-connect:/var/run/docker.sock
```

To get the **IP address** of the container for TCP connection
```bash
docker inspect <container_name>
```

port: `2376`

#### reference: https://stackoverflow.com/questions/47709208/how-to-find-docker-host-uri-to-be-used-in-jenkins-docker-plugin