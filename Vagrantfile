# -*- mode: ruby -*-
# vi: set ft=ruby :

# All Vagrant configuration is done below. The "2" in Vagrant.configure
# configures the configuration version (we support older styles for
# backwards compatibility). Please don't change it unless you know what
# you're doing.
Vagrant.configure("2") do |config|
  # The most common configuration options are documented and commented below.
  # For a complete reference, please see the online documentation at
  # https://docs.vagrantup.com.

  # Every Vagrant development environment requires a box. You can search for
  # boxes at https://atlas.hashicorp.com/search.
  config.vm.box = "box-cutter/ubuntu1604-desktop"

  # Disable automatic box update checking. If you disable this, then
  # boxes will only be checked for updates when the user runs
  # `vagrant box outdated`. This is not recommended.
  # config.vm.box_check_update = false

  # Create a forwarded port mapping which allows access to a specific port
  # within the machine from a port on the host machine. In the example below,
  # accessing "localhost:8080" will access port 80 on the guest machine.
  # config.vm.network "forwarded_port", guest: 80, host: 8080

  # Create a private network, which allows host-only access to the machine
  # using a specific IP.
  # config.vm.network "private_network", ip: "192.168.33.10"

  # Create a public network, which generally matched to bridged network.
  # Bridged networks make the machine appear as another physical device on
  # your network.
  # config.vm.network "public_network", ip: "192.168.0.115"

  # Share an additional folder to the guest VM. The first argument is
  # the path on the host to the actual folder. The second argument is
  # the path on the guest to mount the folder. And the optional third
  # argument is a set of non-required options.
  # config.vm.synced_folder "../data", "/vagrant_data"

  # Provider-specific configuration so you can fine-tune various
  # backing providers for Vagrant. These expose provider-specific options.
  # Example for VirtualBox:
  #
   config.vm.provider "virtualbox" do |vb|
	 vb.name = "dev"	
   
     # Customize the amount of memory on the VM:
     vb.memory = "4096"

     vb.cpus = 2
   end
  #
  # View the documentation for the provider you are using for more
  # information on available options.

  # Define a Vagrant Push strategy for pushing to Atlas. Other push strategies
  # such as FTP and Heroku are also available. See the documentation at
  # https://docs.vagrantup.com/v2/push/atlas.html for more information.
  # config.push.define "atlas" do |push|
  #   push.app = "YOUR_ATLAS_USERNAME/YOUR_APPLICATION_NAME"
  # end

  # Enable provisioning with a shell script. Additional provisioners such as
  # Puppet, Chef, Ansible, Salt, and Docker are also available. Please see the
  # documentation for more information about their specific syntax and use.
   config.vm.provision "shell", inline: <<-SHELL
	
	#--------------ENVIRONMENT-------------------------
	#enable multiple workspaces
   	su -c "dbus-launch gsettings set org.compiz.core:/org/compiz/profiles/unity/plugins/core/ hsize 2" vagrant
	su -c "dbus-launch gsettings set org.compiz.core:/org/compiz/profiles/unity/plugins/core/ vsize 2" vagrant
	
	#install a source controlled bashrc 
	cp /vagrant/.bashrc /home/vagrant/.bashrc
	#--------------ENVIRONMENT-------------------------
	
	#--------------CHROME-------------------------
	#add chrome repo
	wget -q -O - https://dl-ssl.google.com/linux/linux_signing_key.pub | sudo apt-key add - 
	sh -c 'echo "deb [arch=amd64] http://dl.google.com/linux/chrome/deb/ stable main" >> /etc/apt/sources.list.d/google-chrome.list'
	
    apt-get update
    apt-get dist-upgrade

	#install chrome 
	apt-get install -y google-chrome-stable
	#--------------CHROME-------------------------
	
	#--------------GIT-------------------------
	#install git
    apt-get install -y git
	#set global username and email for git
	echo -e "[user]\n\tname = yishnish\n\temail = yishnish@gmail.com" > /home/vagrant/.gitconfig
	#set global config to default pushing to the current checked out branch
	git config --global push.default simple 
	#move rsa keys for github to the vagrant user's .ssh directory
	mkdir ~/.ssh
	cp /vagrant/id_rsa /vagrant/id_rsa.pub ~/.ssh
	 
	#change permissions on keys so git doesn't complain about them being lax
	chmod 600 ~/.ssh/id_rsa
	chmod 600 ~/.ssh/id_rsa.pub
	 
	#add github to known hosts
	ssh-keyscan -t rsa github.com >> ~/.ssh/known_hosts
	cp /vagrant/id_rsa /vagrant/id_rsa.pub /home/vagrant/.ssh
	#--------------GIT------------------------- 
	 
	#--------------NODE-------------------------
	#install node
	curl -sL https://deb.nodesource.com/setup_4.x | sh
	apt-get install -y nodejs
	#--------------NODE-------------------------	  
	 
	#------------------------RVM, RUBY AND RAILS-RELATED-GEMS------------------------------
	#add mpapis public key
	gpg --keyserver hkp://keys.gnupg.net --recv-keys 409B6B1796C275462A1703113804BB82D39DC0E3
	
	#install rvm and current stable ruby 
	curl -sSL https://get.rvm.io | bash -s stable --ruby
		
	#change owner of /usr/local/rvm to vagrant user
	sudo chown -R vagrant /usr/local/rvm/

	#load the rvm script when you launch a bash shell
	source /usr/local/rvm/scripts/rvm

	#update out-of-date global gems
	rvm gemset use global
	gem update
		
	#install bundler globally
	gem install bundler
	#install nokogiri globally
	gem install nokogiri
		
	echo "source /usr/local/rvm/scripts/rvm" >> /home/vagrant/.bashrc
	#------------------------RVM, RUBY AND RAILS-RELATED-GEMS------------------------------
	
	#------------------------JAVA AND MAVEN------------------------------------
	#install open-jdk
	apt-get install -y openjdk-8-jdk
	 
	#install maven
	apt-get install -y maven
	#------------------------JAVA AND MAVEN------------------------------------		


	#----------------IDE------------------------ 
	#download and install intellij
	curl --connect-timeout 60 --max-time 600 http://download-cf.jetbrains.com/idea/ideaIU-14.1.7.tar.gz > intellij.tar.gz
	mkdir /home/vagrant/intellij && tar -xzf intellij.tar.gz -C /home/vagrant/intellij --strip-components 1
	rm intellij.tar.gz
	cp /vagrant/.run-intellij /home/vagrant/.run-intellij
	#----------------IDE------------------------
	
	
	#----------------DOCKER------------------------
	#install docker
	apt-get -y install apt-transport-https ca-certificates
	apt-key adv --keyserver hkp://p80.pool.sks-keyservers.net:80 --recv-keys 58118E89F3A912897C070ADBF76221572C52609D
	echo "deb https://apt.dockerproject.org/repo ubuntu-xenial main" > /etc/apt/sources.list.d/docker.list
	apt-get update
	apt-get purge lxc-docker
	apt-get -y install linux-image-extra-$(uname -r)
	apt-get -y install docker-engine
	
	#groupadd docker
	usermod -aG docker vagrant
	
	#restart docker to pick up the group change
	service docker restart
	#----------------DOCKER------------------------
	 			
	#clone git repos
	mkdir -p /home/vagrant/computer_programs/
	chown -hR vagrant /home/vagrant
	git clone git@github.com:yishnish/sketch.git /home/vagrant/computer_programs/sketch
	git clone git@github.com:yishnish/lyfe.git /home/vagrant/computer_programs/lyfe
	git clone git@github.com:yishnish/sample-mvc.git /home/vagrant/computer_programs/sample-mvc
	git clone git@github.com:yishnish/mytelnet.git /home/vagrant/computer_programs/mytelnet
	 
	#kill all the vagrant user processes to force a logout 
	pkill -KILL -u vagrant
	 
	#set local because it seems necessary in order to be able to start a gnome terminal
	localectl set-locale LANG=en_US.utf8
	
   SHELL
end



