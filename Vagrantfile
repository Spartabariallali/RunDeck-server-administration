$sdb1 = <<-SCRIPT
parted /dev/sdb mklabel msdos
parted /dev/sdb mkpart primary 0% 100%
mkfs.xfs /dev/sdb1
mkdir /mnt/data1
if grep -Fxq "sdb1" /etc/fstab
then
  echo 'sdb1 exist in fstab'
else
  echo `blkid /dev/sdb1 | awk '{print$2}' | sed -e 's/"//g'` /mnt/data1   xfs   noatime,nobarrier   0   0 >> /etc/fstab
fi
if mount | grep /mnt/data1 > /dev/null; then
  echo "/dev/sdb1 mounted /mnt/data1"
  umount /mnt/data1
  mount /mnt/data1
else
  mount /mnt/data1
fi
SCRIPT

$rundeck = <<-SCRIPT
yum update -y 
yum install vim -y
yum install git -y
rpm -Uvh http://repo.rundeck.org/latest.rpm
yum install rundeck java -y
yum update rundeck -y
cd /vagrant
mv ansible-plugin-3.1.1.jar /var/lib/rundeck/libext
service rundeckd start
sed -i 's/localhost:4440$/192.168.100.10:4440/g' /etc/rundeck/framework.properties
sed -i 's/localhost:4440$/192.168.100.10:4440/g' /etc/rundeck/rundeck-config.properties
systemctl restart rundeckd
SCRIPT

$ansible_config = <<-SCRIPT
yum install epel-release -y
yum install python devel ansible -y 
echo "192.168.100.10 node1" >> /etc/hosts
echo "192.168.100.11 node2" >> /etc/hosts 
echo "192.168.100.12 node3" >> /etc/hosts 
rm -rf /etc/ansible/hosts
echo "[linux]" >> /etc/ansible/hosts
echo "node1" >> /etc/ansible/hosts 
echo "node2" >> /etc/ansible/hosts 
echo "node3" >> /etc/ansible/hosts 
sed -i 's/#host_key_auto_add = True/host_key_auto_add = True/g' /etc/ansible/ansible.cfg
chown rundeck.rundeck -R /etc/ansible 
echo "password" | sudo passwd --stdin rundeck
SCRIPT


$pubkey_authentication = <<-SCRIPT
sed -i 's/#PubkeyAuthentication yes/PubkeyAuthentication yes/g' /etc/ssh/sshd_config
sed -i 's/PasswordAuthentication no/PasswordAuthentication yes/g' /etc/ssh/sshd_config
systemctl restart sshd
SCRIPT

$rundeck_user = <<-SCRIPT 
sudo useradd -p $(openssl passwd -1 password) rundeck && sudo usermod -a -G wheel rundeck 
SCRIPT


$rundeck_copy_ssh_id = <<-SCRIPT 
yum -y install sshpass
su rundeck 
sshpass -p password ssh-copy-id -o StrictHostKeyChecking=no -i /var/lib/rundeck/.ssh/id_rsa -f rundeck@node1
sshpass -p password ssh-copy-id -o StrictHostKeyChecking=no -i /var/lib/rundeck/.ssh/id_rsa -f rundeck@node2
sshpass -p password ssh-copy-id -o StrictHostKeyChecking=no -i /var/lib/rundeck/.ssh/id_rsa -f rundeck@node3
SCRIPT


node1disk1 = "./node1disk1.vdi";
node2disk1 = "./node2disk1.vdi";
node3disk1 = "./node3disk1.vdi";

ip_node1 = "192.168.100.10";
ip_node2 = "192.168.100.11";
ip_node3 = "192.168.100.12";


Vagrant.configure("2") do |config|

    config.vm.define "node3" do |node3|
      node3.vm.network "private_network", ip: ip_node3
      node3.vm.hostname = "node3"
      node3.vm.define "node3"
      node3.vm.box_download_insecure = true
      node3.vm.box = "centos/7"
      node3.vm.synced_folder ".", "/vagrant", disabled: false
      node3.vm.provider "virtualbox" do |vb|
        vb.memory = "2048"
        if not File.exists?(node1disk1)
          vb.customize ['createhd', '--filename', node3disk1, '--variant', 'Fixed', '--size', 1 * 1024]
          vb.customize ['storageattach', :id,  '--storagectl', 'IDE', '--port', 0, '--device', 1, '--type', 'hdd', '--medium', node3disk1]
        end
      end

      node3.vm.provision "shell", inline: $sdb1
      node3.vm.provision "shell", inline: $pubkey_authentication
      node3.vm.provision "shell", inline: $rundeck_user
 
    end
  
    config.vm.define "node2" do |node2|
      node2.vm.network "private_network", ip: ip_node2
      node2.vm.hostname = "node2"
      node2.vm.define "node2"
      node2.vm.box_download_insecure = true
      node2.vm.box = "centos/7"
      node2.vm.provider "virtualbox" do |vb|
        vb.memory = "2048"
        if not File.exists?(node2disk1)
          vb.customize ['createhd', '--filename', node2disk1, '--variant', 'Fixed', '--size', 1 * 1024]
          vb.customize ['storageattach', :id,  '--storagectl', 'IDE', '--port', 0, '--device', 1, '--type', 'hdd', '--medium', node2disk1]
        end
      end
      node2.vm.provision "shell", inline: $sdb1
      node2.vm.provision "shell", inline: $pubkey_authentication
      node2.vm.provision "shell", inline: $rundeck_user
  
    end
  
    config.vm.define "node1" do |node1|
      node1.vm.network "private_network", ip: ip_node1
      node1.vm.hostname = "node1"
      node1.vm.define "node1"
      node1.vm.box_download_insecure = true
      node1.vm.box = "centos/7"
      node1.vm.synced_folder "ansible-plugin", "/vagrant", disabled: false
      node1.vm.provider "virtualbox" do |vb|
        vb.memory = "2048"
        if not File.exists?(node1disk1)
          vb.customize ['createhd', '--filename', node1disk1, '--variant', 'Fixed', '--size', 1 * 1024]
          vb.customize ['storageattach', :id,  '--storagectl', 'IDE', '--port', 0, '--device', 1, '--type', 'hdd', '--medium', node1disk1]
        end
      end
      node1.vm.provision "shell", inline: $sdb1
      node1.vm.provision "shell", inline: $rundeck
      node1.vm.provision "shell", inline: $ansible_config
      node1.vm.provision "shell", inline: $pubkey_authentication
      node1.vm.provision "shell", inline: $rundeck_copy_ssh_id
   
    end
  
  end










#   $docker = <<-SCRIPT
# yum update -y 

# echo "Beginning Docker install"

# # Install Docker-ce
# yum install docker -y

# # Start Docker

# systemctl enable docker

# systemctl start docker

# echo "Docker successfully installed"
# echo "Beginning post installation steps"

# # Post Installation Steps

# # Create Docker group
# groupadd docker

# # Add user to the docker group
# usermod -aG docker $USER

# echo "Creating RunDeck service"

# # create runddeck, jenkins and prometheus containers
# # docker run -d --name some-rundeck -p 4440:4440 -v data:/home/rundeck/server/data rundeck/rundeck:3.3.7

# echo "Creating Jenkins service"

# docker run -d -p 8080:8080 -p 50000:50000 jenkins/jenkins:lts

# echo "Creating Prometheus service"

# docker run -d -p 9090:9090 prom/prometheus
# SCRIPT