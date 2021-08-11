# Ubuntu 18.04设置静态IP



- 查看网关

  ![image-20210610172523052](https://cdn.jsdelivr.net/gh/NAMZseng/Picture/img/image-20210610172523052.png)

- sudo vim /etc/netplan/01-network-manager-all.yaml

  ```yaml
  network:
          ethernets:
                  enp2s0:
                          addresses: [192.168.2.196/24]
                          gateway4: 192.168.2.1
                          dhcp4: no
                          nameservers:
                                  addresses: [8.8.8.8, 8.8.4.4]
                                  search: [localdomin]
  
          version: 2
          renderer: NetworkManager
  ```

- 更新修改 sudo netplan apply