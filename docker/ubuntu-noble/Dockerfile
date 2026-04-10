# HashiCorp packages on Ubuntu 24.04 LTS (Noble Numbat) - latest LTS
# Configures the official HashiCorp APT repository and lists available packages on run.
FROM ubuntu:24.04

ENV DEBIAN_FRONTEND=noninteractive

RUN apt-get update && \
    apt-get install -y gnupg wget lsb-release lz4 && \
    wget -O- https://apt.releases.hashicorp.com/gpg | \
        gpg --dearmor | \
        tee /usr/share/keyrings/hashicorp-archive-keyring.gpg > /dev/null && \
    echo "deb [signed-by=/usr/share/keyrings/hashicorp-archive-keyring.gpg] https://apt.releases.hashicorp.com $(lsb_release -cs) main" | \
        tee /etc/apt/sources.list.d/hashicorp.list && \
    apt-get update

RUN mkdir -p /output

# Parses the compressed HashiCorp APT Packages index to emit the latest version of each
# package (name<TAB>version) and writes to /output/hashicorp-packages-ubuntu-noble.txt.
CMD ["bash", "-c", "lz4 -dc /var/lib/apt/lists/*hashicorp*Packages.lz4 2>/dev/null \
  | awk '/^Package:/{pkg=$2} /^Version:/{print pkg\"\\t\"$2}' \
  | sort -t$'\\t' -k1,1 -k2,2Vr \
  | awk -F'\\t' '!seen[$1]++' \
  | tee /output/hashicorp-packages-ubuntu-noble.txt"]
