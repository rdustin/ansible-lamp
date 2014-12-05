# Basic Lamp setup with configurable vhosts and databases

## Getting Started
You must have Vagrant installed to use this project. If you do not have
Vagrant installed and set up, please refer to the following resource
before continuing.
[https://docs.vagrantup.com/v2/getting-started/index.html]


Download or clone this repository from GitHub


## Web: Apache and PHP
*Setting up virtual hosts:*
You can add as many sites as you want to the server by leveraging Vagrant's
built in file syncing capabilities. By default two sample sites are included
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

Alternatively, you can install the Vagrant [https://github.com/cogitatio/vagrant-hostsupdater (Hostupdater plugin)]
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