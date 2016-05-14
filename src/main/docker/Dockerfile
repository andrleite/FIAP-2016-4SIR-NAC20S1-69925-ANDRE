FROM anapsix/alpine-java:jre8
VOLUME /tmp
ADD accessing-mongodb-data-rest-0.1.0.jar app.jar
RUN sh -c 'touch /app.jar'
ENTRYPOINT ["java","-Dspring.data.mongodb.uri=mongodb://mongodb/person","-Djava.security.egd=file:/dev/./urandom","-jar","/app.jar"]
