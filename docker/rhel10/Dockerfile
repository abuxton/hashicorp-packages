# HashiCorp packages on RHEL 10 compatible systems.
# Current EL10 userspace images require x86-64-v3, which is not available on all
# hosts, so we use an EL9 userspace while querying the HashiCorp RHEL 10 repo.
FROM almalinux:9

RUN dnf install -y dnf-plugins-core && \
    printf '%s\n' \
      '[hashicorp]' \
      'name=HashiCorp Stable - x86_64' \
      'baseurl=https://rpm.releases.hashicorp.com/RHEL/10/x86_64/stable' \
      'enabled=1' \
      'gpgcheck=1' \
      'gpgkey=https://rpm.releases.hashicorp.com/gpg' \
      > /etc/yum.repos.d/hashicorp.repo && \
    dnf makecache

RUN mkdir -p /output

# Lists the latest available version of each HashiCorp package (name<TAB>version)
# and writes the result to /output/hashicorp-packages-rhel10.txt as well as stdout.
CMD ["bash", "-c", "dnf --disablerepo=* --enablerepo=hashicorp list available 2>/dev/null \
  | grep -v '^Available\\|^Last' \
  | awk 'NF>=2 {print $1\"\\t\"$2}' \
  | sort -t$'\\t' -k1,1 -k2,2Vr \
  | awk -F'\\t' '!seen[$1]++' \
  | tee /output/hashicorp-packages-rhel10.txt"]
