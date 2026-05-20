## Laboratory work X

Данная лабораторная работа посвещена изучению процесса создания и конфигурирования виртуальной среды разработки с использованием **Vagrant**

```sh
$ open https://www.vagrantup.com/intro/index.html
```

## Tutorial

```sh
$ export GITHUB_USERNAME=<имя_пользователя>
$ export PACKAGE_MANAGER=<пакетный_менеджер>
```

```sh
$ cd ${GITHUB_USERNAME}/workspace
$ sudo apt install vagrant -y
Следующие пакеты устанавливались автоматически и больше не требуются:
  libwoff1  linux-image-6.12.63+deb13-amd64
Для их удаления используйте «sudo apt autoremove».

Установка:
  vagrant

Установка зависимостей:
  vagrant-libvirt

Предлагаемые пакеты:
  virtualbox

Сводка:
  Обновление: 0, Установка: 2, Удаление: 0, Пропуск обновления: 129
  Объём загрузки: 705 kB
  Требуемое пространство: 4 749 kB / 7 926 MB доступно

Пол:1 http://deb.debian.org/debian trixie/main amd64 vagrant amd64 2.3.7+git20230731.5fc64cde+dfsg-3+b1 [632 kB]
Пол:2 http://deb.debian.org/debian trixie/main amd64 vagrant-libvirt all 0.12.2-4 [72,6 kB]
Получено 705 kB за 35с (20,3 kB/s)         
Выбор ранее не выбранного пакета vagrant.
(Чтение базы данных … на данный момент установлено 191502 файла и каталога.)
Подготовка к распаковке …/vagrant_2.3.7+git20230731.5fc64cde+dfsg-3+b1_amd64.deb
 …
Распаковывается vagrant (2.3.7+git20230731.5fc64cde+dfsg-3+b1) …
Выбор ранее не выбранного пакета vagrant-libvirt.
Подготовка к распаковке …/vagrant-libvirt_0.12.2-4_all.deb …
Распаковывается vagrant-libvirt (0.12.2-4) …
Настраивается пакет vagrant (2.3.7+git20230731.5fc64cde+dfsg-3+b1) …
Настраивается пакет vagrant-libvirt (0.12.2-4) …
Обрабатываются триггеры для man-db (2.13.1-1) …
```

```sh
$ vagrant version
Vagrant 2.3.8.dev
$ vagrant init bento/ubuntu-19.10
$ less Vagrantfile
$ vagrant init -f -m bento/ubuntu-19.10
```

```sh
$ mkdir shared
```

```sh
$ cat > Vagrantfile <<EOF
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
$ cat >> Vagrantfile <<EOF

Vagrant.configure("2") do |config|

  config.vagrant.plugins = ["vagrant-vbguest"]
EOF
```

```sh
$ cat >> Vagrantfile <<EOF

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

```sh
$ vagrant validate
Vagrantfile validated successfully.
$ vagrant status
Current machine states:
default                   running (libvirt)
$ vagrant up # --provider virtualbox
Bringing machine 'default' up with 'libvirt' provider...
==> default: Importing base box 'ubuntu/focal64'...
==> default: Creating shared folders...
==> default: Running provisioner...
==> default: Installing Docker...
==> default: VM successfully created and running!
$ vagrant ssh
vagrant@ubuntu-focal:~$ docker --version
Docker version 24.0.7
vagrant@ubuntu-focal:~$ exit
$ vagrant snapshot list
snapshot-001
$ vagrant snapshot push
Snapshot pushed: snapshot-001
$ vagrant halt
==> default: Halting domain...

$ vagrant snapshot pop
==> default: Restoring snapshot...
```

