### Ansible use cases
- Config Management
- App Deployment
- Provisioning
- Continuous Delivery
- Security and Compliance
- Orchestration

### Basics - 
**Playbooks** contains **plays**
Plays contains **tasks**
Tasks call **module**

**Tasks** run **sequentially**

**Handlers** are triggered by tasks and run once at the end of plays.

**Roles** are special playbooks that are self contained with tasks, variable, configurations templates and other supporting files.

### Example playbook with 1 play, 3 tasks and a handler
```
--- # 3 dashes at the dop of the file indicates that this is YAML file
- name: install and start apache 
  hosts: web # Specifies which inventory gruoup this playbook is to be applied
  remote_user: alice
  remote_method: sudo # Directive for escalates previliages on target system
  become_user: root
  vars:
    http_port: 80
    max_client: 200
  
  tasks:
  - name: install httpd
    yum: name=httpd state=latest # module standard structure (module: directive1=value directive2=value)
  - name: write apache config file
    template: src=srv/httpd.j2 dest=/etc/httpd.conf # Retrives the variables defined at the top of playbook
    notify: # Calls handlers
    - restart apache
  - name: start httpd
    service: name=httpd state=running
  
  - handlers: restart apache
    service: name=httpd state:restarted
```



### Basics of YAML
##### Key Value Pair

```
Fruit: Mango
Vegetable: Carrot
Liquid: Water
```


##### Array/Lists
```
Fruits:
  - Orange
  - Apple
  - Banana
  - Watermelon

Vegetables:
  - Carrot
  - Tomato
  - Mango
```  

##### Dictionary/Map
```
Banana:
  Calories: 100
  Protein: 20
  Fat: 10

Watermelon:
  Calories: 80
  Protein: 15
  Fat: 5
```  

##### List of dictionaries
```
- Color: Yellow
  Model:
    Name: TVSStar
    Model: 2005
  Trnasmission: Manual
  Price: $4000
    
- Color: Red
  Model:
    Name: Honda
    Model: 2007
  Trnasmission: Automatic
  Price: $5000    
  ```
  #### Setup and sample example of Ansible
  Provision Control machine and other web app hosts (app01, app02, db01, lb01) in a same Vagrantfile. 
  ```
  git clone https://github.com/darshandeshmukh11/ansible
  cd ansible
  vagrant up
  ```
  ssh into control machine and execute below commands to install Ansible
  ```
  vagrant@vagrant-ubuntu-trusty-64:~$ sudo apt-get install software-properties-common
  $ sudo apt-add-repository ppa:ansible/ansible
  $ sudo apt-get update
  $ sudo apt-get install ansible
  ```
  
  List out all the hosts on control machine
  ```
  $ ansible --list-hosts all  # Located at cat /etc/ansible/hosts
  ```
  Contents of `hosts` can be configured as below 
  `$ cat hosts` 
  ```
  host0.example.org ansible_host=172.18.0.2 ansible_user=root
  host1.example.org ansible_host=172.18.0.3 ansible_user=root
  host2.example.org ansible_host=172.18.0.4 ansible_user=root
  ```
  `ansible_host` is a special variable that sets the IP ansible will use when trying to connect to this host.

  `ansible_user` is another special variable that tells ansible to connect as this user when using ssh. By default ansible would use       your current username, or use another default provided in ~/.ansible.cfg (remote_user).

  Ansible stores this configuration at ```/etc/ansible/ansible.cfg``` 
  To overwrite this configuration - 
  ```
  $ touch ansible.cfg
  $ vi ansible.cfg
  Add below content and save the file 
  [defaults]
  inventory = ./dev #dev will hold your host information and can be a prt of your source code
  ```
  Now you dont need to use -i option to read default configuration
  ```
  $ ansible --list-hosts all
  [WARNING]: Found both group and host with same name: control

  hosts (5):
    control
    db01
    app01
    app02
    lb01
 
  vagrant@vagrant-ubuntu-trusty-64:/vagrant$ ansible --list-hosts control
  [WARNING]: Found both group and host with same name: control

  hosts (1):
    control
 vagrant@vagrant-ubuntu-trusty-64:/vagrant$ ansible --list-hosts database
 [WARNING]: Found both group and host with same name: control

  hosts (1):
    db01
 vagrant@vagrant-ubuntu-trusty-64:/vagrant$ ansible --list-hosts webserver
 [WARNING]: Found both group and host with same name: control

  hosts (2):
    app01
    app02
``` 
##### Playbooks
Create a new folder (called playbooks) and go ahead to add a new playbook (hostname.yml) with below content 
```
---
  - hosts: all
    tasks:
      - command: hostname

$ ansible-playbook playbooks/hostname.yml
```

##### Ansible Modules
Modules can be executed with `-a <module-name>` flag
`e.g. ansible -i hosts -m shell -a 'uname -a' host0.example.org`

##### Ansible Groups
Grouping hosts
Hosts in inventory can be grouped arbitrarily. For instance, you could have a debian group, a web-servers group, a production group,
etc...
```
[debian]
host0.example.org
host1.example.org
host2.example.org
```
This can even be expressed shorter:
```
[debian]
host[0:2].example.org
```
If you wish to use child groups, just define a [groupname:children] and add child groups in it. For instance, let's say we have various flavors of linux running, we could organize our inventory like this:
```
[ubuntu]
host0.example.org

[debian]
host[1:2].example.org

[linux:children]
ubuntu
debian
```
Grouping of course, leverages configuration mutualization.

##### Looping Ansible commands
Ansible provides a more readable way to write this. Ansible can loop over a series of items, and use each item in an action like this:
```
...
- name: Installs necessary packages
apt: pkg={{ item }} state=latest
with_items:
  - apache2
  - libapache2-mod-php
  - git
...
```
