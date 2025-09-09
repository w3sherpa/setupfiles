## Get inside the container

### `docker exec -it c1f49ac4954b bash`

## List all containers

#### `sudo docker ps`

## List all images

#### `sudo docker images`

## Stop Container

#### `sudo docker container stop <contianer-id>`

## List Containers

#### `sudo docker container ls`

## Remove all stopped containers

#### `sudo docker container prune`

## Remove docker image by Id

#### `sudo docker rmi 9f24dc956baa`

## Remove all dangling (name with <none>) images

#### `sudo docker rmi $(sudo docker images -f dangling=true -q)`

## Remove all images with name w3sherpa/sherpa-ticket

#### `sudo docker rmi --force $(sudo docker images -q 'w3sherpa/sherpa-ticket' | uniq)`

## List of containers with different status

### `sudo docker container ls -f 'status=exited' -f 'status=dead' -f 'status=created' -f 'status=paused' -f 'status=restarting' -f 'status=removing'`

### See what network(s) your container is on, assuming your container is called sherpa-ticket:

sudo docker inspect sherpa-ticket -f "{{json .NetworkSettings.Networks }}"

## To push to docker hub

1. docker login
2. username:w3sherpa
3. password:T3serract

`docker build -t sherpa-ticket:latest .`
`docker tag sherpa-ticket w3sherpa/sherpa-ticket:latest`  
`docker push w3sherpa/sherpa-ticket:latest`

## Pulling and running the image

1. docker login
2. username:w3sherpa
3. password:T3serract

`docker image pull w3sherpa/sherpa-ticket:latest`

`docker run --rm -d -p 8082:80 w3sherpa/juna-balla:latest`

# Make sure to stop container before updating

### Copy file from windows os to linux

## Use following in cmd to copy all files from c:/src_path/ to root@45.33.2.230:/var/destination_path

`scp -rp  C:/src_path/* root@45.33.2.230:/var/destination_path`

## If permission denied to the folder, ssh in to the linus destination server and run:

`sudo chown -R utilsherpa:utilsherpa /var/destination_path`

# Manual docker push

`Go to the app folder where the docker file is`

## For Scheapp-api

docker build -t scheapp-api:latest .
docker login -u lonheeti@gmail.com -p T3serract
docker tag scheapp-api w3sherpa/scheapp-api:latest
docker push w3sherpa/scheapp-api:latest

## GO to the utilsherpa vm and run

sudo docker image pull w3sherpa/scheapp-api:latest
sudo docker run --rm -d --name=scheapp-api -p 8001:8080 w3sherpa/scheapp-api:latest

## For Scheapp-app

docker build -t scheapp-app:latest .
docker login -u lonheeti@gmail.com -p T3serract
docker tag scheapp-app w3sherpa/scheapp-app:latest
docker push w3sherpa/scheapp-app:latest

## GO to the webshepa vm and run

sudo docker image pull w3sherpa/scheapp-app:latest
sudo docker run --rm -d --name=scheapp-app -p 8002:8080 w3sherpa/scheapp-app:latest

## For starcutsbarber-app

docker build -t starcutsbarber-service:latest .
docker login -u lonheeti@gmail.com -p T3serract
docker tag starcutsbarber-service w3sherpa/starcutsbarber-service:latest
docker push w3sherpa/starcutsbarber-service:latest

## GO to the webshepa vm and run

sudo docker image pull w3sherpa/starcutsbarber-service:latest
sudo docker run --rm -d --name=starcutsbarber-service -p 8011:8080 w3sherpa/starcutsbarber-service:latest

## Docker Network

-- create network
docker network create scheapp_net

-- remove netowrk
docker network rm <id>

-- inspect the scheapp_net network
docker network inspect scheapp_net

-- inspect the networ of db_scheapp container
docker inspect -f '{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' db_scheapp
