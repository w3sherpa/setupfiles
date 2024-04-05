## List all containers
#### `sudo docker ps`
## List all images
#### `sudo docker images`
## Remove all dangling (name with <none>) images
#### `sudo docker rmi $(sudo docker images -f dangling=true -q)`
## Remove all images with name w3sherpa/sherpa-ticket
#### `sudo docker rmi --force $(sudo docker images -q 'w3sherpa/sherpa-ticket' | uniq)`
