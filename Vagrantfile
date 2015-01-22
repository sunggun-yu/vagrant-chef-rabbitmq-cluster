# -*- mode: ruby -*-
# vi: set ft=ruby :

# All Vagrant configuration is done below. The "2" in Vagrant.configure
# configures the configuration version (we support older styles for
# backwards compatibility). Please don't change it unless you know what
# you're doing.
Vagrant.configure('2') do |config|

  # Define base image
  config.vm.box = 'ubuntu/trusty64'

  # Manage /etc/hosts on host and VMs
  config.hostmanager.enabled = false
  config.hostmanager.manage_host = true
  config.hostmanager.include_offline = true
  config.hostmanager.ignore_private_ip = false

  if Vagrant.has_plugin? ('omnibus')
    config.omnibus.chef_version = 'latest'
  end

  config.berkshelf.enabled = true

  config.vm.provision 'chef_solo' do |chef|
    chef.json = {
        'rabbitmq' => {
            'erlang_cookie' => 'EKTHGEVXEMYWBKFAKSKJ',
            'cluster' => true,
            'cluster_disk_nodes' => [
                'rabbit@rabbit1',
                'rabbit@rabbit2',
                'rabbit@rabbit3'],
            'policies' => {
                'ha-all' => {
                    'pattern' => '^(?!amq\\.).*',
                    'params' => {
                        'ha-mode' => 'all',
                        'ha-sync-mode' => 'automatic'
                    },
                    'priority' => 0
                },
                'ha-two' => nil
            },
            'enabled_plugins' => [
                'rabbitmq_management',
                'rabbitmq_management_visualiser'
            ],
            'enabled_users' => [
                {
                    :name => 'admin',
                    :password => 'admin',
                    :tag => 'administrator',
                    :rights =>[
                        {
                            :vhost => '/',
                            :conf => '.*',
                            :write => '.*',
                            :read => '.*'
                        }
                    ]
                }
            ]
        }
    }
    chef.run_list = [
        'recipe[rabbitmq::default]',
        'recipe[rabbitmq::user_management]',
        'recipe[rabbitmq::policy_management]',
        'recipe[rabbitmq::plugin_management]',
        'recipe[rabbitmq::mgmt_console]'
    ]
  end

  config.vm.define :rabbit1 do |rabbit1|
    rabbit1.vm.provider :virtualbox do |v|
      v.name = 'rabbit1'
      v.customize ['modifyvm', :id, '--memory', '1024']
    end
    rabbit1.vm.network :private_network, ip: '10.211.11.100'
    rabbit1.vm.hostname = 'rabbit1'
    rabbit1.vm.provision :hostmanager
  end

  config.vm.define :rabbit2 do |rabbit2|
    rabbit2.vm.box = 'ubuntu/trusty64'
    rabbit2.vm.provider :virtualbox do |v|
      v.name = 'rabbit2'
      v.customize ['modifyvm', :id, '--memory', '1024']
    end
    rabbit2.vm.network :private_network, ip: '10.211.11.101'
    rabbit2.vm.hostname = 'rabbit2'
    rabbit2.vm.provision :hostmanager
    rabbit2.vm.provision 'shell', inline: 'rabbitmqctl stop_app'
    rabbit2.vm.provision 'shell', inline: 'rabbitmqctl join_cluster --ram rabbit@rabbit1'
    rabbit2.vm.provision 'shell', inline: 'rabbitmqctl change_cluster_node_type disc'
    rabbit2.vm.provision 'shell', inline: 'rabbitmqctl start_app'
    rabbit2.vm.provision 'shell', inline: 'echo `rabbitmqctl cluster_status`'
  end

  config.vm.define :rabbit3 do |rabbit3|
    rabbit3.vm.box = 'ubuntu/trusty64'
    rabbit3.vm.provider :virtualbox do |v|
      v.name = 'rabbit3'
      v.customize ['modifyvm', :id, '--memory', '1024']
    end
    rabbit3.vm.network :private_network, ip: '10.211.11.102'
    rabbit3.vm.hostname = 'rabbit3'
    rabbit3.vm.provision :hostmanager
    rabbit3.vm.provision 'shell', inline: 'rabbitmqctl stop_app'
    rabbit3.vm.provision 'shell', inline: 'rabbitmqctl join_cluster --ram rabbit@rabbit1'
    rabbit3.vm.provision 'shell', inline: 'rabbitmqctl change_cluster_node_type disc'
    rabbit3.vm.provision 'shell', inline: 'rabbitmqctl start_app'
    rabbit3.vm.provision 'shell', inline: 'echo `rabbitmqctl cluster_status`'
  end

end