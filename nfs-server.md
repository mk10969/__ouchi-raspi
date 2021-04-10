# nfs server install (deprecated)

- ref
https://www.server-world.info/query?os=Ubuntu_20.04&p=kubernetes&f=13

- master node

```text
sudo apt -y install nfs-kernel-server
sudo mkdir -p /data/nfsshare
```

```text
sudo vim /etc/exports

# 最終行にマウント設定を記述
/data/nfsshare 192.168.3.0/24(rw,no_root_squash)
```

```text
sudo systemctl enable nfs-server
sudo systemctl restart nfs-server
```
