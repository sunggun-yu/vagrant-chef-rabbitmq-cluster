# Creating RabbitMQ cluster with Chef(Berks) and Vagrant

It made with Berkshelf, but, nothing in the default recipe. All the provisioning is set up in Vagrant file with Chef attribute and some shell provisioning.
RabbitMQ installation is performed by rabbitmq Chef cookbook. however, this cookbook doesn't supports the adding cluster. So, I use Vagrant shell provision to add nodes into the cluster.

## What this Vagrant provisioning is doing.
- Install 3 nodes of RabbitMQ server.
- Add disc node config for cluster : It put setting into rabbitmq.config file. but, no effects in actually. (probably, I may need `rabbitmqctl reset`. but I didn't try)
- Add user
 - admin/admin : for some reason, guest user, which is default user, cannot login to the manage UI.
 - Add administrator tag to the admin user
 - Give rights to the admin user : vhost => "/", conf => ".\*", write => ".\*", read => ".\*"
- Put erlang cookie.
- Add policy.
- Join cluster as disc node : rabbit2 and rabbit3

## Requirements
- Chef, Berkshelf : chefdk would be the best choice.
- Vagrant
 - vagrant-berkshelf plugin : `vagrant plugin install vagrant-berkshelf`
 - vagrant-omnibus plugin : `vagrant plugin install vagrant-omnibus`
 - vagrant-hostmanager plugin : `vagrant plugin install vagrant-hostmanager`


## Run Vagrant up
```
berks install
vagrant up
```