# gitlab_ce_vagrant_libvirt_ansible

This Vagrant setup creates a VM and installs [Gitlab CE (Community
Edition)](https://netbox.dev/).

The `main` branch uses openSUSE Leap 15.4 and the [gitlab-ce role by
alvistack](https://github.com/alvistack/ansible-role-gitlab_ce). Kudos to Wong
Hoi Sing Edison for creating the role!

There is also a `Rocky9` branch, that uses Rocky Linux 9 and the [install_gitlab
role](https://github.com/jjaswanson4/jdc/tree/main/roles/install_gitlab/)
by Josh Swanson.

Default OS is openSUSE Leap 15.4. Although that can be changed in the
Vagrantfile, please beware that this will break the Ansible provisioning.

## Vagrant

1. You need vagrant obviously. And ansible. And git...
1. Fetch the box, per default this is `opensuse/Leap-15.4.x86_64`, using
   `vagrant box add opensuse/Leap-15.4.x86_64`.
1. Make sure the git submodules are fully working by issuing `git submodule init
   && git submodule update`
1. Run `vagrant up`
1. Fetch the Gitlab root password by logging into the VM (`vagrant ssh`) and
   reading `/etc/gitlab/initial_root_password`.
1. Find out the VM's IP (e.g. `vagrant address gitlab-ce`, if you have vagrant
   address installed) and connect to http://IP:80.
1. Log in as `root` user using the password from the
   `/etc/gitlab/initial_root_password` file.
1. Party!

## Avoiding downloading the big RPM each time

As `vagrant destroy` completely removes the virtual machine, each `vagrant up`
requires download the big `gitlab-ce` RPM again.

In case you want to avoid that, download the RPM manually and place it inside
the `ansible/` folder. The Ansible provisioning will pick it up and transfer it
to the right place inside the VM, before the role tries to install it.

Make sure to pick the right version that is going to be installed:

`playbook-all_nodes-alvistack.gitlab_ce.yml` contains:

```yaml
gitlab_release: '16.0'
```

The role then translates this to `16.0.4` (in
`ansible/roles/alvistack.gitlab_ce/vars/main.yml`).

Hence downloading `gitlab-ce-16.0.4-ce.0.sles15.x86_64.rpm` and placing it in
the `ansible` folder will work.

## Disabling the Ansible provisioning

In case you do not want Ansible to install teleport (because you want to install
it yourself), just comment out the following lines in the `Vagrantfile`:

```hcl
    node.vm.provision "ansible" do |ansible|
      ansible.playbook = "ansible/playbook-all_nodes.yml"
    end # node.vm.provision
```

You also find all of the playbooks in the `ansible` folder.

## License

Apache 2.0

## Author Information

I am Johannes Kastl, reachable via kastl@b1-systems.de.
