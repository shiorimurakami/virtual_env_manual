# バージョン一覧　
  
| 種類 | 指定条件 |
| - | - |
| プラグイン | Vagrant |
| ipアドレス | 192.168.33.19 |
| OS | CentOS7 |
| DB | MySQL5.7 |
| webサーバ | Nginx |
| php | ver. 7.3 |
| Laravel | ver. 6.0 |  
  
# 環境構築の流れ  
  
1. 使用しているPCにインストールされているOSとは別の仮想的なOS（ゲストOS）を立ち上げるためのソフトウェアである、VirtualBoxをインストールする  
    ```shell  
    virtualbox  
    ```  
    ※コマンド実行後は入力を受け付けなくなってしまうため、`Control + c`を押す  
  
2. コマンド１つで起動や破壊などが可能で、より効率良く仮想環境を管理・構築することができるVagrantをインストールするため、以下コマンドを入力する  
    ```shell  
    brew cask install vagrant  
    ```  
  
3. vagrantコマンドが使用可能かどうか、以下のコマンドを入力して確認をする（バージョン確認）  
   ```vagrant  
    vagrant -v  
    ```  
  
4. VagrantBoxをダウンロードするために、以下を実行する  
  
    - 今回はLinuxのCentOSのバージョン7のbox名centos/7を指定して実行する  
      ```vagrant  
      vagrant box add centos/7  
      ```  
  
    - 使用するソフトの選択を聞かれるので、今回はvirtualbox(3)を選択する  
      ```  
      Enter your choice:3  
      ```  
  
       以下が表示されたら完了  
      ```  
      Successfully added box 'centos/7' (v1902.01) for 'virtualbox'!  
      ```  
  
5. Vagrant作業用ディレクトリの作成  
    - Vagrant用のフォルダを作成したいディレクトリまで移動し、ターミナルで以下コマンドを入力する
  
      ```shell  
      mkdir フォルダ名（今回はserver_lesson11）  
      ```  
  
   - 作成したフォルダの中に移動し、vagrantのコマンドを使用するためvagrantfileを生成する（以下コマンドを入力する）  
      ```vagrant  
      vagrant init centos/7  
      ```  
  
6. Vagrantfileの編集を行うため、以下コマンドを入力する  
    ```vagrant  
    vi Vagrantfile  
    ```  
  
7. 以下3箇所の編集を行う（ゲストOSとホストOSを同期させるため）  
  
    - 上から26行目の先頭の#を外す（ポート及びポートフォワーディングの設定）  
      ```  
      config.vm.network "forwarded_port", guest: 80, host: 8080  
      ```  
  
    - 上から35行目の先頭の#を外し、ipアドレスを192.168.33.19に変更する（プライベートネットワークの設定）  
      ```  
      config.vm.network "private_network", ip: "192.168.33.10"  
      ```  
  
    - 上から47行目の先頭の#を外し、ホストOSのディレクトリ内（今回はserver_lesson11）とゲストOS (Vagrant) の /vagrant のディレクトリ内をリアルタイムで同期するための設定を行うために以下編集を行う  
      ```  
      config.vm.synced_folder "./", "/vagrant", type:"virtualbox"  
      ```  
  
8. vagrant-vbguestプラグインをインストールするため、以下コマンドを入力する  
     ```vagrant  
     vagrant plugin install vagrant-vbguest  
     ```  
  
      ※ vagrant-vbguest プラグインは、利用している VirtualBox のバージョンとゲストマシンにインストールされている Guest Additions のバージョンを調べて、ゲストマシンにインストールされている Guest Additions のバージョンが古ければアップデートしてくれる機能を持ったプラグイン  
  
9. Vagrantを起動するため、以下コマンドを入力する  
   ```vagrant  
   vagrant up  
   ```  
  
10. Vagrantが起動しているか確認するため、以下コマンドを入力する  
    ```vagrant  
    vagrant status  
    ```  
  
