# nfs server install (deprecated)

- ref
https://www.server-world.info/query?os=Ubuntu_20.04&p=kubernetes&f=13

[1] NFS クライアントの設定

```text
sudo apt -y install nfs-common
```

マウントするコマンド

```text
mount -t nfs 192.168.3.101:/data/nfsshare /mnt
```

[2] システム起動時に、自動的にマウントする(されないのだがw)

```text
sudo vim /etc/fstab

// 最終行に追記：マウントするディレクトリをNFSサーバーのものに変更
192.168.3.101:/data/nfsshare /mnt               nfs     defaults        0 0
```

[3]	システム起動時にではなく、マウントポイントへのアクセス時に、動的に NFS 共有をマウント

```text
sudo apt -y install autofs
```

```text
vi /etc/auto.master
# 最終行に追記
/-    /etc/auto.mount
```

```text
vi /etc/auto.mount

/mnt   -fstype=nfs,rw  192.168.3.101:/data/nfsshare
```

```text
systemctl restart autofs
```
