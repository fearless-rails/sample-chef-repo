VAGRANTFILE_API_VERSION = "2"

Vagrant.require_version ">= 1.5.0"

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
  config.vm.hostname = "sample-app.com"

  # Set the version of chef to install using the vagrant-omnibus plugin
  config.omnibus.chef_version = '11.10.4'

  config.vm.provider :virtualbox do |v|
    v.customize ["modifyvm", :id, "--memory", 1024, "--cpus", "2"]
  end

  # Every Vagrant virtual environment requires a box to build off of.
  config.vm.box = "opscode_ubuntu-12.04_provisionerless"

  # The url from where the 'config.vm.box' box will be fetched if it
  # doesn't already exist on the user's system.
  config.vm.box_url = "https://opscode-vm-bento.s3.amazonaws.com/vagrant/opscode_ubuntu-12.04_provisionerless.box"

  # Assign this VM to a host-only network IP, allowing you to access it
  # via the IP. Host-only networks can talk to the host machine as well as
  # any other machines on the same network, but cannot be accessed (through this
  # network interface) by any external networks.
  config.vm.network :private_network, ip: "192.168.3.11"

  ssh_key = File.read(File.expand_path('~/.ssh/id_rsa.pub'))

  config.vm.provision :chef_solo do |chef|
    chef.json = {
      mysql: {
        server_root_password: 'rootpass',
        server_debian_password: 'debpass',
        server_repl_password: 'replpass'
      },
      postgresql: {
        password: { "postgres" => "password" }
      },
      # gotta fix rvm for chef-solo on vagrant -_-
      rvm: {
        vagrant: {
          system_chef_solo:  "/usr/bin/chef-solo"
        }
      },
      "sample-app" => {
        deploy_keys: [ ssh_key ]
      }
    }

    chef.run_list = [
        "recipe[rvm::vagrant]",
        "recipe[sample-app::default]"
    ]
  end
end