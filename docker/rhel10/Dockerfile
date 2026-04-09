# HashiCorp packages on RHEL 10 compatible (AlmaLinux 10)
# Configures the official HashiCorp RPM repository and lists available packages on run.
FROM almalinux:10

RUN dnf install -y dnf-plugins-core && \
    dnf config-manager --add-repo https://rpm.releases.hashicorp.com/RHEL/hashicorp.repo && \
    dnf makecache

CMD ["dnf", "--disablerepo=*", "--enablerepo=hashicorp", "list", "available"]
