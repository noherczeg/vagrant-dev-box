ENV['VAGRANT_DEFAULT_PROVIDER'] = 'virtualbox'

Vagrant.configure("2") do |config|
  config.vm.box = "hashicorp/bionic64"
  config.vm.hostname = "ubuntu-dev"

  config.vm.provider :virtualbox do |vb|
    vb.gui = true
    vb.customize ["modifyvm", :id, "--memory", 4096]
    vb.customize ["modifyvm", :id, "--vram", 64]
    vb.customize ["modifyvm", :id, "--accelerate3d", "on"]
  end

  config.ssh.forward_agent = true

  config.vm.network "forwarded_port", guest: 8080, host: 8080
  config.vm.network "forwarded_port", guest: 8000, host: 8000
  config.vm.network "forwarded_port", guest: 3000, host: 3000

  config.vm.provision "file", source: "provision/.ssh", destination: ".ssh"

  $rootScript = <<-SCRIPT
    sudo chown -R vagrant /home/vagrant/.ssh
        
    sudo chmod 700 /home/vagrant/.ssh
    sudo chmod 600 /home/vagrant/.ssh/id_rsa
    sudo chmod 644 /home/vagrant/.ssh/id_rsa.pub

    sudo apt-get update

    # Intall package support
    sudo apt install software-properties-common apt-transport-https wget apt-transport-https ca-certificates curl gnupg-agent unzip

    # Install GUI
    sudo apt-get install -y ubuntu-desktop virtualbox-guest-dkms virtualbox-guest-utils virtualbox-guest-x11

    # Permit anyone to start the GUI
    sudo sed -i 's/allowed_users=.*$/allowed_users=anybody/' /etc/X11/Xwrapper.config

    # Add Microsoft GPG
    wget -q https://packages.microsoft.com/keys/microsoft.asc -O- | sudo apt-key add -
    sudo add-apt-repository "deb [arch=amd64] https://packages.microsoft.com/repos/vscode stable main"

    sudo apt-get update

    # VSCode
    sudo apt-get install -y code

    # Docker
    sudo apt-get remove -y docker docker-engine docker.io containerd runc
    curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
    sudo apt-key fingerprint 0EBFCD88
    sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"

    sudo apt-get update

    sudo apt-get install -y docker-ce docker-ce-cli containerd.io

    sudo usermod -aG docker vagrant

    # Terraform
    sudo wget https://releases.hashicorp.com/terraform/0.14.5/terraform_0.14.5_linux_amd64.zip
    sudo unzip terraform_0.14.5_linux_amd64.zip
    sudo rm -rf terraform_0.14.5_linux_amd64.zip
    sudo mv terraform /usr/local/bin/

    # Ansible
    sudo apt-add-repository --yes --update ppa:ansible/ansible
    sudo apt install -y ansible
  SCRIPT

  $userScript = <<-SCRIPT
    cd /home/vagrant

    # Installing nvm
    wget -qO- https://raw.githubusercontent.com/nvm-sh/nvm/v0.37.2/install.sh | bash

    # This enables NVM without a logout/login
    export NVM_DIR="/home/vagrant/.nvm"
    [ -s "$NVM_DIR/nvm.sh" ] && . "$NVM_DIR/nvm.sh"  # This loads nvm

    # Install Node and alias
    nvm install 14.15.4
    nvm alias default 14.15.4

    # create projects folder
    mkdir -p projects

    # IDEA Ultimate
    mkdir -p /home/vagrant/apps
    cd /home/vagrant/apps
    rm -rf idea-IU-203.6682.168
    wget https://download.jetbrains.com/idea/ideaIU-2020.3.1.tar.gz
    tar -xzvf ideaIU-2020.3.1.tar.gz
    rm ideaIU-2020.3.1.tar.gz

    # JDK
    mkdir -p /home/vagrant/tools
    cd /home/vagrant/tools
    rm -rf jdk-11.0.10+9
    wget https://github.com/AdoptOpenJDK/openjdk11-binaries/releases/download/jdk-11.0.10%2B9/OpenJDK11U-jdk_x64_linux_hotspot_11.0.10_9.tar.gz
    tar -xzvf OpenJDK11U-jdk_x64_linux_hotspot_11.0.10_9.tar.gz
    rm -rf OpenJDK11U-jdk_x64_linux_hotspot_11.0.10_9.tar.gz
    echo "export JAVA_HOME=/home/vagrant/tools/jdk-11.0.10+9" >> /home/vagrant/.profile
    echo "export PATH=$PATH:/home/vagrant/tools/jdk-11.0.10+9/bin" >> /home/vagrant/.profile

    # Maven
    cd /home/vagrant/tools
    rm -rf apache-maven-3.6.3
    wget https://downloads.apache.org/maven/maven-3/3.6.3/binaries/apache-maven-3.6.3-bin.tar.gz
    tar -xzvf apache-maven-3.6.3-bin.tar.gz
    rm apache-maven-3.6.3-bin.tar.gz
    echo "export PATH=$PATH:/home/vagrant/tools/apache-maven-3.6.3/bin" >> /home/vagrant/.profile
  SCRIPT

  config.vm.provision "shell", inline: $rootScript
  config.vm.provision "shell", inline: $userScript, privileged: false

  # Disable VSCode telemetry BS
  config.vm.provision "file", source: "provision/vscode/User/settings.json", destination: "/home/vagrant/.config/Code/User/settings.json"
end
