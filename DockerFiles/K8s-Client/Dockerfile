# k8s-client/Dockerfile
FROM amazonlinux:2

# Set architecture and platform
ENV ARCH=amd64
ENV PLATFORM=Linux_${ARCH}
ENV EKSCTL_VERSION=latest

# Install essential tools and Kubernetes CLI tools
RUN yum update -y && \
    yum install -y python3 unzip curl git tar which sudo openssh-server net-tools && \
    # Install kubectl
    curl -LO "https://s3.us-west-2.amazonaws.com/amazon-eks/1.33.0/2025-05-01/bin/linux/amd64/kubectl" && \
    chmod +x kubectl && mv kubectl /usr/local/bin/ && \
    # Install eksctl
    curl -sLO "https://github.com/eksctl-io/eksctl/releases/latest/download/eksctl_${PLATFORM}.tar.gz" && \
    tar -xzf eksctl_${PLATFORM}.tar.gz -C /tmp && \
    mv /tmp/eksctl /usr/local/bin/ && \
    rm -f eksctl_${PLATFORM}.tar.gz && \
    # Install AWS CLI v2
    curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip" && \
    unzip awscliv2.zip && \
    ./aws/install && \
    rm -rf aws awscliv2.zip && \
    # Clean up yum cache
    yum clean all

# SSH setup
RUN ssh-keygen -A && \
    sed -i 's/#\?PasswordAuthentication .*/PasswordAuthentication no/' /etc/ssh/sshd_config && \
    sed -i 's/#\?PubkeyAuthentication .*/PubkeyAuthentication yes/' /etc/ssh/sshd_config && \
    echo "AuthorizedKeysFile .ssh/authorized_keys" >> /etc/ssh/sshd_config

# Create user and allow passwordless sudo
RUN useradd k8sclient && \
    mkdir -p /home/k8sclient/.ssh && \
    chmod 700 /home/k8sclient/.ssh && \
    chown -R k8sclient:k8sclient /home/k8sclient/ && \
    echo "k8sclient ALL=(ALL) NOPASSWD: ALL" >> /etc/sudoers

# Switch to non-root user
USER k8sclient
WORKDIR /home/k8sclient

# Verify installations and create AWS config directory
RUN kubectl version --client && \
    eksctl version && \
    aws --version && \
    mkdir -p /home/k8sclient/.aws

EXPOSE 22

# Start sshd
ENTRYPOINT ["sudo", "/usr/sbin/sshd", "-D"]