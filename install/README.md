# prometheusのインストール方法
EC2のインスタンス上で実施します。
#### 1. ダウンロード
##### 1.1. homeディレクトリにwgetを利用してダウンロードします。
```
$ cd 
$ wget https://github.com/prometheus/alertmanager/releases/download/v0.23.0/alertmanager-0.23.0.linux-amd64.tar.gz
--- 省略 ---
HTTP request sent, awaiting response... 200 OK
Length: 25115445 (24M) [application/octet-stream]
Saving to: ‘alertmanager-0.23.0.linux-amd64.tar.gz’

100%[========================================================================>] 25,115,445  27.1MB/s   in 0.9s   

2021-10-25 06:53:29 (27.1 MB/s) - ‘alertmanager-0.23.0.linux-amd64.tar.gz’ saved [25115445/25115445]
```
##### 1.2. ダウンロードされたことを確認して解凍します。
```
$ $ ls alertmanager-0.23.0.linux-amd64.tar.gz 
alertmanager-0.23.0.linux-amd64.tar.gz
$ tar zxvf alertmanager-0.23.0.linux-amd64.tar.gz 
alertmanager-0.23.0.linux-amd64/
alertmanager-0.23.0.linux-amd64/alertmanager.yml
alertmanager-0.23.0.linux-amd64/LICENSE
alertmanager-0.23.0.linux-amd64/NOTICE
alertmanager-0.23.0.linux-amd64/alertmanager
alertmanager-0.23.0.linux-amd64/amtool
```
#### 2. 動作確認
##### 2.1. 解凍したディレクトリに移動して実行します。
```
$ cd alertmanager-0.23.0.linux-amd64
$ $ ./alertmanager --config.file=alertmanager.yml
level=info ts=2021-10-25T06:55:53.383Z caller=main.go:225 msg="Starting Alertmanager" version="(version=0.23.0, br
anch=HEAD, revision=61046b17771a57cfd4c4a51be370ab930a4d7d54)"
--- 省略 ---
```
###### もし下記のようなerrorが出力されて失敗する場合は既に起動したプロセスが残っている可能性があります。
```
$ ./alertmanager --config.file=alertmanager.yml
--- 省略 ---
level=error ts=2021-10-25T07:01:25.500Z caller=main.go:250 msg="unable to initialize gossip mesh" err="create memb
erlist: Could not set up network transport: failed to obtain an address: Failed to start TCP listener on \"0.0.0.0
\" port 9094: listen tcp 0.0.0.0:9094: bind: address already in use"
```
###### その場合は下記コマンドで実行されているプロセスを削除した後、再実行してください。
```
$ pgrep prometheus | xargs sudo kill
```
##### 2.2. ブラウザでアクセスできるか確認します。
```
http://<ホスト名>:9093
```
下記のように表示されれば成功です。
![image](https://user-images.githubusercontent.com/91726058/138649757-f4313d5e-d8c3-448b-9ff5-46617f53e44b.png)
* ホスト名の確認方法は[EC2インスタンスへの接続方法](../../aws/connect_ec2_instance/README.md)の「2.2」をご確認ください。余談ですが、alertmanagerはIPアドレスで接続するとダメみたいです。
##### 2.3. 実行中を中断します。
EC2のインスタンスに戻りCTRL-Cで実行を中断します。
#### 3. 常駐プロセス化
daemon（常駐プロセス）にして管理しやすいようにします。
##### 3.1. ファイル整理
ファイルを/etc/prometheus/alertmanagerディレクトリを作成し、その配下に移動します。
```
$ sudo mv ~/alertmanager-0.23.0.linux-amd64 /etc/prometheus/alertmanager
$ ls /etc/prometheus/alertmanager
alertmanager  alertmanager.yml  amtool  data  LICENSE  NOTICE
```
##### 3.2. daemonの設定ファイル作成
```
$ sudo vi /usr/lib/systemd/system/alertmanager.service
[Unit]
Description=Alertmanager processes alerts sent by client applications such as the Prometheus server.
Documentation=https://prometheus.io/docs/alerting/latest/alertmanager/
After=network-online.target

[Service]
Type=simple
ExecStart=/etc/prometheus/alertmanager/alertmanager --config.file=/etc/prometheus/alertmanager/alertmanager.yml --storage.path=/etc/prometheus/alertmanager/data

[Install]
WantedBy=multi-user.target
```
##### 3.3. daemonの自動起動設定と起動
```
$ sudo systemctl enable alertmanager.service
Created symlink from /etc/systemd/system/multi-user.target.wants/alertmanager.service to /usr/lib/systemd/system/a
lertmanager.service.
$ sudo systemctl start alertmanager.service
```
##### 3.4. 起動確認
```
$ sudo systemctl status alertmanager.service
● alertmanager.service - Alertmanager processes alerts sent by client applications such as the Prometheus server.
   Loaded: loaded (/usr/lib/systemd/system/alertmanager.service; enabled; vendor preset: disabled)
   Active: active (running) since Mon 2021-10-25 07:24:15 UTC; 3s ago
     Docs: https://prometheus.io/docs/alerting/latest/alertmanager/
 Main PID: 15794 (alertmanager)
   CGroup: /system.slice/alertmanager.service
           └─15794 /etc/prometheus/alertmanager/alertmanager --config.file=/etc/prometheus/alertmanager/alertma...

Oct 25 07:24:15 ip-172-31-39-113.ap-northeast-1.compute.internal systemd[1]: Started Alertmanager processes al....
--- 省略 ---
```
active (running)と表示されていれば成功です。念のためブラウザでアクセスできるか確認しましょう。
```
http://<ホスト名>:9093
```
