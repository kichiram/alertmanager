# alertmanager設定
[公式ドキュメント](https://prometheus.io/docs/alerting/latest/configuration/)
## 設定概要
* alertmanagerの設定ファイルを入れ替えます。
* prometheusの設定ファイルにalertmanagerの指定を追加します。

# alertmanager設定ファイルの入れ替え
#### 1. ダウンロード
```
$ cd; mkdir -p ~/alertmanager
$ cd ~/alertmanager; rm -rf *
$ wget https://raw.githubusercontent.com/kichiram/alertmanager/main/config/alertmanager/alertmanager.yml
$ ls
alertmanager.yml
```
#### 2. 設定ファイルのチェック
```
$ /etc/prometheus/alertmanager/amtool check-config alertmanager.yml 
Checking 'alertmanager.yml'  SUCCESS
Found:
 - global config
 - route
 - 0 inhibit rules
 - 3 receivers
 - 0 templates
```
#### 2. 設定ファイルの上書き
```
$ sudo cp -p alertmanager.yml /etc/prometheus/alertmanager/
```
#### 3. リスタート
```
$ sudo systemctl restart alertmanager.service
$ sudo systemctl status alertmanager.service
● alertmanager.service - Alertmanager processes alerts sent by client applications such as the Prometheus server.
   Loaded: loaded (/usr/lib/systemd/system/alertmanager.service; enabled; vendor preset: disabled)
   Active: active (running) since Tue 2022-03-08 00:25:02 UTC; 30s ago
     Docs: https://prometheus.io/docs/alerting/latest/alertmanager/
 Main PID: 10753 (alertmanager)
   CGroup: /system.slice/alertmanager.service
           └─10753 /etc/prometheus/alertmanager/alertmanager --config.file=/etc/prometheus/alertmanager/alertma...

Mar 08 00:25:03 ip-172-31-39-113.ap-northeast-1.compute.internal alertmanager[10753]: level=info ts=2022-03-08T...
-- 省略 --
```
#### 4. 確認
設定内容が確認できます。
```
http://<ホスト名>:9093/#/status
```
# メール確認準備
#### 1. mailコマンドインストール
メールを確認するためのコマンドをインストールします。
```
sudo yum install -y mailx
```
使い方：https://monologu.com/mail-command/
#### 2. 既存のメール削除
古いメールが残っているとわかりにくいので一旦削除します。
```
sudo rm /var/spool/mail/ec2-user
sudo rm /var/spool/mail/root
```

# prometheusの設定ファイルにalertmanagerの指定を追加
[alerting](https://github.com/kichiram/prometheus/tree/main/config/alerting)でprometheus.ymlを入れ替えます。

# alert確認
## alertmanagerで確認
```
http://<ホスト名>:9093/#/alerts
```
## mailで確認
ec2-userのメール確認
```
$ mail
```
rootのメール確認
```
$ sudo mail -u root
```
