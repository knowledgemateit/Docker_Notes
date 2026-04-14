# docker_compose_2

https://www.fosstechnix.com/dockerfile-instructions/

yum install docker -y

systemctl start docker

 sudo curl -L https://github.com/docker/compose/releases/download/1.21.0/docker-compose-$(uname -s)-$(uname -m) -o /usr/local/bin/docker-compose
 
  sudo chmod +x /usr/local/bin/docker-compose
  
 sudo ln -s /usr/local/bin/docker-compose /usr/bin/docker-compose
 
 docker-compose --version
