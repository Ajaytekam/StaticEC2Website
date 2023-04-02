# Hosting static website on aws EC2 Instance with Docker Container

Youtube Video Link: https://youtu.be/47nHb27AxEk

## Steps : 

1. Create a Security Group on AWS EC2 :  Allow port 80 (http) and 22 (ssh) inbound rule
2. Create an EC2 Instance using below provision shell script   
 
```  
#!/bin/bash

sudo apt-get update -y
sudo apt-get install wget curl git vim ca-certificates gnupg lsb-release -y
sudo mkdir -p /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
sudo echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt-get update -y
sudo apt-cache policy docker-ce
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-compose-plugin -y
sudo usermod -aG docker <USERNAME>
sudo sudo systemctl start docker
sudo sudo enable start docker
sudo wget https://github.com/docker/compose/releases/download/v2.14.0/docker-compose-linux-x86_64 -O /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose
```  

3. Create Dockerfile and nginx configuration   

Dockerfile 

```
FROM nginx:latest
RUN apt-get update && apt-get upgrade -y
RUN apt-get install wget unzip -y
WORKDIR /usr/share/nginx/html
COPY default.conf /etc/nginx/sites-enabled/
ADD https://bootstrapmade.com/content/templatefiles/Ninestars/Ninestars.zip .
RUN unzip Ninestars.zip
RUN mv Ninestars/* .
RUN rm -rf Ninestars Ninestars.zip
EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]
```  

nginx configuration file 

```
server {
        listen 80 default_server;
        root /usr/share/nginx/html;
        index index.html;
        server_name mysite.com;
}
```  

4. Build Docker Image   

```
docker build -t testimg:v1 . 
```  

5. Run the Container  

```
docker run -it --rm -d -p 80:80 testimg:v1  
```
