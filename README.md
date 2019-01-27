# Use Homestead for Magento 2 Development
## Objective
Laravel Homestead is a development environment built for Laravel development. Magento 2 is a very similar application so we will be able to use Homestead without too much modification for Magento 2 Development. 

## Prepare your computer for Homestead
The first step is to make sure you have an SSH key for your current user. To find out, open `terminal` and type `cat ~/.ssh/id_rsa`. If you get: `cat: ~/.ssh/id_rsa: No such file or directory` then proceed to the next step to generate a key, otherwise continue on.

### 1. Generate an SSH Key
Refer to https://help.github.com/articles/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent/ for generating an SSH key and adding to the SSH Agent.

### 2. Install Virtualbox
Visit https://www.virtualbox.org/wiki/Downloads to download the latest version of Virtualbox

### 3. Install Vagrant
Visit https://www.vagrantup.com/downloads.html to download the latest version of Vagrant

### 4. Clone the Homestead Repository
The first step is to clone the Homestead repository:
 `git clone https://github.com/laravel/homestead.git ~/Homestead`

Then checkout a tagged version of homestead so there are no unintended upgrades made.

`cd ~/Homestead`
`git checkout v8.0.1`

### 5. Generate a Homestead.yaml file
In the homestead directory (`cd ~/Homestead`) run `bash init.sh`

Next, customize your `Homestead.yaml` file to meet your requirements. [Here is a sample `Homestead.yaml` file that I use](Homestead.yaml) 

### 6. Run Vagrant
In the homestead directory run `vagrant up`. This will boot the virtual machine and provision it. 

### 7. Add the domain to your hosts file
In terminal, edit your hosts file: `sudo nano /etc/hosts` and add this to the end: `192.168.20.10 magento2.test`

Make sure the IP address you type in matches the one in your `Homestead.yaml` file. 

## Create an Nginx file for Magento 2
[Here is a sample nginx file for Magento 2](magento2.test), assuming you have used the example names above.

## Restart nginx
In terminal, make sure you are connected to your vagrant box: `cd ~/Homestead` and `vagrant ssh`

Then type `sudo service nginx restart`

If there is no output from that command then it was successfully restarted. If you get an error, follow the instructions in the error to see where the error occurred. 

