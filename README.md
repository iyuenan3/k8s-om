- [Overview](#overview)
- [Prerequisites](#prerequisites)
  - [K8S cluster configurations](#K8S-cluster-configurations)
    - [Configure K8S SSH key](#configure-K8S-ssh-key)
  - [Jumper server configurations](#jumper-server-configurations)
    - [Prepare jump server](#prepare-jump-server)
    - [Install ansible](#install-ansible)
    - [Configure k8s-om](#configure-k8s-om)
- [Configurations](#configurations)
    - [Cluster level configurations](#cluster-level-configurations)
      - [Install additional common commands](#install-additional-common-commands)
      - [Configure python command soft link](#configure-python-command-soft-link)
    - [Tenant level configurations](#tenant-level-configurations)
      - [Create tenant users](#create-tenant-users)
      - [Delete tenant users](#delete-tenant-users)
- [Limitations](#limitations)

# Overview

This project contains a few [`ansible`](https://www.ansible.com/) playbooks for Kubernetes Operation and Maintenance (O&M). Currently it has following main functionalities:

- Configure tenant for K8S
- Configure python soft link for K8S
- Install helm3 for K8S
- Install additional command binaries for K8S

Welcome to raise your requirements by creating issues if you see some O&M related tasks are valuable to be implemented here.


# Prerequisites

## K8S cluster configurations

### Configure K8S SSH key

Upload K8S cluster SSH private key to all master node(s), which is used to enable SSH without password from master node to other node(s) in same cluster. For detailed configuration steps, please refer to `k8s-om/docs/configure_ssh_key.md`. Skip this configuration if already done during K8S post configuration phase.

## Jumper server configurations
### Prepare jump server

Prepare a machine (Windows, Linux, whatever), which has network connectivity with the target infrastructure clusters you want to manage. How to set up this kind of machine is out of scope here.

### Install ansible

For detailed installation steps, please refer to `k8s-om/docs/install_ansible.md`. Skip this installation if it already installed.

### Configure k8s-om

Update host inventory info in `k8s-om/hosts` file, including cluster IP under `[K8S]` section and admin username/password under `[K8S:vars]` section.

# Configurations

### Cluster level configurations

#### Install additional common commands

Install `sshpass`/`wget`/`unzip` common commands for all node(s), which are convenient for daily work.
```bash
$ ansible-playbook -i hosts configure-cmd-binaries.yml
```
Check if following cmds are installed successfully on all node(s).
```bash
$ for cmd in {sshpass,wget,unzip};do whereis $cmd;done
sshpass: /var/usrlocal/bin/sshpass
wget: /var/usrlocal/bin/wget
unzip: /var/usrlocal/bin/unzip
```

#### Configure python command soft link
Configure python3 executable command soft link in `/usr/local/bin` directory of all node(s).
```bash
$ ansible-playbook -i hosts configure-python.yml
```

Check if python3 executable command soft link configured successfully of target node(s).
```bash
$ ls -la /usr/local/bin/python3
lrwxrwxrwx. 1 root root 31 Nov 30 10:53 /usr/local/bin/python3 -> /usr/libexec/platform-python3.6
$ python3 --version
Python 3.6.8
```

### Tenant level configurations

#### Create tenant users

Configure tenant users to share same infrastructure platform and isolate resources from each other, including Linux user, SSH key, K8S namespace, K8S RBAC, kube config, helm config, etc.

E.g. create 6 tenants from index 1 (k8suser1) to 6 (k8suser6) with password `systeM!23`.
```bash
$ ansible-playbook -i hosts configure-tenant.yml -e '{first_user_index: 1, last_user_index: 6, configure_action: create}'
```

The main default configurations of tenant for test environments:
| Configuration              |
| -------------------------- |
| Linux user                 |
| Linux user SSH key         |
| Kube config                |
| Helm config                |
| K8S namespace              |
| K8S RBAC                   |

#### Delete tenant users
E.g. delete 6 tenants from index 1 (k8suser1) to 6 (k8suser6).
```bash
$ ansible-playbook -i hosts configure-tenant.yml -e '{first_user_index: 1, last_user_index: 6, configure_action: delete}'
```

# Limitations

    1. Two different types of tenants can't be created on same container infrastructure platform, which will result in configuration conflicts.
    2. The type of created tenants can't be changed on the fly that means created tenants shall be deleted and recreated again with new tenant type.

