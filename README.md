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
  
  
