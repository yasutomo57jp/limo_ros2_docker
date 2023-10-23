[https://github.com/](https://github.com/TechShare-inc/limo_ros2_docker)をもとに，カスタマイズした環境です


# limo_ros2_docker
Dockerを使ってLIMOをROS2から動かすための環境セットアップ用リポジトリ

# セットアップ手順

## 1. リポジトリをクローンする

インターネットから必要なファイルをダウンロードするため、LIMO本体のインターネット接続が必要となるので適宜セットアップを済ませておく。

その後、LIMOのデスクトップ上でTerminalを開き、以下のコマンドを実行する。

~~~
cd ~

git clone https://github.com/TechShare-inc/limo_ros2_docker.git
~~~

## 2. 必要な依存パッケージを用意する

~~~
sudo -i
~~~

プロンプトがroot権限になるので以下のコマンドをを順に実行する。

~~~
apt install -y curl

curl -s -L https://nvidia.github.io/nvidia-docker/gpgkey | sudo apt-key add -

distribution=$(. /etc/os-release;echo $ID$VERSION_ID)

curl -s -L https://nvidia.github.io/nvidia-docker/$distribution/nvidia-docker.list | sudo tee /etc/apt/sources.list.d/nvidia-docker.list

apt-get update

apt install -y nvidia-docker2

systemctl daemon-reload

systemctl restart docker

curl -L https://github.com/docker/compose/releases/download/v2.20.2/docker-compose-`uname -s`-`uname -m` > /usr/local/bin/docker-compose

chmod +x /usr/local/bin/docker-compose

exit
~~~

最後の行を実行するとroot権限ではない一般ユーザーのプロンプトに戻る。



## 3. dockerイメージをビルドする

Terminalのプロンプトで以下のコマンドを実行する。

~~~
cd ~/limo_ros2_docker

sudo docker-compose build
~~~

## 4. ビルドしたイメージからdockerコンテナを作成・起動する

~~~
sudo docker-compose up -d
~~~

作成されたイメージやコンテナは保存されているので、ここまでの手順は一度行えば次からは行わなくてもよい。

# 使用方法

## 0. dockerコンテナを起動する

上記のセットアップ手順4で`docker-compose up`を行うとコンテナの作成と起動が同時に行われるが、LIMOを再起動したりコンテナを停止させた場合は、作成済みのコンテナを再度起動する必要がある。

その後、LIMOのデスクトップ上でTerminalを開き、以下のコマンドを実行する。

~~~
cd ~/limo_ros2_docker

sudo docker-compose start
~~~



## 1. dockerコンテナにGUIへのアクセス権限を付与する

~~~
xhost +local:
~~~

このコマンドはLIMOを再起動するごとに毎回必要になる。

~/.bashrcなどに記載しておくと自動で実行することも出来る。




## 2. dockerコンテナにアタッチする

~~~
sudo docker exec -it limo_foxy_dev bash
~~~

dockerコンテナ内のrootユーザーとしてのプロンプトが開く。（root@agilex:~/ros2_ws#と表示される）

以下、「別のプロンプトで」と記載されている場合は新しいターミナルを開くごとに上記のコマンドを実行して、dockerコンテナ内でコマンドを実行する。






## 3. LIMOをキーボードで操作する

まずLIMO本体とROS2を繋ぐベースノードを起動する。

dockerコンテナ内のプロンプトで

~~~
ros2 launch limo_bringup limo_start.launch.py
~~~





次にキーボード操作で速度指令トピックを送るノードを起動する。

別のプロンプトで

~~~
ros2 run teleop_twist_keyboard teleop_twist_keyboard
~~~

画面に表示されるメッセージに従って、速度指令を送信しLIMOを操作することが出来る。

起動しているノードを停止する際は、該当のプロンプトでCtrl+Cを押す。





## 4. 地図作成(Cartographer)

まずLIMO本体とROS2を繋ぐベースノードを起動する。

dockerコンテナ内のプロンプトで

~~~
ros2 launch limo_bringup limo_start.launch.py
~~~

次に、地図を作成するノードを起動する。

別のプロンプトで

~~~
ros2 launch limo_bringup cartographer.launch.py
~~~

続いてキーボード操作で速度指令トピックを送るノードを起動する。

別のプロンプトで

~~~
ros2 run teleop_twist_keyboard teleop_twist_keyboard
~~~

周囲の環境の地図を作成するため、キーボード操作でLIMOを走行させる。

ある程度走行させ終わったら、地図をファイルに保存する。

別のプロンプトで

~~~
ros2 run nav2_map_server map_saver_cli -f /root/sample_map
~~~

-fの後の`/root/sample_map`の部分はマップの保存先を示す部分なので、別のディレクトリに書き換えてもよい。

コマンドを実行すると、指定したディレクトリに.pgmと.yamlの2つのファイルが生成される。






## 5. 自律走行(Navigation2)

まずLIMO本体とROS2を繋ぐベースノードを起動する。

dockerコンテナ内のプロンプトで

~~~
ros2 launch limo_bringup limo_start.launch.py
~~~

次に、Navigation2のノード群を起動する。

別のプロンプトで

~~~
ros2 launch limo_bringup navigation2.launch.py map:=/root/sample_map.yaml
~~~

map:=の後の`/root/sample_map.yaml`の部分は読み込む地図の場所を指定する部分なので、作成した地図の保存先に合わせて適宜変更する。

可視化ツールのRvizが立ち上がるので、2D Pose Estimateをクリックした後に地図上でクリック&ドラッグして現在のLIMOの姿勢を与える。

次にNavigation2 Goalをクリックし、同様の手順で目的地の姿勢を与えると経路が生成されLIMOが走行を始める。

