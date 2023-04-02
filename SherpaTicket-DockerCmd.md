
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

`docker run --rm -d -p 8090:80 w3sherpa/sherpa-ticket:latest`
