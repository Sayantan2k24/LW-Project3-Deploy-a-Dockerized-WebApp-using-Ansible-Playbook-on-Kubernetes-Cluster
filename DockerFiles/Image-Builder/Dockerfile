# Have to manually Install and configure docker later for this project
# This is ansible slave image - without docker engine
# and must run the container in privileged mode

# Commands are below

# as a `ansible` user run these
# sudo dnf -y install dnf-plugins-core
# sudo dnf config-manager --add-repo https://download.docker.com/linux/rhel/docker-ce.repo


# sudo dnf install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
# sudo usermod -aG docker ansible
# newgrp docker
# sudo dockerd &

FROM redhat/ubi9:latest

# Install Python and OpenSSH server
RUN dnf -y update && \
    dnf -y install \
        python3 \
        net-tools \
        iputils \
        python3-pip \
        openssh-clients \
        openssh-server \
        sudo \
    && dnf clean all

# Create ansible user with sudo rights
RUN useradd ansible && \
    echo "ansible ALL=(ALL) NOPASSWD:ALL" >> /etc/sudoers && \
    echo "ansible:ansible123" | chpasswd

# Set up .ssh directory
RUN mkdir -p /home/ansible/.ssh && \
    chown ansible:ansible /home/ansible/.ssh && \
    chmod 700 /home/ansible/.ssh && \
    ssh-keygen -A

# Configure SSH for public key authentication only
RUN echo "PubkeyAuthentication yes" >> /etc/ssh/sshd_config && \
    echo "PasswordAuthentication no" >> /etc/ssh/sshd_config && \
    echo "PermitRootLogin no" >> /etc/ssh/sshd_config && \
    echo "AuthorizedKeysFile .ssh/authorized_keys" >> /etc/ssh/sshd_config

EXPOSE 22

# logged in user will be ansible
# landing directory is /home/ansible
USER ansible
WORKDIR /home/ansible

# Start SSH daemon
# want ansible user to run the sshd service
CMD ["sudo", "/usr/sbin/sshd", "-D"]