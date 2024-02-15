
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

## List Containers
`sudo docker container ls

## Stop Container
`sudo docker container stop <contianer-id>

# Make sure to stop container before updating

# Get inside the container
`docker exec -it c1f49ac4954b bash`