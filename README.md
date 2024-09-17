## Lab 1

- What inventory parameters can be used to establish a local connection instead of ssh in Ansible?
localhost              ansible_connection=local
- What value we must set for ansible_connection parameter to connect to a Windows server?
ansible_connection=winrm

- We have a sample inventory file called inventory. It has 3 servers listed, add another server called server4.company.com in this file.
```bash
web1 
web2 
```

Then added the aliases named web1, web2 and web3 for the first three hosts respectively. Update this inventory file to add an alias called db1 for server4.company.com host.

```bash
web1 ansible_host=server1.company.com
web2 ansible_host=server2.company.com
```

- As per the details given in the table below, you can see that, the web servers are linux based hosts and the db server is a Windows machine.

	Update the inventory to add a similar entry for server4.company.com host. Find the required details from the table below.

		---------------------------------------------------------------------------
		|  Alias |        HOST         | Connection | User          | Password     | 
		---------------------------------------------------------------------------
		|  web1  | server1.company.com |    ssh     | root          | Password123! |
		---------------------------------------------------------------------------
		|  web2  | server2.company.com |    ssh     | root          | Password123! |
		---------------------------------------------------------------------------
		|  web3  | server3.company.com |    ssh     | root          | Password123! |
		---------------------------------------------------------------------------
		|  db1   | server4.company.com |    winrm   | administrator | Dbp@ss123!   |
		---------------------------------------------------------------------------







- We have inventory file and added a group called web_servers for web servers. Similarly, add a group called db_servers for database servers.

	```bash
	# Sample Inventory File

	# Web Servers
	web1 ansible_host=server1.company.com ansible_connection=ssh ansible_user=root ansible_ssh_pass=Password123!
	web2 ansible_host=server2.company.com ansible_connection=ssh ansible_user=root ansible_ssh_pass=Password123!
	web3 ansible_host=server3.company.com ansible_connection=ssh ansible_user=root ansible_ssh_pass=Password123!

	# Database Servers
	db1 ansible_host=server4.company.com ansible_connection=winrm ansible_user=administrator ansible_password=Password123!


	[web_servers]
	web1
	web2
	web3
	```





- Let us now create a group of groups. Create a new group called all_servers and add the previously created groups web_servers and db_servers under it.
	hint: search for parent group and children
		```bash
		# Sample Inventory File

		# Web Servers
		web1 ansible_host=server1.company.com ansible_connection=ssh ansible_user=root ansible_ssh_pass=Password123!
		web2 ansible_host=server2.company.com ansible_connection=ssh ansible_user=root ansible_ssh_pass=Password123!
		web3 ansible_host=server3.company.com ansible_connection=ssh ansible_user=root ansible_ssh_pass=Password123!

		# Database Servers
		db1 ansible_host=server4.company.com ansible_connection=winrm ansible_user=administrator ansible_password=Password123!


		[web_servers]
		web1
		web2
		web3

		[db_servers]
		db1
		```




- Update the inventory file to represent the data given in the below table in Ansible Inventory format.


		-------------------------------------------------------------------
		| Server Alias |  Server Name  |  OS    |     User      | Password |
		-------------------------------------------------------------------
		| sql_db1      | sql01.xyz.com | Linux  |     root      | Lin$Pass |
		-------------------------------------------------------------------
		| sql_db2      | sql02.xyz.com | Linux  |     root      | Lin$Pass |
		-------------------------------------------------------------------
		| web_node1    | web01.xyz.com | Win    | administrator | Win$Pass |
		-------------------------------------------------------------------
		| web_node2    | web02.xyz.com | Win    | administrator | Win$Pass |
		-------------------------------------------------------------------
		| web_node3    | web03.xyz.com | Win    | administrator | Win$Pass |
		-------------------------------------------------------------------

	 Group the servers together based on this table

		--------------------------------------------------------
		|    Group         |  Members                          |
		--------------------------------------------------------
		|    db_nodes      |  sql_db1, sql_db2                 |
		--------------------------------------------------------
		|   web_nodes      |  web_node1, web_node2, web_node3  |
		--------------------------------------------------------
		|    boston_nodes  |  sql_db1, web_node1               |
		--------------------------------------------------------
		|    dallas_nodes  |  sql_db2, web_node2, web_node3    |
		--------------------------------------------------------
		|   us_nodes       |  boston_nodes, dallas_nodes       |
		--------------------------------------------------------


## Lab 2


1- If we use the following inventory, on which hosts will Ansible install the httpd package using the given playbook?


[webserver]
web1
web2
[appserver]
app1
app2
app3

---
- name: Setup apache
  hosts: webserver
  tasks:
    - name: install httpd
      yum:
        name: httpd
        state: installed

- name: Setup tomcat
  hosts: appserver
  tasks:
    - name: install httpd
      yum:
        name: tomcat
        state: installed


on web1 and web2

2- what commands can you use to run an Ansible playbook named install.yaml?

ansible-playbook -i ./inventory.ini ./install.yaml

