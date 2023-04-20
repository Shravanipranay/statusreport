FROM ubuntu
RUN apt update && apt install openjdk-11-jdk -y && apt install curl -y 
RUN curl -fsSL https://pkg.jenkins.io/debian/jenkins.io-2023.key | tee \
  /usr/share/keyrings/jenkins-keyring.asc > /dev/null
RUN echo deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] \
  https://pkg.jenkins.io/debian binary/ |  tee \
  /etc/apt/sources.list.d/jenkins.list > /dev/null
RUN apt update
RUN apt install jenkins -y
USER jenkins
RUN service jenkins start
EXPOSE 8080
CMD ["sleep" , "1d"]



