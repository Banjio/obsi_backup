
## Stop and remove all containers

```
docker stop $(docker ps -aq)
docker rm $(docker ps -aq)
```
or to remove only stopped containers
```
docker container prune -f
```
