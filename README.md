# hashicorp-packages

A Docker-based project that configures the official [HashiCorp package repositories](https://www.hashicorp.com/en/official-packaging-guide) for multiple Linux platforms and lists the available HashiCorp packages on each.

## Platforms

| Service        | Base image      | Package manager | HashiCorp repo |
|----------------|-----------------|-----------------|----------------|
| `rhel8`        | AlmaLinux 8     | yum             | RPM (`RHEL/8`) |
| `rhel9`        | AlmaLinux 9     | dnf             | RPM (`RHEL/9`) |
| `rhel10`       | AlmaLinux 10    | dnf             | RPM (`RHEL/10`) |
| `ubuntu-noble` | Ubuntu 24.04 LTS | apt            | APT (`noble`)  |
| `ubuntu-jammy` | Ubuntu 22.04 LTS | apt            | APT (`jammy`)  |

## Project layout

```
.
├── docker-compose.yml          # Orchestrates all 5 platform containers
└── docker/
    ├── rhel8/Dockerfile        # RHEL 8 compatible (AlmaLinux 8)
    ├── rhel9/Dockerfile        # RHEL 9 compatible (AlmaLinux 9)
    ├── rhel10/Dockerfile       # RHEL 10 compatible (AlmaLinux 10)
    ├── ubuntu-noble/Dockerfile # Ubuntu 24.04 LTS
    └── ubuntu-jammy/Dockerfile # Ubuntu 22.04 LTS
```

## Usage

### List available HashiCorp packages on all platforms

```bash
docker compose up --build
```

Each service will build its image (configuring the HashiCorp repo during the build) and then print the list of available HashiCorp packages for that platform.

### Run a single platform

```bash
# RHEL 8
docker compose run --rm rhel8

# RHEL 9
docker compose run --rm rhel9

# RHEL 10
docker compose run --rm rhel10

# Ubuntu 24.04 LTS (Noble)
docker compose run --rm ubuntu-noble

# Ubuntu 22.04 LTS (Jammy)
docker compose run --rm ubuntu-jammy
```

### Build images without running

```bash
docker compose build
```

## How it works

Each Dockerfile:

1. **Starts from** the official base image for the target platform.
2. **Adds the HashiCorp package repository** using the method documented in the [official HashiCorp packaging guide](https://www.hashicorp.com/en/official-packaging-guide):
   - **RHEL-based**: installs `yum-utils` / `dnf-plugins-core`, adds `https://rpm.releases.hashicorp.com/RHEL/hashicorp.repo`, and refreshes the metadata cache.
   - **Ubuntu-based**: installs the HashiCorp GPG key, adds the HashiCorp APT source, and refreshes the package lists.
3. **Default command** prints the list of packages available from the HashiCorp repository so you can see what is available for that platform without installing anything.