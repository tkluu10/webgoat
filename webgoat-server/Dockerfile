FROM openjdk:11.0.1-jre-slim-stretch

ARG webgoat_version=v8.0.0.M25

RUN cd ./WebGoat_master; mkdir -p .webgoat-${webgoat_version}
COPY target/webgoat-server-${webgoat_version}.jar ./WebGoat_master/webgoat.jar

EXPOSE 8080

ENTRYPOINT ["java", "-Djava.security.egd=file:/dev/./urandom", "-jar", "./WebGoat_master/webgoat.jar"]
CMD ["--server.port=8080", "--server.address=0.0.0.0"]