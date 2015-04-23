# -*- mode: ruby -*-
# vi: set ft=ruby :

VAGRANTFILE_API_VERSION = "2"

$shell = <<SCRIPT
## Timezone の設定
sudo cp -p  /usr/share/zoneinfo/Japan /etc/localtime
## Database の作成
sudo -u postgres createuser -d -S -R eccube_db_user
sudo -u postgres createdb -U eccube_db_user -E utf-8 eccube_db


echo 'Congratulations!!! Install Success.'
SCRIPT

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
  config.vm.box = "puppetlabs/centos-6.6-32-nocm"

  config.vm.network "private_network", ip: "192.168.33.10"

  if Vagrant.has_plugin?("vagrant-cachier")
    config.cache.scope = :box
  end

  config.vm.synced_folder "../", "/ec-cube",
    :create => true,
    :owner=> 'vagrant', :group=>'vagrant',
    :mount_options => ["dmode=777,fmode=777"]

  config.vm.provision :chef_solo do |chef|
    chef.log_level = :debug
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

    # PHPはバージョンを変更できるようにしておく。ただし択一
    # ▼PHP5.3を利用する場合にコメントアウト
    #chef.add_recipe     "php"
    # ▼PHP5.4を利用する場合にコメントアウト
    chef.add_recipe     "php54-ius"
    # PHPの選択ここまで

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
        # PHP5.3用
        #:packages => ["php-mbstring", "php-pdo", "php-pgsql", "php-mysql", "php-pear", "php-xml", "php-gd", "php-soap", "php-devel"]
        # PHP5.4用
        :packages => ["php54", "php54-mbstring", "php54-pdo", "php54-pgsql", "php54-mysql", "php54-pear", "php54-xml", "php54-gd", "php54-soap", "php54-devel"]
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

  config.omnibus.chef_version = '12.2.1'

  config.vm.provision "shell", inline: $shell
end
