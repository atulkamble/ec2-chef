
Reference github repo: https://github.com/atulkamble/chef-project

// ec2 - t3.medium >> ssh 

cd Downloads 
chmod chef.pem 
ssh -i "chef.pem" ec2-user@ec2-3-87-25-186.compute-1.amazonaws.com

clear
sudo yum update -y
sudo yum install git -y
git clone https://github.com/atulkamble/chef-project.git
ls
cd chef-project/
clear
wget https://packages.chef.io/files/stable/chef-workstation/24.4.1064/el/8/chef-workstation-24.4.1064-1.el8.x86_64.rpm
ls
sudo rpm -Uvh chef-workstation-24.4.1064-1.el8.x86_64.rpm
chef --version
sudo yum install libxcrypt-compat -y
chef generate repo chef-repo
cd chef-repo
ls
cd cookbooks/
ls
cd example/
ls
cd ..
ls
chef generate cookbook cookbooks/my_cookbook
cd cookbooks/my_cookbook
pwd
ls
cd recipes/
ls
cd ..
sudo nano recipes/default.rb
sudo chef-client --local-mode --runlist recipe[my_cookbook::default]
sudo cat /tmp/greeting.txt





