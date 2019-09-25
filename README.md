# Laravel Starter

[![Build Status](https://travis-ci.org/msztorc/laravel-starter.svg?branch=master)](https://travis-ci.org/msztorc/laravel-starter)
[![License](https://img.shields.io/badge/license-MIT-brightgreen.svg?style=flat-square)](https://www.opensource.org/licenses/MIT)

<img style="float: right; height:30px; margin-left:20px;" src="https://www.vagrantup.com/assets/images/mega-nav/logo-vagrant-a7ab5898.svg">
<img width="35" style="float:right; margin-left:20px;" alt="Ansible logo" src="https://upload.wikimedia.org/wikipedia/commons/thumb/2/24/Ansible_logo.svg/64px-Ansible_logo.svg.png">
<img style="float: right; height:30px; margin-left:10px;" src="https://laravel.com/img/logotype.min.svg">
<img style="float: right; height:30px;" src="https://laravel.com/img/logomark.min.svg">

Quick and easy Laravel deployments using Ansible provisioning with Vagrant support for local environments.

**Default environment setup**

- ubuntu 18.04 (bionic64)
- laravel:latest
- php 7.3
- mysql 5.7
- nginx

### Vagrant deployment with Ansible local provisioning

Clone this repo to `my-project` folder (of course you can choose any name you want)

```bash
git clone https://github.com/msztorc/laravel-starter.git my-project
```

```bash
cd my-project
```

Adjust your config files like project name, passwords, etc... - see below for more details.

After that just run your machine to start auto-provisioning

```bash
vagrant up
```

### Adjusting variables of your vm and project

Configurations are stored in files:

[provisioning/group_vars/all](provisioning/group_vars/all)

```yaml
web_root: /var/www/my-project # project path
web_host: my-project.host # project domain
db_root_pass: p4ssvv0rD # database root password
app_db_name: my-project # application database name
app_db_user: my-project # application database user
app_db_pass: p4ssvv0rD # application database user password
```

[provisioning/hosts](provisioning/hosts)

```ini
[local]
localhost	ansible_connection=local ansible_ssh_private_key_file=/vagrant/.vagrant/machines/default/virtualbox/private_key

[sandbox]
SANDBOX_IP_HERE
```

Quick ```my-project``` replace

``$ find ./ -type f -exec sed -i 's/my-project/fancy-project/g' {} \;``

#### Vagrant

Vagrant configurations are stored in [Vagrantfile](Vagrantfile)

Box default config

__IP & hostname__

```ruby
    config.vm.network "private_network", ip: "10.10.10.10"
    config.vm.hostname = "my-project"
```

__Resources (default: memory - 2GB, vCPUs - 2)__

```ruby
   config.vm.provider "virtualbox" do |vb|
     vb.memory = 2048
     vb.cpus = 2
   end
```

__Set synced folder__ (local, destination)

```ruby
  # config.vm.synced_folder "../data", "/vagrant_data"
```

If you want to set synced folder mapping local project to box webroot, you have to uncomment this line:

```ruby
config.vm.synced_folder "../GIT/my-project", "/var/www/my-project"
```

**Note**
Remember to run `vagrant reload` every time when Vagrantfile has been updated

Other useful vagrant commands

Check status of all machines

``vagrant global-status``

Halt vagrant box (run in box directory)

``vagrant halt``

Up vagrant box (run in box directory)

``vagrant up``

**Note**
Remember to run `vagrant reload` and `vagrant provision` every time when any provisioning configs were updated

### Xdebug

To integrate Xdebug with PhpStorm perform following steps:

1. Make sure you have ``php7.3-xdebug`` extension installed in within the vagrant box.
2. Append following configuration to the ``/etc/php/7.3/php.ini`` file:
```
[xdebug]
zend_extension=/usr/lib/php/20170718/xdebug.so
xdebug.remote_enable=1
xdebug.remote_port=9000
xdebug.profiler_enable=1
xdebug.remote_host=10.10.10.10
```
3. Execute command: ``sudo service php7.3-fpm restart``.
4. Install ``Xdebug helper`` extension for Chrome (or some kind of equivalent for your browser).
5. In PhpStorm:
 * Press <kbd>Ctrl+Shift+A</kbd> and search ``Web Server Debug Validation``. 
 * Choose ``Local Web Server or Shared Folder``.
 * Determine ``Path to create validation script`` like so: ``/<path>/<to>/<project>/my-project/public``
 * Determine ``Url to validation script`` like so: ``http://my-project.host``.
 * After clicking ``Validate`` button you should receive some information with green ticks. If not - you probably messed up with previous settings.
6. Navigate to: ``Languages & Frameworks > PHP > Servers`` and map your local and remote paths. It should be something like: ``my-project -> /var/www/my-project``.
7. Press ``Start Listening for PHP Debug Connections`` button. It should be placed on top right side with phone icon.
8. Place breakpoint somewhere in your code that will be executed in the next request e.g. ``index.php``.
9. In the browser:
 * Make sure you have enabled ``Debug`` option in previously installed browser extension.
 * Refresh application's page.
10. In PhpStorm ``Debug`` panel should appear. You can toggle it using <kbd>Alt+5</kbd>.
11. Enjoy :metal:
