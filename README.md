# Ansible
## What is ansible?
- Ansible is an open source configuration management tool that used to automate infrastructure management and configuration.
- Using ansible we can manage thousands of servers with just a single command.
- It is based on push based mechanism.
- 22 port number.
- master slave architecture.
- /etc/ansible/  --> main directory of ansible.
- ansible.cfg --> A configuration file that controls Ansible’s behavior and default settings.
- ansible playbook (*.yml) --> A YAML file that defines a set of tasks to be executed on managed nodes.

## yaml(yet another markup language):
- Easy to read and write
- Start with --- (Optional but good practice) and end with ... (optional Only used if you're combining multiple YAML documents in one file).
- .yaml or .yml file extension
- Use '-' for lists
- Key-value pairs with :
- Indentation is critical
   → Use spaces only, no tabs
   → Typically 2 spaces
- Strings with special characters need quotes
- Modules use dictionaries
```yaml
---
- name: Install and start Nginx          #Describes what the play or task does.
  hosts: webservers                      #Target group of servers defined in your inventory.
  become: yes                            #Run tasks with sudo/root privileges.

  tasks:                                #List of actions to perform.
    - name: Install Nginx
      apt:                                #Module used to manage packages (for Debian/Ubuntu).
        name: nginx                       
        state: present
        update_cache: yes                #Runs apt-get update before installation.

    - name: Start and enable Nginx
      service:                          #Module used to manage system services.
        name: nginx
        state: started
        enabled: yes                    #Enable service at boot	
...
```

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

### Command used to executes an Ansible playbook:
```
ansible-playbook 1stansible.yaml
```
| command | usecase|
| ----- | ------ |
| ansible | Ad-hoc commands are one-liner commands used to perform quick tasks on remote systems without writing a full playbook. |
| ansible-playbook | Run a YAML playbook with multiple tasks, logic, and roles |

## what is lineinfile, blockinfile, tags in ansible:
In Ansible, lineinfile and blockinfile are modules used to manage the contents of files on a target system, especially for configuration management. 

### lineinfile module:
The lineinfile module in Ansible is used to add, modify, or remove lines in a file. The lineinfile module looks for a specific line in a file and replaces it with a predefined regular expression.

### blockinfile module:
blockinfile inserts, modifies, or deletes one or several lines of text located between two marker lines in a file.

### tags:
Tags in Ansible allow you to run specific parts of a playbook rather than executing everything. 

```yaml
- name: add line in index.html
      lineinfile:
        path: /var/www/html/index.html
        line: "Hello this line is added in this file"
      tags: lineinfile

    - name: Add block in index.html
      blockinfile:
        path: /var/www/html/index.html
        block: |
          <h2>Hello world!</h2>
          <p>Hello world!</p>
        marker: "# {mark} ANSIBLE MANAGED BLOCK"
      tags: blockinfile
```
