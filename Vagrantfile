# Check to determine whether we're on a windows or linux/os-x host,
# later on we use this to launch ansible in the supported way
# source: https://stackoverflow.com/questions/2108727/which-in-ruby-checking-if-program-exists-in-path-from-ruby
def which(cmd)
    exts = ENV['PATHEXT'] ? ENV['PATHEXT'].split(';') : ['']
    ENV['PATH'].split(File::PATH_SEPARATOR).each do |path|
        exts.each { |ext|
            exe = File.join(path, "#{cmd}#{ext}")
            return exe if File.executable? exe
        }
    end
    return nil
end

Vagrant.configure("2") do |config|

    config.vm.provider :virtualbox do |v|
        v.name = "vagrant-skeleton"
        v.customize [
            "modifyvm", :id,
            "--name", "vagrant-skeleton",
            "--memory", 2048,
            "--natdnshostresolver1", "on",
            "--cpus", 2,
        ]
    end

    config.vm.box = "ubuntu/trusty64"

    config.vm.box_url = "https://vagrantcloud.com/ubuntu/boxes/trusty64/versions/14.04/providers/virtualbox.box"

    config.vm.network :private_network, ip: "192.168.56.128"

    config.ssh.forward_agent = true

    #############################################################
    # Ansible provisioning (you need to have ansible installed)
    #############################################################

    if which('ansible-playbook')
        config.vm.provision "ansible" do |ansible|
            ansible.playbook = "ansible/playbook.yml"
            ansible.inventory_path = "ansible/inventories/development"
            ansible.limit = 'all'
            ansible.extra_vars = {
                private_interface: "192.168.56.128",
                hostname: "vagrant-skeleton"
            }
        end
    else
        config.vm.provision :shell, path: "ansible/bootstrap.sh", args: ["vagrant-skeleton"]
    end

    # see https://github.com/mitchellh/vagrant/issues/4204, but doesn't work well with winnfsd
    config.vm.synced_folder "./", "/vagrant", type: "nfs"

end
