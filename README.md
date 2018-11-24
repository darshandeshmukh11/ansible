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
    yum: name=httpd state=latest
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
