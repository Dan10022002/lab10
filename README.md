# Отчёт по lab10

## Tutorial

1. Создаём переменные

_export GITHUB_USERNAME=Dan10022002<br/>
export PACKAGE_MANAGER='sudo apt'_

2. Переходим в рабочую директорию, скачиваем vagrant

_cd ${GITHUB_USERNAME}/workspace<br/>
$ ${PACKAGE_MANAGER} install vagrant_

![vargrant](https://github.com/Dan10022002/lab10/blob/master/vagrant.png)

3. Проверяем правильность установки vagrant

_vagrant version<br/>
vagrant init bento/ubuntu-19.10<br/>
less Vagrantfile<br/>
vagrant init -f -m bento/ubuntu-19.10_

![version](https://github.com/Dan10022002/lab10/blob/master/version.png)

4. Создаём директорию для виртуальной среды

_mkdir shared_

5. Настраиваем конфигурационный файл

```sh
cat > Vagrantfile <<EOF
\$script = <<-SCRIPT
sudo apt install docker.io -y
sudo docker pull fastide/ubuntu:19.04
sudo docker create -ti --name fastide fastide/ubuntu:19.04 bash
sudo docker cp fastide:/home/developer /home/
sudo useradd developer
sudo usermod -aG sudo developer
echo "developer:developer" | sudo chpasswd
sudo chown -R developer /home/developer
SCRIPT
EOF
```

```sh
cat >> Vagrantfile <<EOF

Vagrant.configure("2") do |config|

  config.vagrant.plugins = ["vagrant-vbguest"]
EOF
```

```sh
cat >> Vagrantfile <<EOF

  config.vm.box = "bento/ubuntu-19.10"
  config.vm.network "public_network"
  config.vm.synced_folder('shared', '/vagrant', type: 'rsync')

  config.vm.provider "virtualbox" do |vb|
    vb.gui = true
    vb.memory = "2048"
  end

  config.vm.provision "shell", inline: \$script, privileged: true

  config.ssh.extra_args = "-tt"

end
EOF
```

6. Проверяем на корректность файл Vagrantfile, состояние виртуальной машины и запускаем её. Затем выводим список гостевых портов, проверяем статус машины и подключаемся к ней по ssh, проверяем список снимков, делаем снимок, завершаем работу виртуальной машины и извлекаем из стека снимок

_vagrant validate<br/>
vagrant status<br/>
vagrant up --provider virtualbox<br/>
vagrant port<br/>
vagrant status<br/>
vagrant ssh<br/>
vagrant snapshot list<br/>
vagrant snapshot push<br/>
vagrant snapshot list<br/>
vagrant halt<br/>
vagrant snapshot pop_

7. Настраиваем системные настройки для виртуальной машины

```sh
config.vm.provider :vmware_esxi do |esxi|

    esxi.esxi_hostname = '<exsi_hostname>'
    esxi.esxi_username = 'root'
    esxi.esxi_password = 'prompt:'

    esxi.esxi_hostport = 22

    esxi.guest_name = '${GITHUB_USERNAME}'

    esxi.guest_username = 'vagrant'
    esxi.guest_memsize = '2048'
    esxi.guest_numvcpus = '2'
    esxi.guest_disk_type = 'thin'
  end
```

8. Устанавливаем и запускаем плагин vmware_esxi

_vagrant plugin install vagrant-vmware-esxi<br/>
vagrant plugin list<br/>
vagrant up --provider=vmware_esxi_

![plugin](https://github.com/Dan10022002/lab10/blob/master/plugin.png)
