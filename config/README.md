# alertmanagerの設定ファイルについて
[公式ドキュメント](https://prometheus.io/docs/alerting/latest/configuration/)
### 1. global
共通的な設定を記述します。
### 2. [route](https://prometheus.io/docs/alerting/latest/configuration/#route)
アラートの設定を記述します。
#### 2.1. receiver
アラート受信先を記述します。
#### 2.2. group_by
アラート通知のグルーピングを設定します。
#### 2.3. match/match_re
アラート通知の条件を指定します。
#### 2.4. repeat_interval
再通知するまでの間隔を指定します。（アラートが消えた場合はリセットされます）
### 3. [receiver](https://prometheus.io/docs/alerting/latest/configuration/#receiver)
アラート受信先を設定します。emal / slack / webhook / PagerDutyなどへの通知が可能です。
