# ubuntu
ubuntu.md
共有なし
種類
テキスト
サイズ
6 KB (6,194 バイト)
使用容量
11 KB (10,927 バイト)
パス
開発
オーナー
自分
変更
2020/08/25、自分
閲覧
5:12、自分
作成
2020/04/03（Google Drive Web）
説明を追加
閲覧者はダウンロード可
ubuntu

[package auto update]
    sudo dpkg-reconfigure --priority=low unattended-upgrades

[vim] 
    sudo apt install -y vim

[repository]
    #offical repositry list
    /etc/apt/sources.list

    #Third-party repositry list *.list(apt-repository file) 
    /etc/apt/sources.list.d/*.list
        #for Example
        /etc/apt/sources.list.d/atareao-ubuntu-atareao-xenial.list
        /etc/apt/sources.list.d/atareao-ubuntu-atareao-xenial.list.save

    #the way of using add command.
        #usage1:add to sources.list.
            #for Example (wineHQ)
            wget -nc https://dl.winehq.org/wine-builds/winehq.key
            sudo apt-key add wine.key
            add-apt-repository `deb https://dl.winehq.org/wine-bulids/ubuntu/ xenial main`

        #usage2:add to sources.list.d/*.list (ppa/username:Lauchpad.net)
            #for Example (ffmpeg-4)
            sudo add-apt-repository ppa:jonathonf/ffmpeg-4

    #the way of using delete command.
        add-apt-repository --remove REPOSITORY
        #usage1:delete to sources.list.
            sudo apt-add-repository --remove `deb https://dl.winehq.org/wine-builds/ubuntu/ xenial main`

        #usage2:delete to sources.list.d/*.list (ppa/username:Launchpad.net)
            sudo-apt-repository --remove ppajonathonf/ffmpeg-4

[sudoers]
    sudo -i
    sudo export VISUAL=vim
    sudo visudo
    #initial values
        root All=(All:All) All
        %sudo All=(All:All) All
        %admin All=(All:All) All
     #for Example
        username RangeOfSudo = ( user : group ) /fullpath/command
        user 192.168.1.0/24 = ( sudo -u : sudo -g ) /fullpath/command
        user All = ( sudo -u ) /fullpath/command
      #add the follewing items last line to setting file by visudo. 
        ishida All=(root) /sbin/shutdown

[portNo & servies chcek]
    ss -lptu    #ss is used to dump socket statistics TCP&UDP
    systemctl list-utits --type=service    #service list show
        #cups          ##print
        #avahi-daemon  ##local-network device auto detect
        #cups-browsed  ##local-network printer
        #if there is cups, it run command1, command2 and command3
        #command1: sudo systemctl stop cups
        #command2: sudo systemctl disable cups
        #command3: sudo apt remove cups-daemon 

[clamav]
    sudo apt install clamav clamav-daemon libclamunrar7
    *** a few time after check it ***
        /var/lib/clamav/bytecode.cvd
        /var/lib/clamav/daily.cvd
        /var/lib/clamav/main.cvd
        /var/lib/mirrors.dat 
    sudo systemctl start clamav-daemon

    #if list of exclution to add file(or directory)
    sudo vim /etc/clamav/clamd.conf
        ExcludePath /dev    #directory(or file)
   
    #that command which find file of malware.
    clamdscan --fdpass ~/testfile 

    #that command which find malware for in dirctory.
    sudo clamdscan --fdpass /
 
[sshd]
    [client]
    pwgen -y 12 1
    ssh-keygen
        Enter passphrase (empty for no passphrase):
        Enter same passphrase again:
        your identfication has been saved in /home/***/.ssh/id_rsa.
        your public key has been saved in /home/***/.ssh/id_rsp.pub.
    #id_rsa.pub move to server /home/~/.ssh/
    scp -p 50022 id_rsa.pub(sourcefile) username@server(or ipaddress):/home/username/
    [server]
    mkdir /home/username/.ssh
    chmod 700 ~username/.ssh
    cat /home/user/id_rsa.pub /home/user/.ssh/authorized_keys
    rm id_rsa.pub
    chmod 600 ~username/.ssh/

    sudo apt install -y ssh
        vim /etc/ssh/sshd_config
            PermitRootLogin no
            PasswordAuthentication no
            ChallengeResponseAuthentication no
            RSAAuthentication yes
            PubkeyAuthentication yes
            Port 50022
    sudo systemctl reload sshd

[ufw]
    #install application which Firewall-utilty
    sudo apt install -y ufw

    #check state of service.
    sudo ufw status numbered
        status inactive (or active)

    #set up port only.
    sudo ufw default deny
    sudo ufw allow 50022
        #for Example
            #set up ip address.
            sudo ufw allow(or deny) from "ipaddress" to any app "ApplicationName"
            sudo ufw allow(or deny) from "ipaddress" to any port "ServiceName"
            sudo ufw allow(or deny) from "ipaddress" to any port "PortNo"
            sudo ufw allow(or deny) from "ipaddress" to any port "PortNo" proto "protocol"
            #delete rule
            sudo ufw delete "ruleNo"

    #enable(or reload) service.
    sudo ufw enable (or reload)

[fail2ban]
    #install application which protect intrusion.
    sudo apt install -y fail2ban

    #edit setting file.
    sudo vim /etc/fail2ban/jail.local
        ***create new configure file***
        [DEFAULT]
        enabled = true
        port = 50022
        bantime = 86400
        findtime = 3600
        maxretry = 10
        ignoreip = 127.0.0.1/8 192.168.11.0/24

    #add the following items last line to setting flie.
    sudo vim /etc/fail2ban/filter.d/sshd.config
        failregex = 
          ^%(__prefix_line)sConnection closed by <HOST> \[preauth\]$

    sudo systemctl start fail2ban

[rkhunter]
    #install application which check rootkit.
    sudo apt install -y rkhunter

    #edit setting file.
    sudo vim /etc/default/rkhunter
    	CRON_DAILY_RUN="true"
        CRON_DB_UPDATE="true"
        DB_UPDATE_EMIAL="ture"

     #rkhunter is reflect by command after setting file was change.   
     sudo rkhunter --propupd

     #check system with command.
     sudo rkhunter --check --sk
    

[gnupg]
    sudo apt install gnupg
    gpg --gen-key
        select RSA and RSA
        keylength 4096
        expiration date of the key 0
        fullname input
        email input
        comment none
    gpg --export tshr08@yahoo.co.jp > /tmp/toshiharu.gpgpubkey
    gpg --import toshiharu.gpgpubkey
    gpg --encrypt --recpient tshr08@yahoo.co.jp sourceflie
        #sourcefile encryptfile
    gpg --decrypt encryptfile > sourceflie
        #passphrase input     

