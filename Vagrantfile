require 'yaml'

VAGRANTFILE_API_VERSION = "2"

base_dir = File.expand_path(File.dirname(__FILE__))
inventory_file = base_dir + "/hosts.yaml"

servers = YAML.load_file(inventory_file)
meta = servers["_meta"]

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
  
  config.vm.box = "ubuntu/trusty64"
  config.ssh.insert_key = false

  servers.each do |ansible_key,vars|
    vars["hosts"].each_with_index do |host,i|
    
      ip  = meta["hostvars"][host]["vagrant_ip"]  ? meta["hostvars"][host]["vagrant_ip"]  : "10.0.0.#{i + 10}"
      cpu = meta["hostvars"][host]["vagrant_cpu"] ? meta["hostvars"][host]["vagrant_cpu"] : 1
      mem = meta["hostvars"][host]["vagrant_mem"] ? meta["hostvars"][host]["vagrant_mem"] : 2048
      
      config.vm.define "#{host}" do |node|
        node.vm.network "private_network", ip: "#{ip}"
        node.vm.hostname = "#{host}"
        config.vm.provider "virtualbox" do |v|
          v.memory = "#{mem}"
          v.cpus = "#{cpu}"
        end
      end
      
    end if vars["hosts"]
  end

end