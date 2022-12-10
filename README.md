# Ansible Collection - quera.github

Download and install assets from Github releases page.

**Note:** Do not modify README.md directly.
It is auto-generated by `python generate_readme` in `readme_src` folder.

## Installation

### Install the `quera.github` collection from Github

```shell
ansible-galaxy collection install git+https://github.com/QueraTeam/ansible-github.git
```

### Install the `install_from_github` module as a local custom module

Alternatively, you may manually install the `install_from_github` module itself as a [local custom module](https://docs.ansible.com/ansible/latest/dev_guide/developing_locally.html) instead of installing the module through the `quera.github` Ansible collection. However, it is recommended to use `quera.github` collection unless you have a good reason not to. Here are the commands to install the `install_from_github` module as a local custom module:

```shell
# Create the user custom module directory
mkdir ~/.ansible/plugins/modules

# Install the install_from_github module into the user custom module directory
curl -o ~/.ansible/plugins/modules/install_from_github.py https://raw.githubusercontent.com/QueraTeam/ansible-github/main/plugins/modules/install_from_github.py
```

## Ansible Module - quera.github.install_from_github


This module can be used to select a release from a Github repository, select an asset from that release based on OS and CPU architecture, download the asset, and install files/directories from the asset.



### Options

|    Parameter     |                      Type                      |                                                                                                                                                                                                                                                                                           Description                                                                                                                                                                                                                                                                                           |
|------------------|------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|asset_arch_mapping| Type: `dict`                                   |If the repo uses non-standard strings to specify CPU architecture, you can define a custom mapping between those and standard architectures. For example, if some repo uses `64` instead of `x86_64` or `amd64`, you can set this option to `amd64: "64"` or `x86_64: "64"`.                                                                                                                                                                                                                                                                                                                     |
|asset_regex       | Type: `str` <br/>**Required**                  |A regex for selecting an asset (file name) from all the assets of selected release. If there are multiple assets for different OSes and CPU architectures, you don't need to specify OS (darwin, linux, ...) and architecture (x86_64, amd64, aarch64, arm64, ...) in your regex (just write `.*` in place of them). This module tries to narrow down assets based on the system's OS and CPU architecture.                                                                                                                                                                                      |
|move_rules        | Type: `list` <br/>**Required**                 |You need to specify how individual items from an asset should be moved to the system. Privide a list of rules. Each rule should specify `src_regex` and `dst`, and could specify `mode`, `owner`, `group`. An asset can be a single file, or an archive (`.zip`, `.tar.gz`, ...). When asset is an archive, you select by `src_regex` some paths (directories or files) relative to archive root, and they will move to `dst`. Even if the asset is just a single file (not an archive), you should specify a rule to move that file (`src_regex` can be any regex mathing file name, e.g. `.*`).|
|repo              | Type: `str` <br/>**Required**                  |The name of the repository in the format `user_or_org/repo_name`.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                |
|tag               | Type: `str`  <br/>Default: `latest`            |The tag to select from releases page. The default (`latest`) means the most recent non-prerelease, non-draft release.                                                                                                                                                                                                                                                                                                                                                                                                                                                                            |
|version_command   | Type: `str`                                    |The command to get the currently installed version. (e.g. `some_command --version`) If the output of this command matches the selected asset, downloading and installing the asset is skipped.                                                                                                                                                                                                                                                                                                                                                                                                   |
|version_file      | Type: `path`                                   |Path to a file containing the version of currently installed version. The module reads the version from this file instead of `version_command` before installing (to skip download if the desired version is installed) and writes the installed version to this file after successful installation. This is useful for non-executable assets which don't have any `--version` command (e.g. fonts, ...).                                                                                                                                                                                        |
|version_regex     | Type: `str`  <br/>Default: `\d+\.\d+(?:\.\d+)?`|A regex for extracting version from the output of `version_command` or tag name. The default is to match 2 or 3 numbers joined by `.`. E.g. 1.12.7 or 1.12                                                                                                                                                                                                                                                                                                                                                                                                                                       |


#### Note

- If you pass `version_file`, you can't pass `version_command` or `version_regex` options.

### Examples

```yaml

- name: install latest version of lego (ACME client)
  quera.github.install_from_github:
    repo: go-acme/lego
    asset_regex: lego.*\.tar\.gz
    version_command: lego --version
    move_rules:
      - src_regex: lego
        dst: /usr/local/bin
        mode: 0755

```