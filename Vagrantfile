Vagrant.configure("2") do |config|

  config.trigger.before :up do |trigger|
    trigger.name = "Welcome message"
    trigger.info = "This environment is for an automated deployment of Jenkins and Molecule, solely for the purposes of demonstration."
  end

# =========================
# |Create the Jenkins box.|
# =========================
  
  config.vm.define "jenkins" do |jenkins|
    jenkins.vm.box = "generic/rhel7"
    jenkins.vm.provider "virtualbox" do |prov|
      prov.memory = 2048
      prov.cpus = 2
      prov.name = "jenkins"
      prov.customize ["modifyvm", :id, "--natdnshostresolver1", "on"]
      prov.customize ["modifyvm", :id, "--ioapic", "on"]
    end
    jenkins.vm.hostname = "jenkins"
    jenkins.vm.network "forwarded_port", guest: 80, host: 9080
    jenkins.vm.network "forwarded_port", guest: 8080, host: 9081
    jenkins.vm.network "forwarded_port", guest: 8443, host: 9082
    jenkins.vm.network "forwarded_port", guest: 22, host: 9022
    jenkins.vm.network "private_network", ip: "192.168.33.100"
    jenkins.vm.provision "shell", inline: <<-SHELL
      subscription-manager register --org=13545105 --activationkey=infra-tooling-premium
      yum update -y
      yum-config-manager --add-repo ppa:ansible/ansible
      yum update
      yum install -y ansible
    SHELL
    jenkins.vm.provision "ansible" do |ansible|
      ansible.playbook = "provisioning/jenkins.yml"
      ansible.become = true
    end
    jenkins.trigger.before [:up, :provision] do |trigger|
      trigger.name = "Starting Message"
      trigger.info = "Initiating machine build for jenkins."
    end
    jenkins.trigger.after :destroy do |trigger|
        trigger.name = "Destruction Message."
        trigger.info = "Jenkins Destroyed."
    end
    
  end

# ========================
# |Create the Gitlab box.|
# ========================

  config.vm.define "gitlab" do |gitlab|
    gitlab.vm.box = "generic/rhel7"
    gitlab.vm.provider "virtualbox" do |prov|
      prov.memory = 4096
      prov.cpus = 4
      prov.name = "gitlab"
      prov.customize ["modifyvm", :id, "--natdnshostresolver1", "on"]
      prov.customize ["modifyvm", :id, "--ioapic", "on"]
    end
    gitlab.vm.hostname = "gitlab"
    gitlab.vm.network "forwarded_port", guest: 80, host: 9180
    gitlab.vm.network "forwarded_port", guest: 8080, host: 9181
    gitlab.vm.network "forwarded_port", guest: 8443, host: 9182
    gitlab.vm.network "forwarded_port", guest: 22, host: 9122
    gitlab.vm.network "private_network", ip: "192.168.33.101"
    gitlab.vm.provision "shell", inline: <<-SHELL
      subscription-manager register --org=13545105 --activationkey=infra-tooling-premium
      yum update -y
      yum-config-manager --add-repo ppa:ansible/ansible
      yum update
      yum install -y ansible
    SHELL
    gitlab.vm.provision "ansible" do |ansible|
      ansible.playbook = "provisioning/gitlab.yml"
      ansible.become = true
    end
    gitlab.trigger.before [:up, :provision] do |trigger|
      trigger.name = "Starting Message"
      trigger.info = "Initiating machine build for gitlab."
    end
    gitlab.trigger.after :destroy do |trigger|
      trigger.name = "Destruction Message."
      trigger.info = "Gitlab Destroyed."
    end
  end

# =======================
# |Create the Agent box.|
# =======================

  config.vm.define "agent" do |agent|
    agent.vm.hostname = "agent.example.com"
    agent.vm.box = "generic/rhel7"
    agent.vm.provider "virtualbox" do |prov|
      prov.memory = 2048
      prov.cpus = 2
      prov.name = "agent"
      prov.customize ["modifyvm", :id, "--natdnshostresolver1", "on"]
      prov.customize ["modifyvm", :id, "--ioapic", "on"]
    end
    agent.vm.network "forwarded_port", guest: 80, host: 9280
    agent.vm.network "forwarded_port", guest: 8080, host: 9281
    agent.vm.network "forwarded_port", guest: 8443, host: 9282
    agent.vm.network "forwarded_port", guest: 22, host: 9222
    agent.vm.network "private_network", ip: "192.168.33.102"
    agent.vm.provision "shell", inline: <<-SHELL
      subscription-manager register --org=13545105 --activationkey=infra-tooling-premium
      yum update -y
      yum-config-manager --add-repo ppa:ansible/ansible
      yum update
      yum install -y ansible
    SHELL
    agent.vm.provision "ansible" do |ansible|
      ansible.playbook = "provisioning/agent.yml"
      ansible.galaxy_role_file = "requirements.yml"
    end
    agent.trigger.before :up do |trigger|
      trigger.name = "Starting"
      trigger.info = "Initiating machine build for Agent."
    end
    agent.trigger.after :destroy do |trigger|
      trigger.name = "Destruction Message."
      trigger.info = "Agent Destroyed."
    end

  end
  
#  config.vm.provision "ansible" do |ansible|
#    ansible.playbook = "/ansible/provision.yml"
#    ansible.groups = {
#      "jenkins" => ["jenkins_master"],
#      "gitlab" => ["source_control"],
#      "agent" => ["jenkins_agent"]
#    }
#  end
  
  config.trigger.after :up do |trigger|
      trigger.name = "Finished Message"
      trigger.info = "Machines created."
  end
  #  config.trigger.before :destroy do |trigger|
  #    trigger.info = "RUNNING DESTRUCTION PROCESS"
  #    trigger.run_remote = {inline: "subscription-manager unregister"}
  #  end
end
