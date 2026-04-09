# HashiCorp packages on RHEL 9 compatible (AlmaLinux 9)
# Configures the official HashiCorp RPM repository and lists available packages on run.
FROM almalinux:9

RUN dnf install -y dnf-plugins-core && \
    dnf config-manager --add-repo https://rpm.releases.hashicorp.com/RHEL/hashicorp.repo && \
    dnf makecache

RUN mkdir -p /output

# Lists the latest available version of each HashiCorp package (name<TAB>version)
# and writes the result to /output/hashicorp-packages-rhel9.txt as well as stdout.
CMD ["bash", "-c", "dnf --disablerepo=* --enablerepo=hashicorp list available 2>/dev/null \
  | grep -v '^Available\\|^Last' \
  | awk 'NF>=2 {print $1\"\\t\"$2}' \
  | sort -t$'\\t' -k1,1 -k2,2Vr \
  | awk -F'\\t' '!seen[$1]++' \
  | tee /output/hashicorp-packages-rhel9.txt"]
