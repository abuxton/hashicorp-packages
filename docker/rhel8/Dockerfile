# HashiCorp packages on RHEL 8 compatible (AlmaLinux 8)
# Configures the official HashiCorp RPM repository and lists available packages on run.
FROM almalinux:8

RUN yum install -y yum-utils && \
    yum-config-manager --add-repo https://rpm.releases.hashicorp.com/RHEL/hashicorp.repo && \
    yum makecache

CMD ["yum", "--disablerepo=*", "--enablerepo=hashicorp", "list", "available"]
