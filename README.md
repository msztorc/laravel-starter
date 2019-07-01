# Laravel Starter (Vagrant)
Laravel clean starter on vagrant with ansible provisioning.

- ubuntu 18.04 (bionic64)
- laravel:latest
- php 7.3
- mysql 5.7
- nginx

### Deploy

Clone vagrant config with ansible playbook to my-project directory

``git clone https://github.com/msztorc/laravel-starter.git my-project``

``cd my-project``

Run machine
``vagrant up``

### Adjusting variables of your vm and project

Quick ```my-project``` replace

``$ find ./ -type f -exec sed -i 's/my-project/fancy-project/g' {} \;``

#### Vagrant

Vagrant configuration is stored in `Vagrantfile`

Box default config

__IP & hostname__
```
    config.vm.network "private_network", ip: "10.10.10.10"
    config.vm.hostname = "my-project"
```

__Resources (default: memory - 2GB, vCPUs - 2)__
```
   config.vm.provider "virtualbox" do |vb|
     vb.memory = 2048
     vb.cpus = 2
   end
```

__Set synced folder__ (local, destination)
```
  # config.vm.synced_folder "../data", "/vagrant_data"
```

If you want to set synced folder mapping local project to box webroot, you have to set:
```
config.vm.synced_folder "../GIT/my-project", "/var/www/my-project"
```

Remember to run `vagrant reload` in box directory every time after Vagrantfile was updated

Other useful vagrant commands

Check status of all machines

``vagrant global-status``

Halt vagrant box (run in box directory)

``vagrant halt``

Up vagrant box (run in box directory)

``vagrant up``

#### Ansible

Provisioning vars and tasks are stored in `playbook.yml`

Default vars:

```
  vars:
    web_root: /var/www/my-project
    web_host: my-project.host
    db_root_name: root
    db_root_pass: $p4ssvv0rD # set your db root password
    app_db_name: my-project
    app_db_user: my-project
    app_db_pass: $p4ssvv0rD # set your db app user password
```
Remember to run `vagrant reload` and after `vagrant provision` in box directory every time after any provisioning configs was updated

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
xdebug.remote_host=10.10.10.1
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
