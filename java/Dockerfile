FROM openjdk:11
WORKDIR /usr/src/app
COPY MyServer.java .
RUN javac MyServer.java
EXPOSE 8080
CMD ["java", "MyServer"]
