# limo_ros2_docker
Dockerを使ってLIMOをROS2から動かすための環境セットアップ用リポジトリ

# セットアップ手順

## 1. リポジトリをクローンする

~~~
git clone https://github.com/TechShare-inc/limo_ros2_docker.git
~~~

## 2. 必要な依存パッケージを用意する

~~~
sudo -i
~~~

root権限になるので以下を順に実行する

~~~
apt install -y curl

curl -s -L https://nvidia.github.io/nvidia-docker/gpgkey | sudo apt-key add -

distribution=$(. /etc/os-release;echo $ID$VERSION_ID)

curl -s -L https://nvidia.github.io/nvidia-docker/$distribution/nvidia-docker.list | sudo tee /etc/apt/sources.list.d/nvidia-docker.list

apt-get update

apt install -y nvidia-docker2

systemctl daemon-reload

systemctl restart docker

curl -L https://github.com/docker/compose/releases/download/v2.20.2/docker-compose-`uname -s`-`uname -m` > /usr/local/bin/docker-composesu

chmod +x /usr/local/bin/docker-compose

exit
~~~



## 3. dockerイメージをビルドする

~~~
cd limo_ros2_docker

sudo docker-compose build
~~~

## 4. dockerコンテナを作成・起動する

~~~
sudo docker-compose up -d
~~~

## 5. 作成されたコンテナに入る

~~~
sudo docker exec -it limo_foxy_dev bash
~~~
