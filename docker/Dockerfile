FROM openjdk:17-alpine
COPY docker-walkthrough-1.0.0-SNAPSHOT.jar docker-walkthrough-1.0.0-SNAPSHOT.jar
COPY docker/start_app.sh /

ENTRYPOINT ["/start_app.sh"]
EXPOSE 8080
