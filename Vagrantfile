# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|

  config.vm.define "scrutiny-build" do |sb|
    sb.vm.box = "debian/bookworm64"
    sb.vm.box_check_update = "false"

    sb.vm.hostname = "scrutiny-build"

    sb.vm.provider "virtualbox" do |vb|
      vb.cpus = 4
      vb.memory = 8192
    end

    sb.vm.network "forwarded_port", guest: 22, host: 1222, id: "ssh"

    sb.vm.provision "docker" do |d|
      d.build_image "/vagrant", args: "--file /vagrant/docker/Dockerfile --tag analogj/scrutiny:dev-omnibus"
      #d.build_image "/vagrant", args: "--file /vagrant/docker/Dockerfile.collector --tag analogj/scrutiny:dev-omnibus"
      #d.build_image "/vagrant", args: "--file /vagrant/docker/Dockerfile.web --tag analogj/scrutiny:dev-omnibus"
    end

    sb.vm.provision "shell", inline: "docker image save --output /vagrant/scrutiny:dev-omnibus.tar analogj/scrutiny:dev-omnibus"
  end

  config.vm.define "scrutiny-run" do |sr|
    sr.vm.box = "debian/bookworm64"
    sr.vm.box_check_update = "false"

    sr.vm.hostname = "scrutiny-run"

    sr.vm.provider "virtualbox" do |vb|
      vb.cpus = 4
      vb.memory = 8192
    end

    sr.vm.network "forwarded_port", guest: 22, host: 2222, id: "ssh"
    sr.vm.network "forwarded_port", guest: 8080, host: 8080, id: "http"

    sr.vm.provision "docker" do |d|
      d.post_install_provision "shell", inline: "docker image load --input /vagrant/scrutiny:dev-omnibus.tar"
      d.run "analogj/scrutiny:dev-omnibus", args: "--cap-add SYS_RAWIO --device /dev/sda:/dev/sda --name scrutiny --publish 8080:8080 --volume /udev:/udev:ro", auto_assign_name: false
    end

  end

end
