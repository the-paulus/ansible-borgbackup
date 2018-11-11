
hostname_suffix = "example.com"

vms = {
  "server" => {
    box: "centos/7",
    cpus: 1,
    memory: 512,
    ip: "192.168.100.1",
    hostname: "server",
    aliases: [],
    ports: [],
    shares: [],
  },
  "archlinux" => {
    box: "archlinux/archlinux",
    cpus: 1,
    memory: 512,
    ip: "192.168.100.2",
    hostname: "archlinux",
    aliases: [],
    ports: [],
    shares: [],
  },
  "centos6" => {
    box: "centos/6",
    cpus: 1,
    memory: 512,
    ip: "192.168.100.3",
    hostname: "centos6",
    aliases: [],
    ports: [],
    shares: [],
  },
  "centos7" => {
    box: "centos/7",
    cpus: 1,
    memory: 512,
    ip: "192.168.100.4",
    hostname: "centos7",
    aliases: [],
    ports: [],
    shares: [],
  },
  "debian-jessie" => {
    box: "debian/jessie64",
    cpus: 1,
    memory: 512,
    ip: "192.168.100.5",
    hostname: "debian-jessie",
    aliases: [],
    ports: [],
    shares: [],
  },
  "wheezy" => {
    box: "debian/wheezy64",
    cpus: 1,
    memory: 512,
    ip: "192.168.100.6",
    hostname: "debian-wheezy",
    aliases: [],
    ports: [],
    shares: [],
  },
  "stretch" => {
    box: "debian/stretch64",
    cpus: 1,
    memory: 512,
    ip: "192.168.100.7",
    hostname: "debian-stretch",
    aliases: [],
    ports: [],
    shares: [],
  },
  "gentoo" => {
    box: "generic/gentoo",
    cpus: 6,
    memory: 2048,
    ip: "192.168.100.8",
    hostname: "gentoo",
    aliases: [],
    ports: [],
    shares: [],
  },
  "redhat6" => {
    box: "samdoran/rhel6",
    cpus: 1,
    memory: 512,
    ip: "192.168.100.9",
    hostname: "redhat6",
    aliases: [],
    ports: [],
    shares: [],
  },
  "redhat7" => {
    box: "generic/rhel7",
    cpus: 1,
    memory: 512,
    ip: "192.168.100.10",
    hostname: "redhat7",
    aliases: [],
    ports: [],
    shares: [],
  },
  "ubuntu-artful" => {
    box: "ubuntu/xenial64",
    cpus: 1,
    memory: 512,
    ip: "192.168.100.11",
    hostname: "ubuntu-artful",
    aliases: [],
    ports: [],
    shares: [],
  },
  "ubuntu-precise" => {
    box: "ubuntu/precise64",
    cpus: 1,
    memory: 512,
    ip: "192.168.100.12",
    hostname: "ubuntu-precise",
    aliases: [],
    ports: [],
    shares: [],
  },
  "ubuntu-trusty" => {
    box: "ubuntu/trusty64",
    cpus: 1,
    memory: 512,
    ip: "192.168.100.13",
    hostname: "ubuntu-trusty",
    aliases: [],
    ports: [],
    shares: [],
  },
  "ubuntu-xenial" => {
    box: "ubuntu/xenial64",
    cpus: 1,
    memory: 512,
    ip: "192.168.100.14",
    hostname: "ubuntu-xenial",
    aliases: [],
    ports: [],
    shares: [],
  },
}

inventory_groups = {
  "webservers" => [
    "archlinux",
    "centos6",
    "centos7",
    "debian8",
    "debian9",
    "gentoo",
    "ubuntu-artful",
    "ubuntu-precise",
    "ubuntu-trusty",
    "ubuntu-xenial",
  ]
}

host_vars = {
  "ubuntu" => {
    "ansible_python_interpreter" => "/usr/bin/python3"
  },
  "archlinux" => {
    "ansible_python_interpreter" => "/usr/bin/python2.7"
  }
}

Vagrant.configure("2") do |config|

  config.landrush.enabled = true
  config.landrush.tld = hostname_suffix
  config.landrush.guest_redirect_dns = false

  vms.each_pair do |vm_name, vm_config|

    config.vm.define vm_name do |config|
      config.vm.box = vm_config[:box]
      config.vm.hostname = vm_config[:hostname]
      config.vm.network :private_network, ip: vm_config[:ip]

      vm_config[:aliases].each { |vm_alias|
        config.landrush.host vm_alias, vm_config[:ip]
      }

      vm_config[:shares].each { |share|
          #config.vm.synced_folder share[:local], share[:remote]
      } # vm_config[:shares].each

      vm_config[:ports].each { |port_forwarding|
          config.vm.network :forwarded_port,
            host:  port_forwarding[:host_port],
            guest: port_forwarding[:guest_port],
            id:    port_forwarding[:id]
      } # vm_config[:ports].each

      config.vm.provider "virtualbox" do |vb|
        vb.name = vm_name
        vb.memory = vm_config[:memory]
        vb.cpus = vm_config[:cpus]
      end # :virtualbox

      config.vm.provision "ansible" do |ansible|
        ansible.groups = inventory_groups
        ansible.host_vars = host_vars
        ansible.playbook = "playbook.yml"
        #ansible.become = true

      end # :ansible

    end # config.vm.define
  end # vms.each_pair

end # Vagrant.config
