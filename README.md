# Use Homestead for Magento 2 Development
## Objective
Laravel Homestead is a development environment built for Laravel development. Magento 2 is a very similar application so we will be able to use Homestead without too much modification for Magento 2 Development. 

## Prepare your computer for Homestead
The first step is to make sure you have an SSH key for your current user. To find out, open `terminal` and type `cat ~/.ssh/id_rsa`. If you get: `cat: ~/.ssh/id_rsa: No such file or directory` then proceed to the next step to generate a key, otherwise continue on.

The following steps assume that you are using a folder with the path `/username/Sites` or `~/Sites` to house your cloned repositories. Substitute whatever different path you may be using throughout the steps whenever you see reference to this filepath.

### 1. Generate an SSH Key
Refer to https://help.github.com/articles/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent/ for generating an SSH key and adding to the SSH Agent.

### 2. Install Virtualbox
Visit https://www.virtualbox.org/wiki/Downloads to download the latest version of Virtualbox

### 3. Install Vagrant
Visit https://www.vagrantup.com/downloads.html to download the latest version of Vagrant. Think of Vagrant as a server that uses VirtualBox as a dependency. Since Vagrant is serving our development sites, it will be necessary to access it using an ssh tunnel. We'll get to that later.

### 4. Clone the Homestead Repository
Clone the Homestead repository:

```sh
git clone https://github.com/laravel/homestead.git ~/Sites/Homestead
```

The cloned repo will then live in `~/Sites/Homestead` - notice the capitalization of the Homestead folder. Then checkout a tagged version of Homestead so there are no unintended upgrades made.

```sh
cd ~/Sites/Homestead
git checkout v8.2.0
```

### 5. Generate a Homestead.yaml file
In the homestead directory (`cd ~/Sites/Homestead`) run `bash init.sh`

Next, customize your `Homestead.yaml` file to meet your requirements. [Here is a sample `Homestead.yaml` file that I use](Homestead.yaml). Let's break down a couple of those configuration options.

```
folders:
    - map: ~/Sites
      to: /home/vagrant/sites
```

The 'folders' section maps the folder on your Mac which contains your cloned repositories to a folder inside the Vagrant instance. Note that the Vagrant folder name is lower-case.

```
sites:
    - map: magento2.test
      to: /home/vagrant/sites/magento2/public_html
```

The 'sites' section maps a domain that you would use in your browser to access your development site to a folder on the Vagrant machine, a.k.a. the server that is serving up your site. It's important to note that due to the `folders` config we set up, `~/Sites` on your Mac is the same thing as `/home/vagrant/sites` in the Vagrant box. `magento2.test` is a new site we're using to prove out these setup steps. When you work on new sites, they would be added to this sites configuration like so:

**e.g.:**
```
sites:
    - map: magento2.test
      to: /home/vagrant/sites/magento2/public_html
    - map: drupal.test
      to: /home/vagrant/sites/drupal/web
```

Remember, Homestead needs the _Vagrant_ filepath to our code, not the Mac filepath, so `home/vagrant/sites` is really a path to `~/Sites`.

### 6. Run Vagrant
In the Homestead directory run `vagrant up`. This will boot the virtual machine and provision it. 'Provisioning' means it reads all the configuration we have set up in our `Homestead.yaml` file.

### 7. Add the domain to your hosts file
In terminal, edit your hosts file: `sudo nano /etc/hosts` and add this to the end:

`192.168.20.10 magento2.test`.

Make sure the IP address you type in matches the one in your `Homestead.yaml` file. 

Performing this step ensures that when the browser reads the address `http://magento2.test`, it uses the provided IP address and Vagrant serves up the site.

## Create an Nginx file per type of php framework
At this point an nginx file has been generated in the Vagrant box. Access the Vagrant box by navigating to `~/Sites/Homestead` and typing `vagrant ssh`.

You will find a generated nginx file at `/etc/nginx/sites-available/magento2.test` to provision a Laravel site. It is a Laravel site because that is what Homestead is built to spin up by default. But Homestead is also capable of handling other php-based sites such as Drupal, Wordpress, and Magento. This is acheived with a custom nginx file per type of framework and requires us to manually replace the contents of the nginx file with the appropriate configuration.

For example, [here is a sample nginx file for Magento 2](magento2.test), assuming you have used the example names above.

## Create an Upstream file
In your `nginx` directory, there should be a directory named `conf.d`. Any file named `*.conf` will be loaded by nginx.

In your Vagrant ssh terminal session, navigate to `/etc/nginx/conf.d` and enter the command `sudo nano upstream.conf`. Copy the contents of the included [upstream.conf](upstream.conf) in this repository into this new file. Then save and close. 

This file defines the various upstream variables for using different versions of PHP. For Magento 2.3, we will use `fastcgi_72` which uses `PHP 7.2`. 

## Restart nginx
In terminal, make sure you are connected to your vagrant box: `cd ~/Homestead` and `vagrant ssh`.

Then type `sudo service nginx restart`

If there is no output from that command then it was successfully restarted. If you get an error, follow the instructions in the error to see where the error occurred. 

## Load some content
Remember the `sites` config from the Homestead.yaml file in Step #5? On your Mac, navigate to the folder you specified as the path for the domain. If you're following these steps verbatim, it'd be `~/Sites/magento2/public_html` - we're using the Mac path this time because we'll actually be doing work where our development files would live.

Create a file named `index.php` with the following contents:

```
<?php phpinfo(); ?>
```

Go to your browser and visit `http://magento2.test`.

If all is working as expected you should see a printout of php specs.
