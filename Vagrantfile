ENV['VAGRANT_NO_PARALLEL'] = 'yes'

Vagrant.configure("2") do |config|

  ###################################################################################
  # define number of gitlab runners
  W = 1

  # provision W VMs as gitlab runners
  (1..W).each do |i|

    # name the VMs
    config.vm.define "gitlab-runner0#{i}" do |node|

      # which image to use
      node.vm.box = "generic/rocky8"

      # sizing of the VMs
      node.vm.provider "libvirt" do |lv|
        lv.random_hostname = true
        lv.memory = 1024
        lv.cpus = 2
      end

      # set the hostname
      node.vm.hostname = "gitlab-runner#{i}"

    end # config.vm.define gitlab runners

  end # each-loop agents

  ###################################################################################

  config.vm.define "gitlab-ce" do |node|

    # which image to use
    node.vm.box = "generic/rocky8"

    # sizing of the VMs
    node.vm.provider "libvirt" do |lv|
      lv.random_hostname = false
      lv.memory = 8196
      lv.cpus = 2
    end

    # set the hostname
    node.vm.hostname = "gitlab-ce"

    # disable shared folders
    node.vm.synced_folder ".", "/vagrant", disabled: true
    node.vm.provision "ansible" do |ansible|
      ansible.compatibility_mode = '2.0'
      ansible.limit = "all"
      ansible.groups = {
        "gitlab_runners"  => [ "gitlab-runner01" ]
      }
      ansible.playbook = "ansible/playbook-vagrant.yml"
    end # node.vm.provision

  end # config.vm.define

end # Vagrant.configure
