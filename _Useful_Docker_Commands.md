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