11. ゲストOSにログインするため、以下コマンドを入力する  
    ```vagrant  
    vagrant ssh  
    ```  
    ※ 成功していたら`[vagrant@localhost ~]$`という表記になる  
  
12. rootユーザーの権限を使ってパッケージのインストールを行うため、以下コマンドを入力する  
    ```yum  
    sudo yum -y groupinstall "development tools"  
    ```  
    ※ -y：オプションコマンドの一つで、インストール実行途中に yes/no と聞かれた場合に全て自動でyesと回答してくれる  
  
    ※ groupinstall：グループパッケージ（必要なパッケージの集合）をインストールできる  
  
13. phpのインストールを行うため、以下コマンドを入力する  
  
    - EPEL（Red Hat Enterprise Linux系Linuxディストリビューション向けオプションパッケージ郡）を使用するためにepel-releaseパッケージをインストール  
      ```yum  
      sudo yum -y install epel-release wget  
      ```  
  
    - wgetでURLを指定し、指定先URLのファイルをダウンロード  
      ```wget  
      sudo wget http://rpms.famillecollet.com/enterprise/remi-release-7.rpm  
      ```  
  
    - 依存関係まで管理してくれるLinuxディストリビューション内で使用できるパッケージ管理ツールの更新  
       ```rpm  
       sudo rpm -Uvh remi-release-7.rpm  
       ```  
    - PHPのインストールと同時に、PHPアプリケーションを動かす上で必要となるモジュール(拡張機能)をインストール  
      ```yum  
      sudo yum -y install --enablerepo=remi-php73 php php-pdo php-mysqlnd php-mbstring php-xml php-fpm php-common php-devel php-mysql unzip  
      ```  
  
14. phpのバージョンを確認するため、以下コマンドを入力する（今回は7.3）  
    ```php  
    php -v  
    ```  
  
15. phpのパッケージ管理ツールのcomposerをインストールするため、以下コマンドを入力する  
  
    ```php  
    php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');"  
    php composer-setup.php  
    php -r "unlink('composer-setup.php');"  
    ```  
  
16. どのディレクトリにいてもcomposerコマンドを使用を使用できるようfileの移動を行うため、以下コマンドを入力する  
    ```shell  
    sudo mv composer.phar /usr/local/bin/composer  
    ```  
  
17. composerのバージョンを確認するため、以下コマンドを入力する  
    ```  
    composer -v  
    ```  
  
18. Laravelをインストールするため、以下コマンドを入力する  
    ```composer  
    composer create-project laravel/laravel --prefer-dist laravel_app 6.0  
    ```  
  
19. Laravelがインストールできているか確認するため、artisanファイルがあるディレクトリまで移動し、以下コマンドを入力する  
    ```laravel  
    php artisan -v  
    ```  
  
20. ログイン機能の実装  
  
      ```composer  
      composer require laravel/ui "^1.0" --dev  
      php artisan ui vue --auth  
      ```  
  
21. Nginxのインストール  
  
    - viエディタを使用して以下のファイルを作成する  
      ```shell  
      sudo vi /etc/yum.repos.d/nginx.repo  
      ```  
  
    - 以下内容を記述する  
      ```  
      [nginx]  
      name=nginx repo  
      baseurl=http://nginx.org/packages/mainline/centos/\$releasever/\$basearch/  
      gpgcheck=0  
      enabled=1  
      ```  
  
    - 以下のコマンドを実行しNginxのインストールを行う  
  
      ```  
      sudo yum install -y nginx  
      ```  
  
22. Nginxのバージョンを確認するため、以下コマンドを入力する  
  
    ```nginx  
    nginx -v  
    ```  
  
23. Nginxを起動するため、以下コマンドを入力する  
  
    ```shell  
    sudo systemctl start nginx  
    ```  
  
24. ブラウザにて（今回は） http://192.168.33.10 と入力し、NginxのWelcomeページが表示されたらOK  
  
