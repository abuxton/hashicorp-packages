# HashiCorp packages on Ubuntu 22.04 LTS (Jammy Jellyfish) - previous LTS
# Configures the official HashiCorp APT repository and lists available packages on run.
FROM ubuntu:22.04

ENV DEBIAN_FRONTEND=noninteractive

RUN apt-get update && \
    apt-get install -y gnupg wget lsb-release && \
    wget -O- https://apt.releases.hashicorp.com/gpg | \
        gpg --dearmor | \
        tee /usr/share/keyrings/hashicorp-archive-keyring.gpg > /dev/null && \
    echo "deb [signed-by=/usr/share/keyrings/hashicorp-archive-keyring.gpg] https://apt.releases.hashicorp.com $(lsb_release -cs) main" | \
        tee /etc/apt/sources.list.d/hashicorp.list && \
    apt-get update

CMD ["bash", "-c", "grep -h '^Package: ' /var/lib/apt/lists/*hashicorp* 2>/dev/null | sort -u | sed 's/Package: //'"]
