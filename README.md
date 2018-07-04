# WordPress Flawless Deployment Ansible Playbook
Ansible playbook for flawless latest WordPress + Nginx + PHP7.2 + MariaDB10 + Postfix server deployment

### Credits
Credits to @tucsonlabs for his great Ansible Wordpress + Nginx repo.

[https://github.com/tucsonlabs/ansible-playbook-wordpress-nginx](https://github.com/tucsonlabs/ansible-playbook-wordpress-nginx)

I've forked his work, made some changes applicable for me and for usage with Ubuntu 18.04 mainly.

### Changes and differences from @tucsonlabs repo
 - changed DB from MySQL server to MariaDB 10
 - changed PHP version to 7.2 particulary
 - now dowlonading always the latest Wordpress version, not particulary version `4.9.5`
 - changed Wordpress installation directory from `/srv/www` to `/var/www/html/`
 - installing additional PHP7.2 modules, including `php7.2-fpm`
 - removing default `/var/www/html/index.nginx-debian.html` and `/etc/nginx/sites-enabled/default` files to avoid loading them
 - applying changes to `/etc/php/7.2/fpm/php.ini` to lines `upload_max_filesize` and `post_max_size` to increase limits to `100MB`
 - added ansible task to restart not only nginx server but `php7.2-fpm` service also, after changes in `php.ini` are applied
 - entirely changed default nginx `wrodpress.conf` for simple deployment.
 - on Database creation, added string to create the database initially with `utf8` encoding

With all those changes, in the end when the execution of the playbook is finised, you have started and running nginx, php7.2-fpm, mariadb, postfix and wordpress, deployed on top of them.

All you need after that is go to your web server IP address and continue the installation from the web interface!
Enjoy!

### Requirements
- Ansible 2.0.0 or newer
- Ubuntu 16.04* / 18.04 (installed on your web server or virtual machine)
- *`PHP 7.2` (it is included by default in 18.04 repositories, but it is needed to be additionally installed on 16.04!!!) 

*On Ubuntu 16.04 LTS the PHP version, which is in the repository by default is 7.0. This playbook is set to use PHP7.2 by default, so if you want to use it on Ubuntu 16.04, please, install PHP7.2 first!!!

### Instructions:

### 1. Configure your web server for ssh

Allow connections from your development machine to the web server over ssh. This is essential for ansible to work, so make sure to configure your remote or local server to allow connections via ssh. You may find `ssh-copy-id` helpful. 

You can skip the step below if you're not using vagrant and replace `192.168.100.10` with your websever's IP address, but make sure you're able to SSH into your web server before continuing.

If you're using vagrant add these lines to your **Vagrantfile**:

```
config.vm.network :forwarded_port, guest: 80, host: 4567
config.vm.network "private_network", ip: "192.168.100.10"
```

This allows a connection to the machine over ssh on the specified IP address. `"192.168.100.10"` can be swapped out for a different IP, but make sure it matches whatever is set in your ansible inventory file. 

Run `vagrant ssh-config` to see where your key is stored, and create or update your host machine's `~/.ssh/config` file. It should looks something like this with your IdentityFile switched out:

```
Host 192.168.100.10
  StrictHostKeyChecking no
  UserKnownHostsFile /dev/null
  IdentitiesOnly yes
  User vagrant
  IdentityFile /Users/joshua/Boxes/bento/ubuntu-16.04/.vagrant/machines/default/virtualbox/private_key
  PasswordAuthentication no
```

### 2. Clone the repository

```
$ git clone https://github.com/Bandic007/flawless-wordpress.git
$ cd flawless-wordpress/
```

### 3. Set the web server IP address

For your convinience I've already moved `hosts.example`  file to `hosts` file.
Just edit the `hosts` file with your favorite text editor and change the IP address there to your web server IP address or URL:

```
[webservers]
192.168.100.10
```

### 4. Run the playbook

```
$ ansible-playbook playbook.yml -i hosts -u YOUR_REMOTE_USER_ID -K
```

This tells ansible to use the inventory file we've called "hosts". If you're using vagrant you can run the same command as above but exclude the username and sudo prompt:

```
$ ansible-playbook playbook.yml -i hosts
```

### 5. Finish the install

Open your web browser and navigate to [http://192.168.100.10](http://192.168.100.10) (or your webserver's IP) to finish the WordPress installation.
