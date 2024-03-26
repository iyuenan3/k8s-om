## Install ansible in Rocky Linux server
```bash
$  sudo -i

$ wget https://bootstrap.pypa.io/get-pip.py   #Only needed if pip3 is not installed in your server!!!

$ python3 get-pip.py  #Only needed if pip3 is not installed in your server!!!

$ pip3 install ansible==2.10.5
```

Following 3 steps are optional, you can execute them if you can't execute the ansible playbooks CLIs!!!

```bash
$ pip3 install kubernetes==17.17.0

$ pip3 install aicli

$ ansible-galaxy collection install community.kubernetes
```