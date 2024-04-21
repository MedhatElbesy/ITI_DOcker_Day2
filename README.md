# ITI - Docker Lab Two🐋
## Task 1: Run a container using nginx image, and mount a directory from your host into the Docker container.
example: /home/samy/nginx:/home/nginx (bind mount)

### Steps
#### 1. Create Bind Mount Directory
```bash
mkdir nginx_bindMount
cd nginx_bindMount
pwd
```

#### 2. Run a container using nginx image
```bash
docker run -d --name nginx_bindMount -v /root/nginx_bindMount:/user/share/nginx/html nginx
docker exec -it nginx_bindMount bash
```

#### 3. Echo any content to show when curl ip-address
```bash
cd /user/share/nginx/html
echo "Hello from Bind Mount Nginx" > index.html
exit
docker inspect -f '{{.NetworkSettings.IPAddress}}' nginx_bindMount    ->172.17.0.2
curl 172.17.0.2
```

---
## Task 2: Create 2 docker network (net-1 & net-2)

### Steps
#### 1. Create 2 docker network (net-1 & net-2)
```bash
docker network create network_1
docker network create network_2
```

#### 2. Run 2 new containers using nginx:alpine image, and attach the net-1 to them.
```bash
docker run -d --name nginx_net1 --net network_1 nginx:alpine
docker run -d --name nginx_net2 --network network_1 nginx:alpine
```

#### 3. Run another 1 new containers using nginx:alpine image, and attach the net-2 to them.
```bash
docker run -d --name nginx_net3 --net network_2 nginx:alpine
```

#### 4. Inspect the 3 containers to know their IPs and write them aside.
```bash
docker inspect -f '{{.NetworkSettings.Networks.network_1.IPAddress}}' nginx_net1
172.18.0.2

docker inspect -f '{{.NetworkSettings.Networks.network_1.IPAddress}}' nginx_net2
172.18.0.3

docker inspect -f '{{.NetworkSettings.Networks.network_2.IPAddress}}' nginx_net3
172.20.0.2
can use : docker inspect nginx_net1 | grep IPAddress
```

#### 5. Enter a container in the net-1 network and try to ping a container in the net-2 network (What do you notice?)
```bash
docker exec -it nginx_net1 sh 
Use ping or curl
ping 172.20.0.2
ping fails, because the two containers exist in different networks so we can see any response in terminal.
```

#### 6. Enter a container in the net-1 network and try to ping the other container in the same network (What do you notice?)
```bash
docker exec -it nginx_net1 sh 
curl 172.18.0.2
ping 172.18.0.3
ping successful and return response, because the two containers exist in the same network.
```

## Task 3: Explain the difference between Docker volumes and Bind Mount.
### Docker Volumes
● Docker volumes are managed by Docker and stored in a part of the host filesystem that is managed by Docker (/var/lib/docker/volumes on Linux).
● Volumes are designed to be persistent, meaning the data in a volume will persist even after the container is removed.
● Volumes can be shared between multiple containers, allowing for data sharing and collaboration.
● Volumes can be backed up, restored, and migrated more easily than bind mounts.
● Volumes are managed by Docker, which means you don't have to worry about the specific details of where the data is stored on the host filesystem

### Docker Bind Mounts
● Bind mounts are a way to mount a file or directory from the host machine into a container.
● The location of the mount on the host machine is specified by the user when the container is started.
● Bind mounts are tightly coupled to the host machine's filesystem, which can make it harder to move containers between hosts.
● Bind mounts are useful when you need to access specific files or directories on the host machine from within the container.
● Bind mounts are more flexible than volumes, as you can mount any directory from the host machine into the container

