To get docker swarm going and use consul for discovery. These two links (https://docs.docker.com/swarm/discovery/ & http://blog.arungupta.me/docker-swarm-cluster-using-consul/) should be sufficient... Otherwise just follow along

1) Create three machines in DigitalOcean. First one would be swarm master ( & agent), second one is swarm agent and third for consul.

2) Consul set-up : 
 Install consul as container :  
 first install compose ( though it is not madatory step; can directly run consul container from docker) 
 curl -L https://github.com/docker/compose/releases/download/1.5.2/run.sh > /usr/local/bin/docker-compose
 chmod +x /usr/local/bin/docker-compose
 /usr/local/bin/docker-compose 
 docker-compose --version ( to validate) 
 Run the docker-compose.yml, as provided in blog http://blog.arungupta.me/docker-swarm-cluster-using-consul/. 
 Consul installed and running at 8500
 
3) On first machine : 
docker daemon -H tcp://0.0.0.0:2375 -H unix:///var/run/docker.sock
sudo docker run -d swarm join --advertise=<<first machine ip>>:2375 consul://<<consul machine ip>>:8500
sudo docker run -d -p 3000:2375 swarm manage consul://<<consul machine ip>>:8500

4) On second machine
docker daemon -H tcp://0.0.0.0:2375 -H unix:///var/run/docker.sock
sudo docker run -d swarm join --advertise=<<first machine ip>>:2375 consul://<<consul machine ip>>:8500
sudo docker run -d -p 3000:2375 swarm manage consul://<<consul machine ip>>:8500

5) you can see the list of machine now 
    sudo docker  swarm list consul://<<consul machine ip>>:8500
    
6) create containers 
    docker -H tcp://<<first machine ip>>:3000 run -d -P sell201374/nodejs
    
7) Should be able to see the containers spread in those two machines..
 docker -H tcp://<<first machine ip>>:3000 ps

8) On consul UI, one can view the nodes running the swarm agents
http://<<consul machine>>:8500/ui/#/dc1/kv/docker/swarm/nodes/


 
