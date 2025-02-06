# Nessus Agent Installation Ansible Role

## Overview
This Ansible role automates the installation of the Tenable Nessus agent across different Linux distributions and architectures.

## Features
### Version Control
* Default version is set to `10.8.2`, minimum version required to fix autoupdate issue
* Checks existing version and only updates if needed, `>= 10.8.2`
* Supports version comparison to ensure minimum version requirements

### OS Support
* Handles Amazon Linux 2, Rocky 8 and 9, and can support other `rpm` based packages by extending `nessus_agent_packages` and `nessus_agent_checksums` vars
* Uses system facts to determine correct package
* Architecture-aware (currently set up for x86_64)

### Security
* Checksum verification for downloaded packages
* Uses secure HTTPS downloads from the official Tenable download site

### Installation Management
* Checks if agent is already installed
* Performs version comparison
* Can handle fresh installs and upgrades
* Supports removal if needed, use `--tags uninstall`

## Role Variables
* `nessus_agent_version`: Specify the Nessus agent version (default: `10.8.2`)
* `nessus_agent_packages`: Optional map of install packages for various Linux `RPM`-based distro
* `nessus_agent_checksums`: Optional map of checksums for package verification

## Requirements
* Ansible 2.16+
* Internet connectivity to download Nessus agent package
* SSH connectivity is allowed from source to destination

## Example Playbooks
### Basic
```yaml
- name: Playbook to use default vars
  hosts: all
  become: true
  roles:
    - role: ansible-role-nessus-agent
```

### Override default vars
```yaml
- name: Playbook to support Amazon 2023 instances
  hosts: all
  become: true
  roles:
    - role: ansible-role-nessus-agent
      vars:
        nessus_agent_packages:
          x86_64:
            Amazon:
              "2": "NessusAgent-{{ nessus_agent_version }}-amzn2.x86_64.rpm"
              "2023": "NessusAgent-{{ nessus_agent_version }}-amzn2.x86_64.rpm"
            Rocky:
              "8": "NessusAgent-{{ nessus_agent_version }}-el8.x86_64.rpm"
              "9": "NessusAgent-{{ nessus_agent_version }}-el9.x86_64.rpm"
        nessus_agent_checksums: #checksum must match with those in https://www.tenable.com/downloads/nessus-agents
          x86_64:
            Amazon:
              "2": "sha256:9c48b8668d5f97403b35fc7f6beaeb8bb2554af0ed05c83abd699516ce405510"
              "2023": "sha256:9c48b8668d5f97403b35fc7f6beaeb8bb2554af0ed05c83abd699516ce405510"  
            Rocky:
              "8": "sha256:12ca7c81e25928952627fcffbe2c400fbb496743409195bc7fe1e52c0931c7de"
              "9": "sha256:287e3e48954f460f0f60c1f40b5055665b25ef633dae4a595d54bd414de336b4"  
```

## Development
### Maintain this role
1. (Recommended) Run `pre-commit run -a` locally to validate and fix any potential code issues before pushing changes to git repo.
    1. To install and configure pre-commit, Go [here](https://pre-commit.com/).
    ```shell
    localmachine$> pre-commit run -a
    ```
    _Sample Output:_
    ```terminaloutput
    check for added large files..............................................Passed
    check for merge conflicts................................................Passed
    check vcs permalinks.....................................................Passed
    forbid submodules....................................(no files to check)Skipped
    don't commit to branch...................................................Failed
    - hook id: no-commit-to-branch
    - exit code: 1
    fix end of files.........................................................Passed
    trim trailing whitespace.................................................Passed
    check yaml...............................................................Passed
    check for merge conflicts................................................Passed
    check that executables have shebangs.................(no files to check)Skipped
    check json...........................................(no files to check)Skipped
    pretty format json...................................(no files to check)Skipped
    fix requirements.txt.................................(no files to check)Skipped
    check for case conflicts.................................................Passed
    detect aws credentials...................................................Passed
    detect private key.......................................................Passed
    ```
2. Commit and push changes.
3. Create a Merge request (pull request) for peer review.
4. Once the code changes have successfully promoted to `main` branch, `git tag` appropriately with semver, e.g., `v1.2.3`
