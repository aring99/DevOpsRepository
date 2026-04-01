# Ansible

Powerful and easy script execution automation.
The default location for the configuration file is /etc/ansible/ansible.cfg if you want to have it in a different folder, you can do it but you should define the location in an environment variable.

If you need different configs, you can overwrite the default ansible.cfg by putting the desired config in the repo of the server, db or anything

When considering the configuration parameters, the ones specified by the environment variable are prioritized and it overwrites the others. 

for minor changes from the default config, instead of copying the entire ansible.cfg file What you could do is override that single parameter using an environment variable.

You can set an environment variable right before executing the Ansible playbook to change that behavior. by changing the whole parameter to uppercase and prefixing ANSIBLE_ to it.

ansible-config-list, ansible-config view, ansible-config dump are important commands. You can find the location of a parameter by running ansible-config dump | grep parametername


Yaml info: To store different information or properties of a single object, we use a dictionary. If we need to add more information about the property, then we add a dictionary inside the dictionary

a list or an array is used when there are multiple items of the same type of object. To merge the mentioned information fully, you make a list of dictionaries.

Dictionaries are unordered and lists are ordered.

in order work with multiple servers, ansible needs to connect them via ssh or powershell remoting. Ansible works agent less, so you don't need to install software on target machines.

The information uses an inventory to store target machine information. The default location is /etc/ansible/hosts
You can group the servers and also give aliases to them. There are other parameters, such as ansible_connection to specify how to connect (ssh for example)

For connection information, the best practice is to use a paswordless ssh key based connection.you can group server groups, too.

For variables, in Ansible we use them with double curly braces, like '{{variablename}}' you don't add the '' if it is used in the middle of something.


Variable precedence. The variable defined in the host level prices the variable defined in the group level. However, the variable defined in the playbook (play level) precedes host level

If you run --extra-vars command on the terminal (pass it in the terminal on run time), it is considered the highest priority value for that variable

if you want to pass the output of a command and pass it as an input to another command in a playbook, add register: variableName on the command that has the output that you want to capture
And put var: samevariablename on the command that is going to take the mentioned output as an input. You can define which specific parts of that output you want to use by adding a "."
For example return code, if it is anything other than 0 it means it has failed. We can use result.rc to specify only the return code to be used.

Variable scopes, host level, play level and global 

Magic variables can be used to use variables between hosts. '{{hostvars['hostname'].varname}}'

You can use groups and group names too.

Ansible facts, this shows information about the target device Ansible connects to, this is part of the ansible setup module. All facts are stored under a variable called ansible_facts


!! You can disable this by defining gather_fact: no in the playbook. You can also disable it by default under /etc/ansible/ansible.cfg, gathering = explicit


Ansible playbooks are ansible's orchestration language. It is where we define what we want Ansible to do. A play defines a set of tasks and a task is an action to be performed.

Don't forget to definene the host in the inventory.yaml

!!Always verify playbooks, so everything is made sure to work. Check mode and diff mode exist. Check mode is a dry run without making any changes on the host, doesn't apply it on them
Diff mode shows before and after running the playbook. There is also a syntax check mode --syntax-check

Ansible lint is a command line tool to check and quality control on ansible playbooks

Conditionals, we can define conditions to run a certain task only when a condition is met with the "when:" such as when red hat use yum and for Debian use apt. Use "==" you can also use and/or


Loops. 	for example, instead of creating numerous tasks for similar objects (such as users with different names), you can write a single task, give it the variables and run the task as a loop

The name of the var for the loop is named "item", so if you are using dictionaries you can use name = '{{item.name}}', item.id,... with_items and loop do the same thing, with_file... also exist

Service start != started, idempotency 

Ansible handlers automate the restarting of the devices and application of the changes in the config file in an infrastructure.

Ansible roles can be used like packages, instead of re-writing code you can call on a role in the playbook, makes it reusable

ansible-galaxy init nameofRole, this is to create the skeleton of your own role

to connect a role to the playbook, you can create a repo called roles and put the role under it or at the default /etc/ansible/roles

Ansible collections for network automation. THey are self contained units and also has roles, modules inside them too

Templating can be used in Ansible playbooks. Jinja2 is a templating engine for python. You can make string manipulations with it.