3- A sample playbook named update_service.yml is shown below, it is supposed to update a service on your servers.

                  - hosts: all
                    tasks:
                      - name: Install a new package
                        apt:
                          name: new_package
                          state: present
                  
                      - name: Update the service
                        service:
                          name: my_service
                          state: restarted
                  
                      - name: Check service status
                        service:
                          name: my_service
                          state: started

		what command would you use to run update_service.yml playbook in check mode?

ansible-playbook update_service.yml --check




4- Consider again the same sample playbook named update_service.yml as shown below.
    Let's suppose you have already ran this playbook on your server. Now, once you run this playbook in check mode against same server, which tasks would result in changed status?

          A. Install a new package
          B. Update the service
          C. Check service status
          D. All of the tasks


none of them 

-------------------------------------------------------------

5- There is another sample playbook named configure_database.yml that modifies a configuration file on your database servers. The initial code sample is as follows:
    - hosts: all
      tasks:
        - name: Set max connections
          lineinfile:
            path: /etc/postgresql/12/main/postgresql.conf
            line: 'max_connections = 500'

        - name: Set listen addresses
          lineinfile:
            path: /etc/postgresql/12/main/postgresql.conf
            line: 'listen_addresses = "*"'

what command would you use to run the configure_database.yml playbook in both check mode and diff mode?
ansible-playbook -i configure_database.yml --check --diff

-------------------

6- Consider again the same sample playbook named configure_database.yml as shown below \
	what is the command to check syntax error
ansible-lint configure_database.yml
what command would you use to run ansible-lint on the database_setup.yml playbook?
ansible-lint database_setup.yml

----------------------------------------------------------------------------------------


7- Consider again the same sample playbook named database_setup.yml as shown below.

      ```bash
      - name: Database Setup Playbook
        hosts: db_servers
        tasks:
          - name: Ensure PostgreSQL is installed
            apt:
              name: postgresql
              state: latest
              update_cache: yes

          - name: Start PostgreSQL service
            service:
              name: postgresql
              state: started

          - copy:
              src: /path/to/pg_hba.conf
              dest: /etc/postgresql/12/main/pg_hba.conf
            notify:
              - Restart PostgreSQL
      ```
After running ansible-lint on the playbook, which of the following issues might you expect to see [make sure to select two choice]?

    A. Incorrect indentation.
    B. Deprecated 'apt' module.
    C. Missing 'name' attribute for a task.
    D. Use of a blacklisted command.

A. Incorrect identation
C. Missing name for a task

--------------------------
8- You've been given feedback from ansible-lint about potential issues in your hypothetical webserver_setup.yml playbook. The feedback mentions issues with indentation, deprecated modules, and missing name attributes.
Which of the following is NOT a recommended action based on the feedback?

    A. Correcting the indentation in the playbook.
    B. Replacing deprecated modules with their newer counterparts.
    C. Ignoring the feedback and proceeding with playbook execution.
    D. Adding 'name' attributes to tasks that are missing them.


	C
-------------------------------

9- If ansible-lint provides no output after checking a playbook, what does it indicate?

A. The playbook has syntax errors.
B. The playbook is empty.
C. The playbook adheres to best practices and has no style-related issues.
D. ansible-lint failed to check the playbook.

c. No errors

-----------------------------------

10-Solve issues in below playbook

```bash
---
- hosts: localhost
  become: yes
  tasks:
    - name: 'Execute a date command on localhost'
      command: date

```
name: Date Setting
hosts: localhost
  become: yes
  tasks:
    - name: 'Execute a date command on localhost'
      command: date



11-Create an Ansible playbook to install Nginx on a group of servers. Ensure the service is started and enabled at boot.


[Playbook](lab2/playbook1.yml)



## Lab 3 

- Write the playbook  to add a task to start httpd service on all nodes defined in inventory file.
    hint: Use the service module.
[Playbook](lab3/playbook1.yml)
- Create a playbook that uses the file module to create a directory structure on multiple hosts. Ensure the directories have specific permissions, ownership, and that certain files are absent in these directories.
[Playbook](lab3/playbook2.yml)
- Use the lineinfile module to ensure that a specific line exists in a configuration file on all hosts. For example, add a line to /etc/hosts only if it’s not already there.
[Playbook](lab3/playbook3.yml)
- Write a playbook that uses the docker_container module to start a container with a specific image (e.g., nginx) and ensures the container is always running.
[Playbook](lab3/playbook4.yml)
- Create a role named nginx_setup that installs and configures Nginx on any system. The role should ensure that the nginx.conf file is properly set up, the service is running, and the firewall allows traffic on port 80.
[The tasks file ](lab3/nginx_setup/tasks/main.yml)
- Create a role that configures a basic LAMP stack (Linux, Apache, MySQL, PHP). Ensure each service is installed and properly configured. For example, MySQL should have a database and user created, and Apache should serve a test PHP file.
[Tasks file](lab3/lamp_setup/tasks/main.yml)
- Given the following sample Ansible playbook, write handler will correctly restart the web server when the configuration file is changed?
    - name: Update web server configuration
      hosts: webservers
      tasks:
        - name: Copy web server configuration file
          copy:
            src: /path/to/webserver.conf
            dest: /etc/webserver/webserver.conf
          notify: Restart web server
		


[Playbook](lab3/playbook5.yml)