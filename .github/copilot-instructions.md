# Copilot instructions for `hashicorp-packages`

## Build and run commands

This repository does not define separate lint or test tooling. The working interface is Docker Compose:

```bash
# Build every image
docker compose build

# Build and run every platform, writing host output files under ./output/
docker compose up --build

# Run a single platform
docker compose run --rm rhel8
docker compose run --rm rhel9
docker compose run --rm rhel10
docker compose run --rm ubuntu-jammy
docker compose run --rm ubuntu-noble

# Build a single platform image
docker compose build rhel9
```

When validating a change, treat `docker compose run --rm <service>` as the smallest useful per-platform check.

## High-level architecture

The repository is a small matrix of containerized package-repository probes:

- `docker-compose.yml` is the top-level orchestrator. Each service maps directly to one target OS/repository combination and bind-mounts `./output:/output`.
- `docker/<platform>/Dockerfile` owns all platform-specific behavior. The `RUN` steps configure the official HashiCorp package repository for that distro family.
- The container `CMD` is the actual data-generation step: it prints the latest available HashiCorp packages to stdout and also writes the same tab-separated data to `/output/hashicorp-packages-<platform>.txt` on the host.

There are two implementation families:

- **RHEL-family images (`rhel8`, `rhel9`, `rhel10`)** use `yum`/`dnf` plus the shared RPM repo URL `https://rpm.releases.hashicorp.com/RHEL/hashicorp.repo`, then query `list available`.
- **Ubuntu-family images (`ubuntu-jammy`, `ubuntu-noble`)** install the HashiCorp APT key and source, then read `/var/lib/apt/lists/*hashicorp*Packages` directly to enumerate package/version pairs.

## Key repository conventions

- Keep the service name, Dockerfile directory, and output filename aligned. Example: `rhel9` maps to `docker/rhel9` and writes `hashicorp-packages-rhel9.txt`.
- Every service is pinned to `platform: linux/amd64` in Compose. Preserve that unless the repo intentionally broadens architecture support.
- The output contract is `package-name<TAB>latest-version`, one line per package. The command pipelines sort versions in descending order and deduplicate by package name to keep only the newest version.
- On RHEL images, repository queries intentionally disable all other repos and strip package-manager header noise before sorting.
- On Ubuntu images, the apt source uses `$(lsb_release -cs)` rather than a hardcoded codename, so base image and repo configuration stay in sync.
- `output/` is generated host state and is gitignored; changes to output format should keep both stdout and `/output/...txt` behavior consistent.
