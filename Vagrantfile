# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|

  config.vm.provider :libvirt do |domain|
    domain.autostart = true
  end

  config.vm.define "isp_monitor" do |isp_monitor|
    isp_monitor.vm.box = "generic/ubuntu2004"

    isp_monitor.vm.network :public_network,
      :ip => "192.168.250.6",
      :dev => "bridge0",
      :mode => "bridge",
      :type => "bridge"

    # default router
    isp_monitor.vm.provision "shell",
      run: "always",
      inline: "ip route add default via 192.168.250.1 || true"

    # delete default gw on eth0
    isp_monitor.vm.provision "shell",
      run: "always",
      inline: "eval `ip route list | awk '{ if ($5 ==\"eth0\" && $3 != \"0.0.0.0\") print \"ip route del default via \" $3; }'`"

    isp_monitor.vm.provision :ansible do |ansible|
      ansible.playbook = "monitor-router.yml"
      ansible.verbose = "v"
      ansible.become = true
      ansible.compatibility_mode = "2.0"
      ansible.playbook_command = "/home/kenneth/.pyenv/versions/isp-monitor-venv/bin/ansible-playbook"
      ansible.host_vars = {
        "default" => {
          "ansible_python_interpreter" => "/usr/bin/python3"
        }
      }
    end
  end

  config.vm.define "unifi" do |unifi|
    unifi.vm.box = "generic/debian9"
    unifi.vm.synced_folder ".", "/vagrant", disabled: true

    unifi.vm.network :public_network,
      :ip => "192.168.250.7",
      :dev => "bridge0",
      :mode => "bridge",
      :type => "bridge"

    unifi.vm.provision :ansible do |ansible|
      ansible.playbook = "unifi.yml"
      ansible.verbose = "v"
      ansible.become = true
      ansible.compatibility_mode = "2.0"
      ansible.host_vars = {
        "default" => {
          "ansible_python_interpreter" => "/usr/bin/python3"
        }
      }
    end
  end

end
