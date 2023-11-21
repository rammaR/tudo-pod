Vagrant.configure("2") do |config|
    config.vm.box = "ubuntu/bionic64"
    config.vm.network "forwarded_port", guest: 80, host: 8080
    config.vm.network "forwarded_port", guest: 6443, host: 6443
    config.vm.network "forwarded_port", guest: 443, host: 8443
  
    config.vm.provider "virtualbox" do |vb|
      vb.memory = "4048"
      vb.cpus = 4
    end
  
    config.vm.provision "shell", inline: <<-SHELL
      # Atualiza a lista de pacotes e instala as dependências
      sudo apt-get update
      sudo apt-get upgrade -y
      sudo apt-get install -y apt-transport-https openssh-server ca-certificates curl software-properties-common

      # Install Docker
      sudo apt-get install -y docker.io
      sudo systemctl start docker
      sudo systemctl enable docker

      #Instalação do Kubernetes
      curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"

      curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl.sha256"

      echo "$(cat kubectl.sha256)  kubectl" | sha256sum --check

      sudo install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl

      # Pull the official GitLab image from Docker Hub
      sudo docker pull gitlab/gitlab-ce:latest

      # Run GitLab container
      sudo docker run -d \
        --hostname localhost \
        --publish 8443:443 --publish 8080:80 --publish 2222:22 \
        --name gitlab \
        --restart always \
        --volume /srv/gitlab/config:/etc/gitlab \
        --volume /srv/gitlab/logs:/var/log/gitlab \
        --volume /srv/gitlab/data:/var/opt/gitlab \
        gitlab/gitlab-ce:latest

    SHELL
  end
