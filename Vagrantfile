authorized_keys = ["ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDHfJER+eGjDa0PCjN9V+VSYjMO9CiCkuTEi7w0Fio85PL/qBI67L3HmUkIFsIcA5fLiheClfeBvQPhojPpPrJSUD/3/iDgqLW/dWTQ64bpInuOCZJtZbSkVnbeVVyAxO7CF/0yh7W0NOg+IallnCimOEsDGhma686FYcvvOweCJ+w3bYA9fPwNGTBxsc++hTs/mR/WYehc1gECVZ1zE2EBNF+N9Uov+p+SuFkccpCVFM5tVzduNuQsWy6TRjoUK/q/P4BxhV8M/E2lbWK6SZ+ieOgQsC7UxBel5a6B7G//MqO4ZfHJEtpy5LuiuZMxjl/pdbfWlIwshjtnplH4BN35 jenkins@main",
                   "ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQC+eE2iEP0XcVfCRlTPm/xrb6oTgv5iI8LQCz2JGwthLMW2sVxQyzaKAbqmdZJRsNqiuMerZ06xSurVLRPLEjHJmChT5wBX14sBRESwhCQ+SIByce05BZ8iKVphZdtmthAXBpQHS+JAIAyO3nk9dcbeZEy0vfCGLvVOtQ6/1MSxtMJ23ECvMYqBjRnPuvWlC70kmKhOe/bJgSgp+7JW/YXoX62BzQ8HO4e5e8QQy9QDY3uuQbKTCnoPqkat14ARRr/Q63JMGhe6jEQamNCSgYH/b13uc/4xDSMeTfUJes3kWO8J7REWmAoausZ+LPT9hUJF+5pzuh2V2VOY/0Ks40PX jenkins@admin1"]

Vagrant.configure(2) do |config|

  [31819, 31820].each do |port|
    config.vm.define "ubuntu-16.04-32bit-#{port}" do |node|
      node.vm.box = "ubuntu/xenial32"
      node.vm.network "forwarded_port", guest: 22, host: port

      node.vm.provider "virtualbox" do |vb|
        vb.name = "ubuntu-16.04-32bit-#{port}"
        vb.memory = "2500"
        vb.use_vdi = true
        vb.customize 'pre-boot', ['modifyhd', :disk0, '--resize', '40000']
      end

      node.vm.provision "shell", privileged: true, inline: <<EOF
echo "127.0.0.1 ubuntu-xenial" >> /etc/hosts
apt-get purge -y --auto-remove cloud-init
apt-get update
apt-get install -y build-essential openjdk-9-jre-headless

useradd -d /home/jenkins -m -s /bin/bash jenkins
echo "jenkins ALL=(ALL) NOPASSWD:ALL" > /etc/sudoers.d/90-jenkins
chmod 440 /etc/sudoers.d/90-jenkins

mkdir /home/jenkins/.ssh
touch /home/jenkins/.ssh/authorized_keys
chown -R jenkins:jenkins /home/jenkins/.ssh
chmod 600 /home/jenkins/.ssh/authorized_keys
chmod 700 /home/jenkins/.ssh
EOF

      authorized_keys.each do |key|
        node.vm.provision "shell", inline: "echo \"#{key}\" >> /home/jenkins/.ssh/authorized_keys"
      end
    end
  end

  [10001, 10002].each do |port|
    config.vm.define "osx-11-#{port}" do |node|
      node.vm.box = "ndn-jenkins/osx-10.11"
      node.vm.network "forwarded_port", guest: 22, host: port

      node.vm.provider "virtualbox" do |vb|
        vb.name = "osx-11-#{port}"
        vb.memory = "4000"
        vb.linked_clone = true
      end

      node.vm.provision "shell", privileged: false, inline: <<EOF
/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
brew update
EOF

      authorized_keys.each do |key|
        node.vm.provision "shell", inline: "echo \"#{key}\" >> /Users/jenkins/.ssh/authorized_keys"
      end
    end
  end

end