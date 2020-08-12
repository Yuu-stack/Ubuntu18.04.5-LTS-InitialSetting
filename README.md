# Ubuntu18.04.5-LTS-InitialSetting

Ubuntuでの固定IP化  

`sudo vim /etc/netplan/99_config.yaml`  


    network:
      version: 2
      renderer: networkd
      ethernets:
        eth0:
          dhcp4: false
          dhcp6: false
          addresses: [192.168.1.70/24]
          gateway4: 192.168.1.1
          nameservers:
            addresses: [192.168.1.1, 8.8.8.8, 8.8.4.4]
            
            

KVMやLXDなどで、物理マシンの外に公開するサーバーを建てたい場合にはブリッジの設定をすると思う。その場合には下記のように記述します。(そして仮想nicをbr0に差し込む)  

# 事前準備
設定をする前にブリッジ機能の有効化をしておく  

`$ sudo apt install -y bridge-utils`  
`$ brctl show`  
ブリッジ構成時の設定ファイル  

    network:
      version: 2
      renderer: networkd
      ethernets:
        eth0:
          dhcp4: false
          dhcp6: false
      bridges:
        br0:
          interfaces: [eth0]
          dhcp4: false
          dhcp6: false
          addresses: [192.168.1.70/24]
          gateway4: 192.168.1.1
          nameservers:
            addresses: [192.168.1.1, 8.8.8.8, 8.8.4.4]
          parameters:
            forward-delay: 0
            stp: false
          optional: true


IPアドレス設定変更の反映  
netplan applyを実行して反映します。再起動不要です。  


`$ sudo netplan apply`  

IPアドレスの確認  
ifconfigコマンドがデフォルトでインストールされなくなったので、ipコマンドで確認。  

`$ ip addr`  
(2020-05-28 自分用にメモ追記)  
ネットワークの状態を詳細に確認するなら、下記コマンドが便利。  


`$ networkctl status -a`  


参考:Ubuntu 20.04 LTSで固定IPアドレスの設定  
https://qiita.com/zen3/items/757f96cbe522a9ad397d
