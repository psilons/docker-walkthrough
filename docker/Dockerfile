FROM openjdk:17-alpine
RUN apk --no-cache add curl
COPY docker-walkthrough-1.0.0-SNAPSHOT.jar /docker-walkthrough-1.0.0-SNAPSHOT.jar

# use shell, works fine
COPY start_app.sh /
RUN chmod -R 777 /start_app.sh
ENTRYPOINT ["/start_app.sh"]
# use java directly, works fine
# ENTRYPOINT ["java", "-jar", "/docker-walkthrough-1.0.0-SNAPSHOT.jar"]

EXPOSE 8080
HEALTHCHECK CMD curl -f http://localhost:8080 || exit 1