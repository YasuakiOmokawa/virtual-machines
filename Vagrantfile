Vagrant.configure("2") do |config|

  config.vm.box = "ubuntu/trusty64"

  # ポートフォワーディング設定
  config.vm.network "forwarded_port", guest: 3000, host: 3000
  config.vm.network "forwarded_port", guest: 3100, host: 3100

  # ホスト側のファイルをリアルタイムで同期させる
  config.vm.synced_folder ".", "/vagrant", type:"virtualbox"

  # Dockerインストール
  config.vm.provision "docker"

  # Docker-composeインストール(バージョン番号は適宜、自分で設定すること)
  $get_compose = <<-'EOF'
    curl -L https://github.com/docker/compose/releases/download/1.23.2/docker-compose-`uname -s`-`uname -m` > /usr/local/bin/docker-compose
    sudo chmod +x /usr/local/bin/docker-compose
    /usr/local/bin/docker-compose --version
  EOF
  config.vm.provision "shell", run: "always", inline: $get_compose

  # Docker-compose起動(フルパス記述が嫌ならリンクを貼ること)
  # ファイルパスはホスト側がマウントされた/vagrantディレクトリから始まる
  config.vm.provision "shell", inline: "/usr/local/bin/docker-compose -f /vagrant/docker/compose_test/docker-compose.yml up -d", run: "always"
end

  # docker composeではないdocker導入（参考として残すだけ)
  # config.vm.provision "docker" do |d|
  #   d.build_image "/vagrant", args: "-t omokawa/webapp"
  #   d.run "omokawa/webapp", args: "-d -t -v /vagrant:/webapp -p 3000:3000" # ゲストの3000番->ホスト(vagrant)の3000番にフォワーディング
  # end
