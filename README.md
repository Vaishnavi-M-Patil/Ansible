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

```
ansible -i hosts all -u ubuntu --private-key=./id_rsa -m shell -a hostname
```
The Ansible command is used for running the hostname command on all hosts listed in your inventory file using the shell module. 

**-i hosts-**	Specifies the inventory file named hosts.
**all-**	Target group/hosts (here, all hosts in the inventory).
**-u ubuntu-**	user name.
**--private-key=./id_rsa-**	Path to the SSH private key for authentication.
**-m shell-**	Use the shell module to run shell commands.
**-a hostname-**	The argument to the shell module — in this case, the **hostname** command.

### 1stansible.yaml playbook(conmfiguration file):
```yaml
- name: Message print
  hosts: all
  tasks:
  - name: Print message
    debug:
      msg: "Hello, World!"
```

### Command used to executes an Ansible playbook 
```
ansible-playbook 1stansible.yaml
```
