# Live Event: Continuous Integration with DroneCI


Show some ❤️: ⭐ this Repository and follow [hrittikhere](https://github.com/hrittikhere)

## Schedule
* Location: LinkedIn Live
* Date: Feb 4 2022, Fri
* Time: 9:00 pm IST to 10:30 PM IST
* Join [here](https://t.co/fERBfLb5YU)

## Agenda
1. What is continuous integration?
1. Why is it required?
1. How to set up a self-hosted CI with Drone Community Edition? 

## Prerequisite
1. Public IP on your local Machine via [ngrok](https://ngrok.com) or Public VM with HTTP(80) port exposed. Use [Azure](azure.com) or other Cloud Providers for a Public VM.

## Resources

### Enviroment Variables for `docker-compose`:

```bash
# Drone Server configuration
DRONE_SERVER_HOST=< hostname>
DRONE_SERVER_PROTO=http
DRONE_GITHUB_CLIENT_ID=<github client ID>
DRONE_GITHUB_CLIENT_SECRET=<github secret>
DRONE_RPC_SECRET=<Drone secret>
DRONE_TLS_AUTOCERT=false
DRONE_USER_CREATE=username:<github_username>,admin:true

# Runners configuration
DRONE_RPC_HOST=< hostname>
DRONE_RPC_PROTO=http
DRONE_RUNNER_NAME="Drone.io_runner" # It CANNOT contain spaces!
```

### `RPC_SECRET`
Create a shared secret to authenticate communication between runners and your central Drone server. You can use openssl to generate a shared secret:

```bash
openssl rand -hex 16
```

### `Docker-compose.yml` for Servers and Runners:

```yaml

version: '3.8'
services:
  drone:
    image: 'drone/drone:2'
    volumes:
      - "/var/run/docker.sock:/var/run/docker.sock"
      - "./volumes/drone:/data"
      - '/etc/localtime:/etc/localtime:ro'
    restart: always
    ports:
      - 80:80
    env_file:
    - .env
  drone-agent:
    image: drone/drone-runner-docker:1
    command: agent
    restart: always
    depends_on:
      - drone
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock
      - '/etc/localtime:/etc/localtime:ro'
    env_file:
      - .env

```

### `.drone.yml` for pipelines:

```yaml
kind: pipeline
type: docker
name: default

workspace:
  base: /go
  path: src/github.com/hrittikhere/reponame/main

steps:
- name: test
  image: golang
  commands:
  - go mod init
  - go get
  - go test

- name: docker  
  image: plugins/docker
  settings:
    # registry: docker.io
    repo: username/name_of_your_repo
    username:
      from_secret: docker_username
    password:
      from_secret: docker_password
    tags: 
      - latest
```

Read more about Drone CI [here](https://docs.drone.io/).