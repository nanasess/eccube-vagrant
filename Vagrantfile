# coding: utf-8
# -*- mode: ruby -*-
# vi: set ft=ruby :

VAGRANTFILE_API_VERSION = "2"

$shell = <<SCRIPT
## Timezone の設定
sudo cp -p  /usr/share/zoneinfo/Japan /etc/localtime
## Database の作成
createuser -U postgres -d -S -R cube3_dev_user
createdb -U cube3_dev_user -E utf-8 cube3_dev


echo 'Congratulations!!! Install Success.'
SCRIPT

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
  config.vm.box = "centos-6.7-i386"
  config.vm.box_url = "http://downloads.sourceforge.net/project/nrel-vagrant-boxes/CentOS-6.7-i386-v20151108.box?r=&ts=1447201364&use_mirror=jaist"

  config.vm.network "private_network", ip: "192.168.33.10"

  if Vagrant.has_plugin?("vagrant-cachier")
    config.cache.scope = :box
  end

  config.vm.synced_folder "../", "/ec-cube",
    :create => true,
    :owner=> 'root', :group=>'root',
    :mount_options => ["dmode=777,fmode=777"]

  config.vm.provision :chef_solo do |chef|
    #chef.log_level = :debug
    chef.cookbooks_path = ["./chef/cookbooks", "./chef/site-cookbooks"]
    chef.roles_path = "./chef/roles"
    chef.data_bags_path = "./chef/data_bags"

    chef.add_recipe     "iptables::disabled"
    chef.add_recipe     "apache2"
    chef.add_recipe     "apache2::mod_php5"
    chef.add_recipe     "apache2::mod_ssl"
    chef.add_recipe     "apache2::mod_rewrite"
    chef.add_recipe     "git"
    chef.add_recipe     "yum-ius"

    chef.add_recipe     "php56u-ius"

    chef.add_recipe     "postgresql::client"
    chef.add_recipe     "postgresql::server"
    chef.add_recipe     "mysql::client"
    chef.add_recipe     "mysql::server"

    chef.add_recipe     "ec-cube3"

    chef.json = {
      :yum => {
        :epel => {
          :key_url => "wget http://ftp.riken.jp/Linux/fedora/epel/RPM-GPG-KEY-EPEL-6"
        }
      },
      :apache => {
        :version => "2.2",
        :default_site_enabled => true,
        :docroot_dir => "/ec-cube/html",
        :listen_ports => [80, 443]
      },
      :php => {
        :directives => {
          :display_errors => 'On',
          "date.timezone" => "Asia/Tokyo",
        },
        :packages => ["php56u", "php56u-mbstring", "php56u-pdo", "php56u-pgsql", "php56u-mysql", "php56u-pear", "php56u-xml", "php56u-gd", "php56u-soap", "php56u-devel", "php56u-pecl-xdebug", "php56u-opcache", "php56u-pecl-apcu"]
      },
      :postgresql => {
        :password => {
          postgres: 'password'
        },
        :pg_hba => [
          {:type => 'local', :db => 'all', :user => 'postgres', :addr => nil, :method => 'trust'},
          {:type => 'local', :db => 'all', :user => 'all', :addr => nil, :method => 'trust'},
          {:type => 'host', :db => 'all', :user => 'all', :addr => '127.0.0.1/32', :method => 'trust'},
          {:type => 'host', :db => 'all', :user => 'all', :addr => '::1/128', :method => 'trust'}
        ]
      },
      :mysql => {
        :server_root_password => "password",
        :allow_remote_root => true,
        :bind_address           => "0.0.0.0",
      }
    }
  end

  config.omnibus.chef_version = '12.5.1'

  config.vm.provision "shell", inline: $shell
end
