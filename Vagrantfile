ENV['VAGRANT_DEFAULT_PROVIDER'] = 'libvirt'

cluster = {
  "kmaster" => { :ip => "192.168.122.11", :mgmt_mac => "52:54:00:15:63:c1", :mem => 2048, :cpu => 2 },
  "worker1" => { :ip => "192.168.122.12", :mgmt_mac => "52:54:00:15:63:c2", :mem => 2048, :cpu => 2 },
  "worker2" => { :ip => "192.168.122.13", :mgmt_mac => "52:54:00:15:63:c3", :mem => 2048, :cpu => 2 },
}

#system("virsh net-update default add ip-dhcp-host \"<host mac='52:54:00:15:63:c1' ip='192.168.122.11' />\" --live --config")
#system("virsh net-update default add ip-dhcp-host \"<host mac='52:54:00:15:63:c2' ip='192.168.122.12' />\" --live --config")
#system("virsh net-update default add ip-dhcp-host \"<host mac='52:54:00:15:63:c3' ip='192.168.122.13' />\" --live --config")

$script = <<-'EOF'
echo 'ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIKBYP8WLbAudznHNukAktWyDL1I0N5Wn8hus/re3ncI8' >> /home/vagrant/.ssh/authorized_keys && \
mkdir /root/.ssh && chmod 700 /root/.ssh && \
cp /home/vagrant/.ssh/authorized_keys /root/.ssh/  && \
mv /tmp/k3s /usr/local/bin/k3s && chmod +x /usr/local/bin/k3s
EOF

Vagrant.configure("2") do |config|

  config.vm.provision "file", source: "./k3s", destination: "/tmp/k3s"
  config.vm.provision "shell", inline: $script
  config.vm.synced_folder ".", "/vagrant", disabled: true

  cluster.each_with_index do |(node, info), index|

    if "#{node}" == "kmaster" then
      config.vm.define node do |cfg|
        cfg.vm.box = "ubuntu/1604"
        cfg.vm.hostname = node + '.local.test'
        cfg.vm.provider :libvirt do |vm|
          vm.management_network_name = "default"
          vm.management_network_address = "192.168.122.0/24"
           vm.management_network_mac = "#{info[:mgmt_mac]}"
          vm.memory = info[:mem]
          vm.cpus = info[:cpu]
        end # end provider
      end # end config
    else
      config.vm.define node do |cfg|
        cfg.vm.box = "ubuntu/1604"
        cfg.vm.hostname = node + '.local.test'
        cfg.vm.provider :libvirt do |vm|
          vm.management_network_name = "default"
          vm.management_network_address = "192.168.122.0/24"
           vm.management_network_mac = "#{info[:mgmt_mac]}"
          vm.memory = info[:mem]
          vm.cpus = info[:cpu]
        end # end provider
      end # end config
    end
  end # end cluster

end
