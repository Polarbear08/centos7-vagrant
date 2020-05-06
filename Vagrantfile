# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|

  # 対象Box名
  config.vm.box = "centos/7"

  # 外部から接続するためのIP
  # config.vm.network "private_network", ip: "192.168.33.10"

  # 共有フォルダ
  # config.vm.synced_folder "./share", "/mnt/vagrant", :mount_options => ["dmode=775,fmode=775"]

  # CPU, メモリサイズ
  config.vm.provider "virtualbox" do |vb|
    vb.cpus = 1
    vb.memory = 1024
  end

  # ポートフォワーディング
  # config.vm.network "forwarded_port", guest: 3128, host: 3128

  # updateをチェックする
  config.vm.box_check_update = true

  # [初回のみ] 必要なソフトウェアをインストールする
  config.vm.provision "shell", inline: <<-SHELL
    echo -------------------------
    echo Install Docker
    echo "See https://docs.docker.com/engine/install/centos/"
    echo -------------------------
    # 既にDockerが入っていたら、アンインストールする
    yum remove docker \
                  docker-client \
                  docker-client-latest \
                  docker-common \
                  docker-latest \
                  docker-latest-logrotate \
                  docker-logrotate \
                  docker-engine

    # 依存パッケージのインストール
    yum install -y yum-utils device-mapper-persistent-data lvm2

    # Docker CE をインストール
    yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
    yum makecache fast
    yum install -y docker-ce

    # Dockerグループにvagrantを追加
    usermod -aG docker vagrant

    # Dockerサービスを起動
    systemctl enable docker
    systemctl start docker
    echo $(docker --version)
    echo ""

    echo -------------------------
    echo Install Docker Compose
    echo "See https://docs.docker.com/compose/install/"
    echo -------------------------
    curl -L https://github.com/docker/compose/releases/download/1.25.5/docker-compose-`uname -s`-`uname -m` -o /usr/local/bin/docker-compose
    chmod 755 /usr/local/bin/docker-compose
    ln -s /usr/local/bin/docker-compose /usr/bin/docker-compose
    echo $(docker-compose --version)
    echo ""

    echo -------------------------
    echo Install MySQL
    echo "See https://dev.mysql.com/doc/mysql-installation-excerpt/5.7/en/"
    echo -------------------------
    yum localinstall -y https://dev.mysql.com/get/mysql57-community-release-el7-11.noarch.rpm
    yum install -y mysql-community-server

    # mysqldサービス起動
    systemctl enable mysqld
    systemctl start mysqld
    echo $(mysql --version)
    echo ""

    echo -------------------------
    echo Install Git
    echo "See https://git-scm.com/download/linux"
    echo -------------------------
    # 必要なパッケージをインストール
    yum install -y wget openssl-devel libcurl-devel expat-devel

    # git本体を入手
    mkdir -p /usr/local/src
    cd /usr/local/src
    wget https://mirrors.edge.kernel.org/pub/software/scm/git/git-2.26.2.tar.gz
    tar xf git-2.26.2.tar.gz
    rm -rf git-2.26.2.tar.gz

    # gitをビルド
    cd git-2.26.2
    make prefix=/usr/local all
    make prefix=/usr/local install
    ln -s /usr/local/bin/git /usr/bin/git
    cd $HOME
    echo $(git --version)
    echo ""
  SHELL
end
