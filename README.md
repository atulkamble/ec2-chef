# ec2-chef

Home›Tools›Notepad
 Facebook Twitter WhatsApp Mail 

   
            
Enable auto save when page is closed
 
Chef Practice 

// update 
sudo yum update -y
// install ruby 
sudo yum install ruby -y
sudo yum install tree -y 
// install chef 
wget https://packages.chef.io/files/stable/chef-workstation/24.4.1064/el/8/chef-workstation-24.4.1064-1.el8.x86_64.rpm
sudo rpm -Uvh chef-workstation-24.4.1064-1.el8.x86_64.rpm
chef --version

// install dependency 
sudo yum install libxcrypt-compat -y

// create chef project 

chef generate repo project
cd project/

// create cookbook 
chef generate cookbook cookbooks/my_cookbook
cd cookbooks/
cd my_cookbook/
cd recipes/
cat default.rb 
sudo nano default.rb 

file '/tmp/greeting.txt' do
  content 'Hello, Chef!'
end`

or 

package 'docker' do
action :install
end
service 'docker' do
action [:enable, :start]
end

cd ..
cd ..
cd ..

sudo chef-client --local-mode --runlist recipe[my_cookbook::default]

cat /tmp/greering.txt
docker --version
Line 1, Column 1    Chars 877, Words 78
