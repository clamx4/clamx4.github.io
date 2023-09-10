https://www.cyberciti.biz/faq/how-to-install-docker-on-amazon-linux-2/

``` Bash
ssh ec2-user@ec2-ip-address-dns-name-here
sudo yum update
sudo yum search docker
sudo yum info docker
sudo yum install docker
sudo usermod -a -G docker ec2-user
id ec2-user
newgrp docker

# install docker-compose
wget https://github.com/docker/compose/releases/latest/download/docker-compose-$(uname -s)-$(uname -m) 
sudo mv docker-compose-$(uname -s)-$(uname -m) /usr/local/bin/docker-compose
sudo chmod -v +x /usr/local/bin/docker-compose
# end install docker-compose

sudo systemctl enable docker.service
sudo systemctl start docker.service

# verification
sudo systemctl status docker.service
docker version
docker-compose version
```
