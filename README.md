# Ubuntu18.04.5-LTS-InitialSetting

# この項目でできること

> 01.初期設定  vim設定
> 02.IP固定 


<s>vim 等の設定は RasPi-InitialSetting を参考にしてください.<s>  
<s>https://github.com/Yuu-stack/RasPi-InitialSetting/blob/master/README.md<s>  


# 01.🍓初期設定  vim設定+IP固定

    sudo timedatectl set-timezone Asia/Tokyo && \
    sudo localedef -f UTF-8 -i en_US en_US && sudo localedef -f UTF-8 -i ja_JP ja_JP && \
    sudo localectl set-locale LANG=en_US.utf8 && \
    sudo apt update && sudo apt upgrade -y && \
    sudo apt autoremove -y && \
    dpkg -l | grep vim && \
    sudo apt --purge remove -y vim-common vim-tiny && \
    sudo apt install vim-gtk -y && sudo cp /etc/vim/vimrc /etc/vim/vimrc.bak && \
    wget -O .vimrc https://gist.github.com/Yuu-stack/afc3644c76d10dc39bd4c0ad48a0bc86/raw/6ca7b465dae295db9789a4bdd6806a1629610d11/.vimrc && \
    sudo cp ~/.vimrc /etc/vim/vimrc


# 02.🍓Ubuntuでの固定IP化  

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
