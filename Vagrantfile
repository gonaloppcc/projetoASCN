Number_Workers = 0 #number Workers to launch
IP_RANGE= "192.168.56" #Change me if needed!
PUBLIC_KEY_PATH = "~/.ssh/id_rsa.pub" #Change me if needed!
READ_PUBLIC_KEY = File.read(File.expand_path(PUBLIC_KEY_PATH)).strip

Vagrant.configure("2") do |config|
  config.vm.box = "bento/ubuntu-20.04"
  config.vm.synced_folder ".", "/vagrant"
  config.vm.provision :shell, privileged: true, inline: $provision_all

  config.vm.define :master do |master|
    master.vm.hostname = "master"
    master.vm.provider "virtualbox" do |v|
      v.memory = 2048
      v.cpus = 2
    end
    master.vm.network :private_network, ip: "#{IP_RANGE}.100"
  end

end

$provision_all = <<-SHELL
  echo "[ALL|Task 1] Configure SSH Public Key authentication"
  echo "#{READ_PUBLIC_KEY}" >> /home/vagrant/.ssh/authorized_keys
  sed -i 's/#PubkeyAuthentication yes/PubkeyAuthentication yes/g' /etc/ssh/sshd_config
  sed -i 's/#PasswordAuthentication yes/PasswordAuthentication no/g' /etc/ssh/sshd_config
  systemctl restart sshd

  echo "[ALL|Task 2] Update packages and install vim"
  apt update
  apt-get install -y vim

  echo "[ALL|Task 3] Installing Docker"
  apt-get install  -y
  echo '{"exec-opts": ["native.cgroupdriver=systemd"]}' >> /etc/docker/daemon.json

  echo "[ALL|Task 4] Installing ansible"
  sudo apt install -y ansible

  echo "[ALL|Task 5] Installing Needed Google Cloud Packages"
  sudo apt-get install apt-transport-https ca-certificates gnupg

  echo "[ALL|Task 6] Adding Google Cloud Repository"
  echo "deb [signed-by=/usr/share/keyrings/cloud.google.gpg] https://packages.cloud.google.com/apt cloud-sdk main" | sudo tee -a /etc/apt/sources.list.d/google-cloud-sdk.list

  echo "[ALL|Task 7] Importing Google Cloud Repository Key"
  curl https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key --keyring /usr/share/keyrings/cloud.google.gpg add -

  echo "[ALL|Task 8] Updating and installing gcloud CLI"
  sudo apt-get update && sudo apt-get install google-cloud-cli

  echo "[ALL|Task 9] Install Kubernetes"
  apt-get install -y kubeadm kubelet kubectl
  apt-mark hold kubeadm kubelet kubectl

SHELL
