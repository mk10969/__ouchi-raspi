# raspi4 初期設定手順


## IP固定化（手動）

https://tarufu.info/how_to_assign_static_ip_address_on_ubuntu/

各サーバに設定追加
```sudo vi /etc/netplan/99_config.yaml```
  フォルダごとに設定違う（~/raspi01, ~raspi02. ~raspi03）

  その後設定反映
```sudo netplan apply```


## hostnameの設定

hostnamectl で、hostname変更
```
# hostnamectl set-hostname <name>
```

## hosts追記

/etc/hostsに下記を追加
100.64.1.101 raspi4-01
100.64.1.102 raspi4-02
100.64.1.103 raspi4-03
100.64.1.104 raspi4-04
100.64.1.151 raspi0-01
100.64.1.201 raspco-01

## timezone 設定

sudo timedatectl set-timezone Asia/Tokyo

## 公開鍵の配布

公開鍵をscpで、コピーする。

## sshdの設定
basic認証を、Falseに設定する。

## firewallの設定

ufwコマンドで設定して確認できる。
https://qiita.com/siida36/items/be21d361cf80d664859c

https://kubernetes.io/ja/docs/setup/production-environment/tools/kubeadm/install-kubeadm/#%E5%BF%85%E9%A0%88%E3%83%9D%E3%83%BC%E3%83%88%E3%81%AE%E7%A2%BA%E8%AA%8D

こちら開放しておく。

## user新規作成（k8sで利用する専用ユーザ）
```
TODO
```

## swap off

cat /etc/fstab
→swapの行がないことを確認。

free
→swapが、0であることを確認。

## brigeの設定

[参考](https://kubernetes.io/ja/docs/setup/production-environment/tools/kubeadm/install-kubeadm/)

```
cat <<EOF > /etc/sysctl.d/k8s.conf
net.bridge.bridge-nf-call-ip6tables = 1
net.bridge.bridge-nf-call-iptables = 1
EOF
sysctl --system
```

## cgroupの設定

sudo vi /boot/firmware/cmdline.txt

末尾に下記を追加する。
cgroup_enable=cpuset cgroup_memory=1 cgroup_enable=memory



## install docker

[参考公式ドキュメント](https://docs.docker.com/engine/install/ubuntu/)

```sudo apt update```

```
sudo apt-get install \
    apt-transport-https \
    ca-certificates \
    curl \
    gnupg \
    lsb-release

curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg

echo \
  "deb [arch=arm64 signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu \
  $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list

sudo apt update

sudo apt install -y docker-ce docker-ce-cli containerd.io

sudo apt-mark hold docker-ce docker-ce-cli containerd.io

docker --version
```


## install kubernetes
```
curl -fsSL https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add -

echo "deb http://apt.kubernetes.io/ kubernetes-xenial main" | sudo tee /etc/apt/sources.list.d/kube.list

sudo apt update

sudo apt install -y kubelet kubeadm kubectl
```


## kubernetes構築

 - ref
https://kubernetes.io/ja/docs/setup/production-environment/tools/kubeadm/create-cluster-kubeadm/
https://qiita.com/reireias/items/0d87de18f43f27a8ed9b
https://sminamot-dev.hatenablog.com/entry/2020/01/26/111949


◯Masterで実行
```
sudo kubeadm init --pod-network-cidr=10.244.0.0/16
```
以下、fish shell
```
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown (id -u):(id -g) $HOME/.kube/config
```
flannelを利用
```
kubectl apply -f https://raw.githubusercontent.com/coreos/flannel/master/Documentation/kube-flannel.yml
```

◯Nodeで実行
```
kubeadm join 100.64.1.101:6443 --token <xxxxxxxxxxx> --discovery-token-ca-cert-hash sha256:<xxxxxxxxxxxxxxxxxxxxxxx>
```


## Node labelsを設定

kubectl label nodes raspi02 devicetype=bluetooth
kubectl label nodes raspi03 devicetype=infrared


## TODO

将来的に、Ansibleにしたいね！
