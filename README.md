# Ansible

Introduction To Ad-Hoc Commands

What’s an ad-hoc command?
An ad-hoc command is something that you might type in to do something really quick, but don’t want to save for later.

This is a good place to start to understand the basics of what Ansible can do prior to learning the playbooks language – ad-hoc commands can also be used to do quick things that you might not necessarily want to write a full playbook for.

Generally speaking, the true power of Ansible lies in playbooks. Why would you use ad-hoc tasks versus playbooks?

For instance, if you wanted to power off all of your lab for Christmas vacation, you could execute a quick one-liner in Ansible without writing a playbook.

For configuration management and deployments, though, you’ll want to pick up on using ‘/usr/bin/ansible-playbook’ – the concepts you will learn here will port over directly to the playbook language.
	
How to write an Ansible ad-hoc command?
What you just read above is a simple example of an Ansible ad-hoc command. Now we will move forward and understand its anatomy.

Syntax:-
ansible <hosts> [-m <module_name>] -a <"arguments"> -u <username> [--become]
ansible <hosts> [-m <module_name>] -a <"arguments"> -K

Hosts: 	
It can be any entry in the inventory file. For specifying all hosts in inventory, use all or '*'. Wild card patterns are also accepted.

module_name 	
It's an optional parameter. There are hundreds of modules available in Ansible. By default, it is command. For example, shell, copy, yum, apt, file.

Arguments:
We should pass values that are required by the module. It may change according to the module used.

Username: 	
It specifies the user account in which Ansible can execute commands. User account, SSH.

Become: 	
It's an optional parameter specified when we want to execute operations that need sudo privilege. By default, become is false.
--ask-become-pass, -K ---> ask for privilege escalation password.


[root@nagios1 ~]# ansible common  -m ping -k                 	[--ask-pass: ask for connection password]
[root@nagios1 ~]# ansible common -a "ls -l" -k		[--args: module arguments]
[root@nagios1 ~]# ansible common -a "ip addr show" -k
[root@nagios1 ~]# ansible common -m copy -a "src=/etc/hosts dest=/tmp/hosts" -k   [--module-name: module name to execute default=command]


If you put a -c option, then Ansible will do a dry run of the command. It will not actually be applied on the nodes.


Frequently used modules
command: Used to execute command in bash and see the result
#ansible webservers -m command -a "pwd"

shell: operations like piping and redirects will not work in command. In these situations, you have to use shell module.
#ansible webservers -m shell -a 'echo $LD_PATH'

file: Used to create files, directories, set, or change file permissions and ownership etc.
#ansible webservers -m file -a 'dest=/home/ubuntu/new.txt mode=600 owner=vishnu group=vishnu'
#ansible webservers -m file -a 'dest=/home/ubuntu/new mode=755 state=directory owner=vishnu'

copy: Used for copying files to multiple nodes concurrently
#ansible webservers -m copy -a 'src=/home/user/cfg.ini dest=/home/ubuntu /cfg.ini'

yum/apt: For installation and management of applications
#ansible webservers -m apt -a 'name=python state=present'

user: To add and delete users
#ansible webservers -m user -a 'name=user1 password=somepassword'
#ansible webservers -m user -a 'name=user1 state=absent' (to delete user)

service: To manage services like httpd, mysqld etc.
#ansible dbservers -m service -a 'name=mysqld state=started'
setup: It's used for gathering facts about the hosts
#ansible all -m setup
Patterns:-
ansible <pattern_goes_here> -m <module_name> -a <arguments>

Such as:
[root@ansible ansible]# ansible webservers -m service -a "name=httpd state=restarted" -k


The following patterns are equivalent and target all hosts in the inventory:
all
*
Such as:-
[root@ansible ansible] # ansible all -m service -a "name=httpd state=restarted" -k
[root@ansible ansible]# ansible * -m service -a "name=httpd state=restarted" -k
[root@ansible ansible] # ansible 169.144.82.107 -m service -a "name=httpd state=restarted" -k
[root@ansible ansible] # ansible 169.144.82.* -m service -a "name=httpd state=restarted" -k
[root@ansible ansible] # ansible webservers:phonix  -m service -a "name=httpd state=restarted" -k 

•	You can exclude groups as well, for instance, all machines must be in the group webservers but not in the group phoenix:
[root@ansible ansible] # ansible webservers:!phonix  -m service -a "name=httpd state=started"    -k
[root@ansible ansible] # ansible webservers:!phonix  -m service -a "name=httpd state=restarted" -k
[root@ansible ansible] # ansible webservers:!phonix  -m service -a "name=httpd state=stopped"  -k
[root@ansible ansible] # ansible all  -m service -a "name=httpd state=stopped"  -k

•	To install the bind package
[root@ansible ~]# ansible webservers -m yum -a "name=bind state=present" -k
•	To remove the bind package
[root@ansible ~]# ansible webservers -m yum -a "name=bind state=absent" -k
•	File Transfer
[root@ansible ~]# ansible webservers -m copy -a "src=/etc/hosts dest=/tmp/hosts" -k
•	Set permission over the file
[root@ansible ~]# ansible webservers -m file -a "dest=/root/anaconda-ks.cfg mode=660 owner=user1 group=user1" -k
•	Set permission on directory
[root@ansible ~]# ansible webservers -m file -a "dest=/data  mode=755 owner=user1 group=user1 state=directory" -k
•	Delete directory recursi¬¬¬vely 
[root@ansible ~]# ansible webservers -m file -a "dest=/data state=absent" -k      [state=absent,present,latest]
•	Users and Groups
[root@ansible ~]# ansible webservers -m user -a "name=foo1 password=<foo@123>" -k
[root@ansible ~]# ansible all -m user -a "name=foo1 state=absent" -k

