# First stage: Build the application
FROM openjdk:11 AS BUILD_IMAGE

# Install Maven and Git
RUN apt-get update && \
    apt-get install -y --no-install-recommends maven git && \
    rm -rf /var/lib/apt/lists/*

# Increase Git buffer size before cloning
RUN git config --global http.postBuffer 524288000

# Clone the repo with retry logic
RUN echo "Attempting to clone the repository..." && \
    for i in {1..5}; do git clone https://github.com/devopshydclub/vprofile-project.git && break || sleep 5; done && \
    cd vprofile-project && \
    git checkout docker && \
    mvn install

# Second stage: Deploy the application
FROM tomcat:9-jre11

LABEL "Project"="Vprofile"
LABEL "Author"="Ekow"

# Clean the webapps directory and deploy the built .war file
RUN rm -rf /usr/local/tomcat/webapps/*
COPY --from=BUILD_IMAGE vprofile-project/target/vprofile-v2.war /usr/local/tomcat/webapps/ROOT.war

# Expose the Tomcat port and set the working directory
EXPOSE 8080
WORKDIR /usr/local/tomcat/

# Set up volume for persistence and start Tomcat
VOLUME /usr/local/tomcat/webapps
CMD ["catalina.sh", "run"]
