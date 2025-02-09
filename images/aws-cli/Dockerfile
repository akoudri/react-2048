FROM amazon/aws-cli:2.24.0
USER root

# Installer Docker CLI
RUN yum update -y && \
    yum install -y docker && \
    yum clean all

# Vérifier les versions installées
RUN aws --version && docker --version