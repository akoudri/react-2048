FROM amazon/aws-cli:latest

USER root

RUN curl -fsSL https://download.docker.com/linux/static/stable/x86_64/docker-24.0.5.tgz | tar -xz -C /usr/local/bin --strip-components=1 \
    && chmod +x /usr/local/bin/docker


USER 1000
