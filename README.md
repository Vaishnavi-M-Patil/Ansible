# Ansible
## ✅ What is ansible?
- Ansible is an open source **configuration management tool** that used to automate infrastructure management and configuration.
- Using ansible we can **manage thousands of servers with just a single command**.
- It is based on **push based mechanism**.
- **Agentless:** No need to install agents on managed nodes.
- Uses **SSH (Port 22)** for communication.
- **master slave architecture:**
   - **Control Node:** Runs Ansible commands and playbooks.
   - **Managed Nodes:** Remote servers managed by Ansible (no agents needed).
- /etc/ansible/  --> main directory of ansible.
- ansible.cfg --> A configuration file that controls Ansible’s behavior and default settings.
- hosts --> Inventory file defining groups and IPs of managed nodes
- ansible playbook (*.yml) --> A YAML file that defines a set of tasks to be executed on managed nodes.

![Screenshot (409)](https://github.com/Vaishnavi-M-Patil/AnsibleRepo/blob/main/diagram.jpg)

## ✅ yaml(yet another markup language):
- Easy to read and write
- YAML stands for yet another markup language or YAML ain’t markup language 
- Start with --- (Optional but good practice) and end with ... (optional Only used if you're combining multiple YAML documents in one file).
- .yaml or .yml file extension
- Comments can be identified with a pound or hash symbol (#).
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

## ✅ How ansible works?
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

### 1stansible.yaml playbook(configuration file):
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


## ✅ variables:
### local variable : 
- Defined within a task and used locally        
```yaml
- name: Install service
      apt:
        name: "{{ service }}"
        state: present
      vars:
        service: "nginx"
```
### Global variable:
Global variables are accessible across the entire playbook. 
```yaml
- name: Install and configure Nginx
  hosts: all
vars:
  service: "nginx"
tasks:
  - name: Install service
     apt:
        name: "{{ service }}"
        state: present
```
### Variable File:
External file that contains variables.
```yaml
- name: Install and configure apache2
  hosts: all
  vars_files:
    - ./var.txt
  tasks:
    - name: Install service
       apt:
          name: "{{ service }}"
          state: present
```
var.txt:
```
service: "apache2"
```

### command line variable:
```
ansible-playbook -e service=apache2 ansible.yaml
```

### prompt variable:
- Used to ask the user for input when the playbook runs.
- vars_prompt must be outside the **tasks:** block.
- Prompts are only available at playbook run time, not in ad-hoc commands.
- You can use input anywhere with {{ variable_name }}.
```yaml
- name: Prompt variable
  hosts: all
  vars_prompt:
    - name: movie
      prompt: Enter the movie name
      private: no                         # shows the input
  tasks:
    - name: print movie
       debug:
          msg: "This is {{ movie }}"
```

### register variable:
Stores the output of a command or task.
```yaml
- name: Register variable
  hosts: all
  tasks:
    - name: Get hostname
       shell: hostname
       register: host                           # store output of shell in host variable
    - name: print hostname
       debug: 
          msg: "This is {{ host.stdout }}"
```

### Pass variable from hosts file:
- In Ansible, you can pass variables directly from the hosts inventory file (usually hosts or inventory) by defining them next to the host entries or in group/host variable files.
- It contains least priority
```
192.168.1.10   movie = thar
```
```yaml
- name: Use variable from inventory
  hosts: all
  tasks:
    - debug:
        msg: "Movie name is {{ movie }}"
```

## ✅ Gathering facts:
The command **ansible all -m setup** is used to gather and display system facts (hardware, network, OS details, etc.) from all managed hosts.
```
ansible all -m setup
```
- **all:** Run on all hosts in your inventory.
- **-m setup:** Use the setup module, which collects facts about the system.
To view specific facts, use **-a** with a filter:
```
ansible all -m setup -a 'filter=ansible_hostname'
```
```yaml
- name: Print gathering facts
  hosts: all
  tasks:
    - debug:
        msg: "Hello this is {{ ansible_os_family }}"
```

## ✅ conditions:
Conditions in Ansible are used to **run tasks only when certain criteria are met**, using the *when* keyword.
```yaml
- name: Install Apache on Debian
  apt:
    name: apache2
    state: present
  when: ansible_os_family == "Debian"
```

## ✅ Package module:
The package module is a generic way to manage software packages, regardless of the underlying package manager (like apt, yum, dnf etc.).
| State |	Description |
| ----- | ------ |
| present | Ensures the package is installed. Does nothing if already installed |
| absent |	Ensures the package is removed/uninstalled |
| latest | Ensures the latest version of the package is installed (if available) |
```yaml
- name: Print gathering facts
  hosts: all
  tasks:
    - name:  Install a package if not already installed
      package:
         name: nginx
         state: present
  
   - name: Remove package
     package:
        name: apache2
        state: absent

   - name: Install the latest version of a package
     package:
       name: curl
       state: latest
```

## ✅ privilege in ansible:
- In Ansible, privilege escalation allows you to run tasks as root user, even if you're connected as a regular user.
- If **become: yes** is set in the playbook, then all tasks in the playbook will run with elevated privileges.
```yaml
- name: Install nginx with root privileges
  hosts: webservers
  become: yes                                    # Enable sudo
  tasks:
    - name: Install nginx
      apt:
        name: nginx
        state: present
```
You can also use it per task if only some tasks need sudo:
```yaml
tasks:
  - name: Run a command as root
    command: apt update
    become: yes
```
You can configure privilege (become) globally using the **ansible.cfg** file.

Edit your /etc/ansible/ansible.cfg and add:
```
[defaults]
become = True
```
run an ad-hoc command with privilege:
```
ansible all -m apt -a "name=nginx state=present" -b                  # Here -b = --become
```

## ✅ service module:
- The service module in Ansible is used to start, stop, restart, or enable/disable services on target machines.
- On modern systems (like Ubuntu 18.04+, CentOS 7+), this works with systemd.

| Parameter |	Description |
| ------- |----------- |
| name | Name of the service (e.g., nginx, httpd) |
| state | Desired state: started, stopped, restarted, reloaded |
| enabled |	Whether the service should start at boot: yes / no |

```yaml
- name: Manage nginx service
  hosts: webservers
  become: yes
  tasks:
    - name: Ensure nginx is started
      service:
        name: nginx
        state: started
        enabled: yes
```

### daemon_reload in Ansible's systemd Module:
- The daemon_reload option in the systemd module is used to tell systemd to reload its configuration. 
- Use daemon_reload: yes after:
   - Creating or modifying a systemd service file (/etc/systemd/system/myapp.service)
   - Changing unit file properties (ExecStart, Environment, etc.)
- **daemon_reload** does not restart services.
- It only refreshes systemd's in-memory configuration database.
```yaml
- name: Install and configure a custom service
  hosts: all
  become: yes
  tasks:
    - name: Copy custom systemd unit file
      copy:
        src: myservice.service
        dest: /etc/systemd/system/myservice.service
        mode: '0644'

    - name: Reload systemd daemon
      systemd:
        daemon_reload: yes

    - name: Start and enable the custom service
      systemd:
        name: myservice
        state: started
        enabled: yes
```

## ✅ Loops in Ansible: 
Loops in Ansible are used to repeat a task multiple times with different items.
```yaml
- name: Install multiple packages
  apt:
    name: "{{ item }}"
    state: present
  loop:
    - nginx
    - git
    - curl
```
Loop with dictionaries:
```yaml
- name: Create users with specific shells
  hosts: all
  become: yes
  tasks:
    - name: Add users with custom shells
      user:
        name: "{{ item.name }}"
        shell: "{{ item.shell }}"
        state: present
      loop:
        - { name: 'alice', shell: '/bin/bash' }
        - { name: 'bob', shell: '/bin/sh' }
```

## ✅ Copy module:
The copy module is used to copy files from your Ansible control node (local machine) to the managed hosts.
```yaml
- name: Copy a file to remote server
  copy:
    src: /path/to/local/file.txt
    dest: /path/on/remote/file.txt
    # owner: www-data
    # group: www-data
    # mode: '0644'
```
| Option | Description |
| ------ | ---------- |
| src	| Path to the source file on the control node |
| dest | Path on the managed node to copy the file to |
| owner | Set the file owner on the remote system |
| group | Set the file group on the remote system |
| mode |	Set file permissions (e.g., '0644') |
| content |	Use this instead of src to directly write text into the destination file |
| backup |	Creates a backup before overwriting an existing file |


## ✅ what is lineinfile, blockinfile, tags in ansible:
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
