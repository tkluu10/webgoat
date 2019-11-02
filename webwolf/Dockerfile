FROM openjdk:11.0.1-jre-slim-stretch

ARG webwolf_version=v8.0.0-SNAPSHOT

COPY target/webwolf-${webwolf_version}.jar ./WebGoat_master/webwolf/webwolf.jar
COPY start-webwolf.sh ./WebGoat_master/webwolf

EXPOSE 9090

ENTRYPOINT ["./WebGoat_master/webwolf/start-webwolf.sh"]
CMD ["--server.port=9090", "--server.address=0.0.0.0"]