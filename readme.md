# Basic Lamp setup with configurable vhosts and MySQL databases using Ansible

## Introduction
In order to better learn server provisioning using [Ansible](http://docs.ansible.com/), I decided to build
a playbook that can be used to create a basic LAMP server on Ubuntu 14.04 and allows for customization of Apache
virtual hosts and MySQL databases and users. Please report any bugs or suggestions via the issues section of the
[GitHub Project Page](https://github.com/rdustin/ansible-lamp).

Currently this script has only been tested when running from a Windows machine (host) since that is what I had available
during development; however, with minor changes in the Vagrant file it should run from any OSX or Linux machine (host) as well.
See the headings inside the Getting Started section for more information.

## Getting Started
You must have Vagrant installed to use this project. If you do not have
Vagrant installed and set up, please refer to the following resource
before continuing.
https://docs.vagrantup.com/v2/getting-started/index.html.


Download or clone this repository from GitHub

### Using this script on a Windows machine
This should work without any changes if you are running this from a Windows host machine.

### Using this script on an  OSX or Linux machine
To use this on an OSX or Linux host machine open the Vagrant file included in this project and
find the comment that says: ```provision the server (OSX/Linux)``` and follow the instructions
in the comments.

## Web: Apache and PHP
*Setting up virtual hosts:*
You can add as many sites as you want to the server by leveraging Vagrant's
built in file syncing capabilities and configuring ansible variables. By default two sample sites are included
for your reference called sample-site and sample-site2.

__Step 1: Set up your files on your host (local) machine__
The default folder that is synchronized is located inside the
{project-folder-name}/www/sites folder, with {project-folder-name} being whatever
name you chose when you cloned the repository from GitHub. These files will
be editable by you on your local machine with the changes showing up immediately
on the server.

__Step 2: Define your virtual hosts__
In your editor open the webservers variable file located at
{project-folder-name}/provisioning/playbooks/group_vars/webservers.
You will see a variable called enabled_vhosts with two definitions for
sample-site and sample-site2 that look like the following:
```
enabled_vhosts:
 - { server_name: 'sample-site.dev', document_root: '/var/www/html/sample-site' }
 - { server_name: 'sample-site2.dev', document_root: '/var/www/html/sample-site2' }
 ```
Feel free to change the server_name values to match the site name you want to use.
Change the folder name in the document_root values to match the folder name you have
in the {project-folder-name}/www/sites folder.

__NOTE:__ You will need to manually edit your local machine's hosts file to match
your server ip with the server_name values defined above. For example, if I'm on
a Windows machine as my local computer I would need to open my
`"C:\Windows\System32\drivers\etc\hosts"` file and add the following line:
`192.168.30.117 sample-site.dev sample-site2.dev`

Alternatively, you can install the Vagrant [Hostupdater plugin](https://github.com/cogitatio/vagrant-hostsupdater)
and configure your virtual host definitions in the Vagrantfile.

*Disable a virtual host*
If you want to __disable a virtual host__ you defined before you can do so by adding
the definitions to the same webservers variable file were we define our active virtual
hosts. It will look like:
```
disabled_vhosts:
 - { server_name: 'sample-site2.dev'}
```
__NOTE:__ The provisioning will fail if the site has not been defined

*Enabling and disabling Apache modules*
Similarly to enabling virtual hosts above, you may define any apache modules you
like to have enabled or disabled by putting them in the webservers variable file
located at {project-folder-name}/provisioning/playbooks/group_vars/webservers.
For example we could enable mod rewrite and disable it by using the following definition:
```
enabled_modules:
 - rewrite

disabled_modules:
- rewrite
```

## Mysql
*Creating databases and users:* You can add as many databases and users as you want to the server
by configuring ansible variables. By default two databases (sampledb & sampledb2) and users (sampleuser & sampleuser2) are
included for your reference.

__Adding a Database__: In your editor open the dbservers variable file located at {project-folder-name}/provisioning/playbooks/group_vars/dbservers.
Under the mysql_vars variable you will see a variable called databases with the two sample database names that looks like the following:
```
mysql_vars:
    databases:
        - sampledb
        - sampledb2
```
Please change the database names to what you want them to be. You can have as many databases as you would like to have.

__Adding a User__: In your editor open the dbservers variable file located at {project-folder-name}/provisioning/playbooks/group_vars/dbservers.
Under the mysql_vars variable you will see a variable called users with the two sample users names that looks like the following:
```
users:
    sampleuser:
        priv: sampledb.*:ALL
        password: abc123
        state: present
    sampleuser2:
        priv: sampledb2.*:ALL/sampledb.*:SELECT
        password: abc123
        state: present
```
Please change the user names to what you want them to be. You can have as many users as you would like to have. You have the following
settings available for each user:
* __Give database access and set privileges__: You can set which databases the user has access to and which privileges on each database.
In our example above, sample user only has access to the sampledb database and has all privileges to all tables on that database.
Sampleuser2, on the other hand, has all privileges and access to sampledb2 but only have select privileges on sampledb. For more examples
privileges see [this Ansible document](http://docs.ansible.com/mysql_user_module.html#examples).

* __Set user password__: You can set the user's password by changing the value abc123 to whatever value you would like the password to be.

* __Create or Delete user__: If you change state above to absent instead of present it will delete the user.