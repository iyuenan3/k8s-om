Upload OCP cluster SSH private key to all OCP master node(s). Following is one example to upload cluster SSH private key to one master node by manual from your jumper server or any machine which can connect to OCP cluster. Repeat same configuration for other master nodes if target OCP cluster is multiple nodes.

```bash
$ scp -i your_cluster_ssh_private_key your_cluster_ssh_private_key core@your_cluster_node_ip:~/.ssh/id_rsa
$ ssh -i your_cluster_ssh_private_key your_cluster_ssh_private_key core@cluster_master_node_ip chmod 600 /var/home/core/.ssh/id_rsa
```