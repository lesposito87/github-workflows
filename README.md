# github-workflows

This repo contains the following reusable GitHub Workflows:

- [Build and Push Multi-Arch Docker Image (`docker-multiarch-build.yml`)](#dockermultiarchbuild)
- [Ansible Lint (`ansible-lint.yml`)](#ansiblelint)
- [Auto-Update Ansible Roles README with "docsible" (`docsible.yml`)](#docsible)

<br>

## Build and Push Multi-Arch Docker Image (`docker-multiarch-build.yml`) <a name="dockermultiarchbuild"/>

This reusable GitHub Actions workflow builds and pushes multi-arch Docker images (AMD64 and ARM64) to Docker Hub. It supports automatic version tagging for main branch pushes and PR-based temporary tagging.

### 🔧 Features / What it does?

- **Multi-arch build** using Docker Buildx and QEMU (supports `linux/amd64` and `linux/arm64`)
- **Automatic version bumping** (patch version) when pushing to the `main` branch
- **Pull Request builds** with temporary tags (e.g. `pr-123`)
- **Auto-deletes PR tags** on PR closure to keep your Docker Hub clean
- **Docker Hub login** support via secrets

---

### 📥 Inputs

| Name         | Description                                               | Required |
|--------------|-----------------------------------------------------------|----------|
| `docker_repo` | Docker Hub repository (e.g. `lesposito87/poorman-aws-playground`)        | ✅ Yes    |

---

### 🔐 Secrets

| Name               | Description                           | Required |
|--------------------|---------------------------------------|----------|
| `DOCKERHUB_USERNAME` | Your Docker Hub username              | ✅ Yes    |
| `DOCKERHUB_TOKEN`    | A Docker Hub [access token](https://hub.docker.com/settings/security) with write access | ✅ Yes    |

---

### 📦 Tagging Strategy

| Trigger        | Behavior                                     | Tag(s) Applied         |
|----------------|----------------------------------------------|------------------------|
| Push to `main` | Bumps the latest semantic version (patch) and adds `latest` tag | e.g. `1.0.3`, `latest` |
| PR open/sync   | Builds with tag based on PR number          | e.g. `pr-123`          |
| PR closed      | Deletes the associated `pr-` Docker tag     |                        |

---

### 🧩 Usage Example

To use this workflow in another repository, create a workflow file like `.github/workflows/docker-multiarch-build.yml`:
```
name: Build and Push Multi-Arch "poorman-aws-playground" Docker Image
on:
  push:
    branches:
      - main

  pull_request:
    types:
      - opened
      - synchronize
      - reopened
      - closed
    branches:
      - '**'

jobs:
  docker-multiarch-build:
    uses: lesposito87/github-workflows/.github/workflows/docker-multiarch-build.yml@v1.0.7
    with:
      docker_repo: lesposito87/poorman-aws-playground
    secrets:
      DOCKERHUB_USERNAME: ${{ secrets.DOCKERHUB_USERNAME }}
      DOCKERHUB_TOKEN: ${{ secrets.DOCKERHUB_TOKEN }}
```

<br>

## Ansible Lint (`ansible-lint.yml`) <a name="ansiblelint"/>

This reusable GitHub Actions workflow performs linting on your Ansible code using [`ansible-lint`](https://ansible-lint.readthedocs.io/).

### 🔧 Features / What it does?

- Checks out your code.
- Dynamically extracts the repository name.
- Copies your project into a subdirectory (required by `ansible-lint`).
- Sets up Python.
- Installs Ansible and `ansible-lint` via pip.
- Runs `ansible-lint` on your entire project.

---

### 🧩 Usage Example

To use this workflow in another repository, create a workflow file like `.github/workflows/ansible-lint.yml`:
```
name: Ansible Lint

on:
  pull_request:

jobs:
  ansible-lint:
    uses: lesposito87/github-workflows/.github/workflows/ansible-lint.yml@v1.0.0
```

<br>

## Auto-Update Ansible Roles README with "docsible" (`docsible.yml`) <a name="docsible"/>

This reusable GitHub Actions workflow automatically updates your `README.md` file using [`docsible`](https://github.com/melezhik/docsible).

### 🔧 Features / What it does?

- Checks out your repository.
- Sets up Python 3.
- Installs `docsible` via pip.
- Generates and updates your `README.md` based on the structure and metadata of your Ansible role.
- Automatically commits and pushes the updated `README.md` to the repository.

---

### 🧩 Usage Example

To use this workflow in another repository, create a workflow file like `.github/workflows/docsible.yml`:

```
name: Auto-Update README with "docsible"

on:
  push:
    branches:
      - main
    paths-ignore:
      - 'README.md'

permissions:
  contents: write # needed to use $GITHUB_TOKEN

jobs:
  docsible:
    uses: lesposito87/github-workflows/.github/workflows/docsible.yml@v1.0.0
```
