# Docker Commands

   Build & Tag	         
   ```
    docker build -t repo/app:latest .
   ```
    
   Tag image
   ```
    docker tag imageid repo/app
   ```
    
   Push image	
   ```
    docker push repo/app
   ```
   
   Pull image
   ```
    docker pull repo/app:version
   ```
  
   List images	         
   ```
    docker images
   ```
   
   List containers
   ```
    docker ps -a
   ```
    
   Remove image and containers
   ```
    docker ps | grep app | awk '{ print $$1 }' | xargs -I{} docker kill {}
    docker ps | grep app | awk '{ print $$1 }' | xargs -I{} docker rm {}
    docker rmi
    docker rmi . -f
    docker prune repo/app:version
    docker system prune -a
   ```
   
   ```
    docker login --username=<user> --password=<pwd> --email=<email-id>
   ```
   
   Run container
   ```
    docker run -it repo/app:version /bin/bash
    docker run repo/app:version
    docker run -d -p 9102:9102 -p 9125:9125 -p 9125:9125/udp prom/statsd-exporter
    docker run --name some-postgres -p 5432 -e POSTGRES_PASSWORD=<pwd> -d postgres
    docker run -it --env-file=.env repo/app:version /bin/bash
   ```
   
   ```
    docker stop
   ```
