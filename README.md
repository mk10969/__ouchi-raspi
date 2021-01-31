# raspi4 初期設定手順

## hostnameの設定

hostnamectl で、hostname変更

## hosts追記

/etc/hostsに下記を追加
192.168.3.101 raspi01
192.168.3.102 raspi02
192.168.3.103 raspi03

## timezone 設定

sudo timedatectl set-timezone Asia/Tokyo

## 公開鍵の配布

公開鍵をscpで、コピーする。

## firewallの設定

ufwコマンドで設定して確認できる。
https://qiita.com/siida36/items/be21d361cf80d664859c

https://kubernetes.io/ja/docs/setup/production-environment/tools/kubeadm/install-kubeadm/#%E5%BF%85%E9%A0%88%E3%83%9D%E3%83%BC%E3%83%88%E3%81%AE%E7%A2%BA%E8%AA%8D

こちら開放しておく。

## IP固定化

https://tarufu.info/how_to_assign_static_ip_address_on_ubuntu/

各サーバに設定追加
```sudo vi /etc/netplan/99_config.yaml```
  フォルダごとに設定違う（~/raspi01, ~raspi02. ~raspi03）

  その後設定反映
```sudo netplan apply```

## swap off

cat /etc/fstab
→swapの行がないことを確認。

free
→swapが、0であることを確認。

## install docker

[参考公式ドキュメント](https://docs.docker.com/engine/install/ubuntu/)

```sudo apt update```

```
sudo apt install -y \
    apt-transport-https \
    ca-certificates \
    curl \
    gnupg-agent \
    software-properties-common
```

curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -

sudo apt-key fingerprint 0EBFCD88

sudo apt install docker-ce docker-ce-cli containerd.io

sudo apt-mark hold docker-ce docker-ce-cli containerd.io

docker --version

## brigeの設定

[参考](https://kubernetes.io/ja/docs/setup/production-environment/tools/kubeadm/install-kubeadm/)

```
cat <<EOF > /etc/sysctl.d/k8s.conf
net.bridge.bridge-nf-call-ip6tables = 1
net.bridge.bridge-nf-call-iptables = 1
EOF
sysctl --system
```

## user新規作成（k8sで利用する専用ユーザ）

## kubernetes構築

https://kubernetes.io/ja/docs/setup/production-environment/tools/kubeadm/install-kubeadm/#kubeadm-kubelet-kubectl%E3%81%AE%E3%82%A4%E3%83%B3%E3%82%B9%E3%83%88%E3%83%BC%E3%83%AB

## cgroupの設定

sudo vi /boot/firmware/cmdline.txt

末尾に下記を追加する。
cgroup_enable=cpuset cgroup_memory=1 cgroup_enable=memory

## TODO

将来的に、Ansibleにしたいね！
