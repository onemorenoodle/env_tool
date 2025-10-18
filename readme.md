# env_tool User Guide

## Overview

`env_tool` is a CLI utility for managing multiple Conda environments in a reproducible way. It helps you synchronize environments from `environment.yaml` files, freeze environments to `explicit.txt` files, and recreate environments from those files. This workflow is ideal for teams and projects that require consistent, versioned environments.

## Installation

The dev container automatically installs `env_tool` and links it to your PATH:

```
"postCreateCommand": "chmod +x ${containerWorkspaceFolder}/env_tool && ln -sf ${containerWorkspaceFolder}/env_tool /usr/local/bin/env_tool"
```

**Important:**  
To ensure proper permissions for environment management, set `"remoteUser": "root"` in your `devcontainer.json`:

```jsonc
"remoteUser": "root"
```

You can download the latest version via:

```
curl -L -o env_tool https://raw.githubusercontent.com/onemorenoodle/env_tool/refs/heads/main/env_tool
```

## Usage

All environments are expected to be organized under the `conda/` directory, each with its own subdirectory containing an `environment.yaml` and/or `explicit.txt`.

### Commands

#### 1. `sync <env_name>`
Synchronize creates the specified Conda environment from its `environment.yaml`.

**Example:**
```
env_tool sync myenv
```

#### 2. `sync_all`
Synchronize all environments found in `conda/` that have an `environment.yaml`.

**Example:**
```
env_tool sync_all
```

#### 3. `freeze <env_name>`
Export the exact package list of the specified environment to `explicit.txt` in its directory.

**Example:**
```
env_tool freeze myenv
```

#### 4. `freeze_all`
Export `explicit.txt` for all environments in `conda/` that have an `environment.yaml`.

**Example:**
```
env_tool freeze_all
```

#### 5. `sync_static <env_name>`
Recreate the specified environment from its `explicit.txt` file.

**Example:**
```
env_tool sync_static myenv
```

#### 6. `sync_static_all`
Recreate all environments in `conda/` that have an `explicit.txt`.

**Example:**
```
env_tool sync_static_all
```

#### 7. `version`
Print the current version of `env_tool`.

**Example:**
```
env_tool version
```

#### 8. `prune`
Remove every Conda environment (except `base`) that lacks an `environment.yaml` in `conda/`.

**Example:**
```
env_tool prune
```

## Best Practices & Intended Workflow

1. **Environment Source Control:**  
   Store each environment in its own subdirectory under `conda/`, with both `environment.yaml` and (optionally) `explicit.txt`.

2. **Syncing Environments:**  
   Use `sync` or `sync_all` to create environments from `environment.yaml`.  
   This is best for development and when you want flexibility in package versions.

3. **Freezing Environments:**  
   After installing or updating packages, use `freeze` or `freeze_all` to generate `explicit.txt`.  
   This file captures the exact package versions for reproducibility.

4. **Recreating Environments:**  
   Use `sync_static` or `sync_static_all` to recreate environments from `explicit.txt`.  
   This is ideal for production, deployment, or sharing environments with others.

5. **Version Control:**  
   Commit both `environment.yaml` and `explicit.txt` to your repository.  
   Update `environment.yaml` for general changes, and regenerate `explicit.txt` after any package updates.

6. **Collaboration:**  
   Team members should use `sync_static` for guaranteed reproducibility, or `sync` for flexibility.

7. **Pruning Environments:**  
   Run `prune` periodically to delete Conda environments that are not tracked by an `environment.yaml` in `conda/`. The `base` environment is never removed.

**Recommended Workflow:**  
- Create your `environment.yaml` specifying the main packages and pin versions or ranges as needed.
- Run `env_tool sync <env_name>` to create or update the environment from the YAML file.
- To modify the environment, edit the YAML and run `sync` again.
- Before committing, run `env_tool sync_static <env_name>` (or `env_tool sync_static_all`) to save an exact snapshot (`explicit.txt`) of your environment for reproducibility.

## Example Directory Structure

```
conda/
  myenv/
    environment.yaml
    explicit.txt
  anotherenv/
    environment.yaml
    explicit.txt
```

