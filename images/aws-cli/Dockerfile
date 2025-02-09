FROM amazon/aws-cli:latest

USER root

# Activer les extras et installer Docker
RUN amazon-linux-extras enable docker && \
    yum install -y docker && \
    yum clean all

# Vérification des versions installées
RUN aws --version && docker --version