25. Nginxの設定ファイルを編集する  
    ※/etc/nginx/conf.d ディレクトリ下の default.conf ファイルが対象  
  
    ```shell  
    sudo vi /etc/nginx/conf.d/default.conf  
    ```  
  
    以下内容に編集  
  
    ```  
    server {  
        listen       80;  
        server_name  192.168.33.10;  
        ↑ Vagranfileでコメントを外した箇所のipアドレスを記述  
        root /vagrant/laravel_app/public; ←追記  
        index  index.html index.htm index.php; ←追記  
  
    〜省略〜  
  
    location / {  
      #root   /usr/share/nginx/html; ←コメントアウト  
      #index  index.html index.htm;  ←コメントアウト  
      try_files $uri $uri/ /index.php$is_args$args;  ←追記  
    }  
  
    〜省略〜  
  
    下記は root を除いたlocation { } までのコメントを解除  
    location ~ \.php$ {  
    #    root           html;  
      fastcgi_pass   127.0.0.1:9000;  
      fastcgi_index  index.php;  
      fastcgi_param  SCRIPT_FILENAME  /$document_root/$fastcgi_script_name;  # $fastcgi_script_name以前を /$document_root/に変更  
      include        fastcgi_params;  
    }  
  
26. php-fpm の設定ファイルを編集する  
  
    ```shell  
    sudo vi /etc/php-fpm.d/www.conf  
    ```  
  
    - 24行目  
      ```  
      user = apache 右記に編集→ user = nginx  
      group = apache 右記に編集→ group = nginx  
      ```  
  
27. Nginxを再起動するため、以下コマンドを入力  
  
    ```shell  
    sudo systemctl restart nginx  
    ```  
  
28. Nginxの状態を確認するため、以下コマンドを入力する  
  
    ```shell  
    sudo systemctl status nginx  
    ```  
  
29. php-fpmを起動する  
  
    ```shell  
    sudo systemctl start php-fpm  
    ```  
  
30. データベースのインストールを行うため、以下コマンドを入力する  
  
    - wgetでURLを指定し、指定先URLのファイルをダウンロード  
  
      ```shell  
      sudo wget http://dev.mysql.com/get/mysql57-community-release-el7-7.noarch.rpm  
      ```  
  
      ※今回はMySQLのバージョン5.7  
  
    - パッケージの更新を行う  
      ```shell  
      sudo rpm -Uvh mysql57-community-release-el7-7.noarch.rpm  
      ```  
  
    - 「MySQL Community Server」をインストール  
  
      ```shell  
      sudo yum install -y mysql-community-server  
      ```  
  
31. インストールが正常に行われたか確認するため、以下コマンドでバージョンの確認を行う  
    ```shell  
    mysql --version  
    ```  
  
32. MySQLを起動するため、以下コマンドを入力する  
    ```shell  
    sudo systemctl start mysqld  
    ```  
  
33. パスワードを調べて、再設定を行うため以下コマンドを入力する  
    ```musql  
    udo cat /var/log/mysqld.log | grep 'temporary password'  
    ```  
  
    ※ 下記表示のコロン以降（○の箇所）がパスワードとなる  
  
    ```mysql  
    2020-12-10T08:45:36.560757Z 1 [Note] A temporary password is generated for root@localhost:○○○  
    ```  
  
34. 以下コマンドでMySQLにアクセスし、33で調べたパスワードを入力する  
  
    ```mysql  
    mysql -u root -p  
    Enter password:○○○  
    ```  
  
35. MySQLに接続した状況で以下コマンドを入力すると新しいパスワードを設定ができる  
  
    ```mysql  
    mysql > set password = "新たなpassword";  
    ```  
  
    ※ 新たなpasswordには、必ず大文字小文字の英数字 + 記号かつ8文字以上の設定をする必要がある
  面倒臭い場合は、以下の通りにMySQLの設定ファイルを変更することで、シンプルなパスワードに初期設定できるようになる  
  
    - ファイルを編集するため、以下コマンドを入力する  
  
       ```  
       sudo vi /etc/my.cnf  
       ```  
  
    - 以下  
      ```  
      # read_rnd_buffer_size = 2M  
      datadir=/var/lib/mysql  
      socket=/var/lib/mysql/mysql.sock  
      ```  
      の下に次の１行を追加  
      ```  
      validate-password=OFF  
      ```  
  
     - MySQLの再起動を行うため、以下コマンドを入力する  
  
       ```musql  
       sudo systemctl restart mysqld  
       ```  
  
36. データベースの作成を行うため、以下コマンドを入力する  
  
    ```mysql  
    mysql > create database laravel_app;  
    ```  
  
    Query OKと表示されたらOK  
  
    ※ 今回は「laravel_app」という名前のデータベースを作成  
  
37. 一度MySQLを終了し、Vagrantにログインした状況で「laravel_app」のディレクトリまで移動し、.envファイルを編集するために、以下コマンドを入力する  
  
    ```shell  
    vi .env  
    ```  
  
38. DB_PASSWORD= と記載されている箇所があるので、= 以降に新しいパスワードを入力する  
  
    ```vi  
    DB_PASSWORD=登録したパスワード  
    ```  
  
39. 「laravel_app」ディレクトリに移動して、マイグレーションを実行する  
  
    ```php  
    php artisan migrate  
    ```  
  
40. ログイン機能を実装するために以下コマンドを入力する  
  
    - まずはlaravel/uiパッケージの追加を行う  
      ```composer  
      composer require laravel/ui "^1.0" --dev  
      ```  
  
    - Authに関するファイルの生成を行う  
      ```php  
      php artisan ui vue --auth  
      ```  
  
41. プラウザに「http://192.168.33.19」を入力し、無事にログインできたら完了！  
  
#  環境構築の所感  
  
仮想環境の構築を行うにあたり、最初はターミナルの操作がとても苦手だったので、Server_Lessonにも苦手意識がございました。  
カリキュラムを進める中でターミナルに何度も入力していくと、エラーが出た場合は、要因や試してみるべきことなどを丁寧に教えてくれていること、また、たった１文で幅広くいろんなことをやってくれるということがわかり、少しだけターミナルのことが好きになりました。  
  
仮想環境と一口に言っても何種類かあり、それぞれにメリット・デメリットがあるということがわかりました。  
- ホスト型  
- ハイパーバイザー型  
- コンテナ型  
  
今回VagrantとDockerを行ってみて、Dockerの方がゲストかホストにいるのか気を遣わなくてよいので、わかりやすく感じましたが、DockerよりもVagrantの方が一体化して（いるように感じて）おり、慣れるとVagrantの方が便利のように感じました。  
  
私がよく起こしてしまったミスは以下となります。  
- ファイルの編集後に再起動していない  
- artisan など、そのファイルが必要な場所でコマンドを実行しなければいけないのに、そのファイルがない場所でコマンドを実行していた  
- システムを起動していないのに、そのシステムのコマンドを入力していた  

編集した後は、システムの再起動を行うこと、システムの状況を確認すること（status）に特に気をつけるようにいたします。  
  
# 参考サイト  
  
- auth機能の追加  
[Laravelバージョン6.xの公式HP](https://readouble.com/laravel/6.x/ja/authentication.html)  
  
- rpmパッケージのオプションコマンドの確認  
  [rpm - システム管理コマンドの説明 - Linux コマンド集 一覧表](https://kazmax.zpp.jp/cmd/r/rpm.8.html)  
  
- viで１行すべて消すときの方法  
  [viで文字を削除するコマンド【色々な方法まとめました】](https://eng-entrance.com/linux-vi-delete)  
  
- マークダウンでテーブルを作る方法  
  [Markdown記法/書き方（見出し・表・リンク・画像・文字色など）](https://notepm.jp/help/how-to-markdown)  
