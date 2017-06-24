# we use array here, becasue we may want to input more parameters later
hosts = {
  'host1' => [51],
  'host2' => [52],
  # 'host3' => [53],
}


Vagrant.configure('2') do |config|
  config.vm.box = 'ubuntu/trusty64'
  config.vm.box_check_update = false
  # Default is 2200..something, but port 2200 is used by forescout NAC agent.
  config.vm.usable_port_range = 3800..3900

  # Do not generate a key
  config.ssh.insert_key = false
  config.ssh.private_key_path = ['~/.ssh/id_rsa', '~/.vagrant.d/insecure_private_key']
  config.vm.provision 'file', source: '~/.ssh/id_rsa.pub', destination: '~/.ssh/authorized_keys'

  # Cachier will cache the apt install between hosts
  unless Vagrant::Util::Platform.windows?
    if Vagrant.has_plugin?('vagrant-cachier')
      config.cache.scope = :box
      config.cache.enable :apt
      config.cache.synced_folder_opts = {
        type: :nfs,
        mount_options: ['rw', 'vers=3', 'tcp', 'nolock']
      }
    else
      puts '[-] WARN: This would be much faster if you ran vagrant plugin install vagrant-cachier first'
    end
  end

  hosts.each do |hostname, (hostip)|
    config.vm.define hostname.to_s do |box|
      box.vm.hostname = hostname.to_s
      box.vm.network :private_network, ip: "10.10.10.#{hostip}",   netmask: '255.255.255.0'
      box.vm.network :private_network, ip: "192.168.10.#{hostip}", netmask: '255.255.255.0'
      box.vm.network :private_network, ip: "172.16.10.#{hostip}",  netmask: "255.255.255.0"
      box.vm.provider 'virtualbox' do |vb|
        # Display the VirtualBox GUI when booting the machine
        vb.gui = false

        vb.memory = if hostname == '50-controller'
                      '4096'
                    else
                      '1024'
                    end
        vb.cpus = 1
      end
    end
  end
end
