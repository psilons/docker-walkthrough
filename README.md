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

## Build Docker Image

