# EC2 Chef Configuration Management

A comprehensive guide and practice repository for setting up and using Chef on Amazon EC2 instances for infrastructure automation and configuration management.

## Table of Contents

- [Overview](#overview)
- [Prerequisites](#prerequisites)
- [Installation](#installation)
- [Getting Started](#getting-started)
- [Creating Cookbooks](#creating-cookbooks)
- [Recipe Examples](#recipe-examples)
- [Running Chef](#running-chef)
- [Troubleshooting](#troubleshooting)
- [Best Practices](#best-practices)
- [Contributing](#contributing)

## Overview

This repository contains instructions and examples for:
- Installing Chef Workstation on EC2 instances
- Creating and managing Chef cookbooks
- Automating server configuration and package management
- Best practices for infrastructure as code with Chef

## Prerequisites

- Amazon EC2 instance running Amazon Linux 2 or CentOS/RHEL 8
- Root or sudo access on the instance
- Basic knowledge of Linux command line
- Understanding of configuration management concepts

## Installation

### 1. Update System Packages

```bash
sudo yum update -y
```

### 2. Install Required Dependencies

```bash
# Install Ruby (required for Chef)
sudo yum install ruby -y

# Install tree utility for directory visualization
sudo yum install tree -y

# Install compatibility library
sudo yum install libxcrypt-compat -y
```

### 3. Install Chef Workstation

```bash
# Download Chef Workstation package
wget https://packages.chef.io/files/stable/chef-workstation/24.4.1064/el/8/chef-workstation-24.4.1064-1.el8.x86_64.rpm

# Install the package
sudo rpm -Uvh chef-workstation-24.4.1064-1.el8.x86_64.rpm

# Verify installation
chef --version
```

## Getting Started

### 1. Create a Chef Project

```bash
# Generate a new Chef repository
chef generate repo project
cd project/
```

### 2. Explore Project Structure

```bash
tree
```

The generated structure includes:
- `cookbooks/` - Directory for your cookbooks
- `policyfiles/` - Policy files for cookbook versioning
- `README.md` - Project documentation

## Creating Cookbooks

### 1. Generate a New Cookbook

```bash
# Create a new cookbook
chef generate cookbook cookbooks/my_cookbook

# Navigate to the cookbook
cd cookbooks/my_cookbook/
```

### 2. Cookbook Structure

```
my_cookbook/
├── README.md
├── metadata.rb
├── recipes/
│   └── default.rb
├── templates/
├── files/
├── attributes/
└── spec/
```

## Recipe Examples

### 1. Simple File Creation Recipe

Edit the default recipe:

```bash
cd recipes/
sudo nano default.rb
```

Add the following content:

```ruby
# Create a greeting file
file '/tmp/greeting.txt' do
  content 'Hello, Chef!'
  mode '0644'
  owner 'root'
  group 'root'
end
```

### 2. Package Installation and Service Management

Alternative recipe for Docker installation:

```ruby
# Install Docker package
package 'docker' do
  action :install
end

# Enable and start Docker service
service 'docker' do
  action [:enable, :start]
end
```

### 3. Multi-Resource Recipe

```ruby
# Update system packages
execute 'update_packages' do
  command 'yum update -y'
  action :run
end

# Install essential packages
%w[git curl wget vim].each do |pkg|
  package pkg do
    action :install
  end
end

# Create application directory
directory '/opt/myapp' do
  owner 'root'
  group 'root'
  mode '0755'
  recursive true
end
```

## Running Chef

### 1. Run Chef in Local Mode

Navigate back to the project root:

```bash
cd ../../../
```

Execute the cookbook:

```bash
sudo chef-client --local-mode --runlist recipe[my_cookbook::default]
```

### 2. Verify Results

```bash
# Check if greeting file was created
cat /tmp/greeting.txt

# If Docker recipe was used, verify Docker installation
docker --version
systemctl status docker
```

## Troubleshooting

### Common Issues

1. **Chef command not found**
   - Ensure Chef Workstation is properly installed
   - Check PATH environment variable
   ```bash
   echo $PATH
   which chef
   ```

2. **Permission denied errors**
   - Run chef-client with sudo privileges
   - Check file/directory permissions

3. **Package installation failures**
   - Ensure system is updated: `sudo yum update -y`
   - Check internet connectivity
   - Verify package names for your distribution

4. **Ruby version conflicts**
   - Chef Workstation includes its own Ruby installation
   - Use `chef exec ruby --version` to check Chef's Ruby version

## Best Practices

### 1. Recipe Organization

- Keep recipes focused and single-purpose
- Use descriptive names for resources
- Group related resources logically

### 2. Attribute Management

```ruby
# Use attributes for configurable values
default['myapp']['version'] = '1.0.0'
default['myapp']['port'] = 8080
```

### 3. Testing

```bash
# Use cookstyle for linting
cookstyle cookbooks/my_cookbook/

# Run unit tests
chef exec rspec cookbooks/my_cookbook/
```

### 4. Security

- Never hardcode sensitive data in recipes
- Use Chef Vault or encrypted data bags for secrets
- Follow principle of least privilege

### 5. Version Control

- Keep your Chef repository under version control
- Use meaningful commit messages
- Tag releases appropriately

## Advanced Usage

### 1. Using Policyfiles

```bash
# Generate a policyfile
chef generate policyfile my_policy

# Install cookbook dependencies
chef install my_policy.rb

# Apply policy
chef push production my_policy.lock.json
```

### 2. Custom Resources

```ruby
# Define custom resource
provides :my_custom_app

property :version, String, required: true
property :port, Integer, default: 8080

action :install do
  # Implementation here
end
```

## Monitoring and Logging

### Chef Client Logs

```bash
# View Chef client logs
sudo tail -f /var/log/chef/client.log

# Check Chef run status
chef-client --why-run
```

### System Integration

- Integrate with AWS CloudWatch for monitoring
- Use Chef Automate for centralized management
- Set up log aggregation for troubleshooting

## Common Use Cases

### 1. Web Server Setup

```ruby
# Install and configure Apache
package 'httpd' do
  action :install
end

service 'httpd' do
  action [:enable, :start]
end

# Create index page
file '/var/www/html/index.html' do
  content '<h1>Hello from Chef!</h1>'
  mode '0644'
end
```

### 2. Database Installation

```ruby
# Install MySQL
package 'mysql-server' do
  action :install
end

service 'mysqld' do
  action [:enable, :start]
end
```

### 3. User Management

```ruby
# Create application user
user 'appuser' do
  comment 'Application User'
  home '/home/appuser'
  shell '/bin/bash'
  manage_home true
end
```

## Environment-Specific Configurations

### Development Environment

```ruby
# Install development tools
%w[gcc gcc-c++ make].each do |pkg|
  package pkg do
    action :install
  end
end
```

### Production Environment

```ruby
# Set up log rotation
template '/etc/logrotate.d/myapp' do
  source 'logrotate.erb'
  mode '0644'
end
```

## Integration with AWS Services

### 1. CloudWatch Agent Installation

```ruby
# Download and install CloudWatch agent
remote_file '/tmp/amazon-cloudwatch-agent.rpm' do
  source 'https://s3.amazonaws.com/amazoncloudwatch-agent/amazon_linux/amd64/latest/amazon-cloudwatch-agent.rpm'
end

rpm_package 'amazon-cloudwatch-agent' do
  source '/tmp/amazon-cloudwatch-agent.rpm'
  action :install
end
```

### 2. S3 Integration

```ruby
# Install AWS CLI
package 'awscli' do
  action :install
end

# Configure S3 sync script
template '/usr/local/bin/s3-sync.sh' do
  source 's3-sync.sh.erb'
  mode '0755'
end
```

## Contributing

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/amazing-feature`)
3. Commit your changes (`git commit -m 'Add some amazing feature'`)
4. Push to the branch (`git push origin feature/amazing-feature`)
5. Open a Pull Request

## Resources

- [Chef Documentation](https://docs.chef.io/)
- [Chef Supermarket](https://supermarket.chef.io/)
- [Chef Community](https://community.chef.io/)
- [AWS EC2 Documentation](https://docs.aws.amazon.com/ec2/)
- [Chef Learning Resources](https://learn.chef.io/)

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## Support

For support and questions:
- Create an issue in this repository
- Join the [Chef Community Slack](https://chefcommunity.slack.com/)
- Check the [Chef Documentation](https://docs.chef.io/)

---

**Note**: Always test your Chef cookbooks in a development environment before applying them to production systems. This ensures your configurations work as expected and don't cause unintended changes to critical infrastructure.

**Security Reminder**: Never commit sensitive information like passwords, API keys, or certificates to version control. Use Chef Vault, encrypted data bags, or environment variables for sensitive data management.