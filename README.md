# Ansible
## What is ansible?
- Ansible is an open source configuration management tool that used to automate infrastructure management and configuration.
- Using ansible we can manage thousands of servers with just a single command.
- It is based on push based mechanism.
- configuration file called playbook.
- 22 port number.
- master slave architecture.
- /etc/ansible/  --> main directory of ansible.
- .yaml(yet another markup language) or .yml extension

## How ansible works?
1. Ansible uses playbook written in yaml syntax where we can define all tasks and configurations that we want to apply on all the servers.
2. Along with playbook we need **hosts file or inventory file** which will contains IP address of all servers on which we want to run playbook.
3. After editing playbook and host file, you can run the ansible command which will run playbook on all the servers defined the host file using ssh.

1stansible.yaml:
```yaml


```
