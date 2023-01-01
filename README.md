# docker-walkthrough

## Simple Spring Boot Application
Follow: https://spring.io/guides/gs/spring-boot/
- Refresh gradle in IDE: gradle should be setup during the refresh
- ```.\gradlew bootRun``` to run the app
- ```curl localhost:8080``` should display Hello World!

To build jar  
```.\gradlew clean build```

To test  
```java -jar build\libs\docker-walkthrough-1.0.0-SNAPSHOT.jar```  
and then ```curl localhost:8080``` as above.

## Build Docker Images

- Add a Dockerfile to specify the image build
- **Need to remove version in the jar name later on.**
- In build.gradle, add the plugin and specify docker's name and tag.
- check the previous build ```docker iamges``` 
- remove the previous build ```docker rmi -f 1e8d2411917f```
- start the build with ```./gradlew docker```
- Run ```docker images``` to verify the new build
- If needed, get into image: ```docker run --rm -it --entrypoint=/bin/sh 1e8d2411917f```

We need to pick the simplest plugin that don't enclose Dockerfile details.

## Run Docker Images

docker rm hellow
docker run -d -p 8080:8080 --name hellow 192.168.1.206:5000/docker-walkthrough:1.0.0-SNAPSHOT
docker ps
docker stop 71428877674a  # container id from docker ps

