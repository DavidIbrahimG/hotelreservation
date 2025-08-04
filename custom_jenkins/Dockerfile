FROM jenkins/jenkins:lts

USER root

# Install Docker using the official Docker script
RUN apt-get update && \
    apt-get install -y curl ca-certificates gnupg lsb-release && \
    curl -fsSL https://get.docker.com | sh && \
    usermod -aG docker jenkins && \
    apt-get clean

# Optional: Create Docker volume dir
RUN mkdir -p /var/lib/docker
VOLUME /var/lib/docker

USER jenkins
