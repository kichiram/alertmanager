# silence
アラート通知を停止したいはサイレンスを登録します。サイレンスはlabelを指定して行なえるので柔軟に設定できます。また、APIも利用可能です。

[非公式ドキュメント](https://hawksnowlog.blogspot.com/2021/02/disable-notification-with-alertmanager.html)

#### 1. UIを起動
UIを開きます。
```
http://<ホスト名>:9093
```
#### 2. サイレンス登録ページを開く
発生しているアラートの「Silence」または右上の「New Silence」をクリックします。
![image](https://user-images.githubusercontent.com/91726058/172295421-4fec77ed-fd9c-4d6b-a7bf-81278e8b8cc6.png)
#### 3. サイレンスを登録する
発生しているアラートの「Silence」から遷移した場合は下記の通りlabelが「Matchers Alerts affected by this silence」に設定されます。
![image](https://user-images.githubusercontent.com/91726058/172296052-b691971c-e66a-493e-996a-2438153667de.png)
##### Start
サイレンスの開始時間を設定します（デフォルト現在日時）
##### Duration
サイレンスの有効期間を設定します（デフォルト2時間）　※Endを入力すると上書きされます
##### End
サイレンスの終了時間を設定します（デフォルト現在日時＋2時間）　※Durationを入力すると上書きされます
##### Matchers
一致する条件のアラートの通知が停止されます。複数指定する場合はAND条件となります。正規表現を利用できます。正規表現を利用する場合は記号を「=~」とします。
##### Creator
必須入力で作成者を設定しますが、特にチェックは無いので任意の文字列となります
##### Comment
必須入力でコメントを設定しますが、特にチェックは無いので任意の文字列となります

今回はMatchersに ```severity=~"warn.*"``` を入力、CreatorとCommentは適当な値をいれてください

「Preview Alerts」ボタンを押し「Affected alerts」にアラートが表示されることが確認出来たら「Create」ボタンを押します。
![image](https://user-images.githubusercontent.com/91726058/172298548-2e13f37f-66c8-4537-9e80-d6ef6c350b0b.png)

上記の様に表示されれば登録完了です。
#### 3. アラートが表示されないことを確認する
上部の「Alerts」タブを選択し、「No alert groups found」となっていることを確認する
![image](https://user-images.githubusercontent.com/91726058/172298784-62dcd4b0-d28c-4887-99d9-75b01f411db3.png)
#### 4. サイレンスを無効にする
上部の「Silences」タブを選択すると登録されたサイレンスが表示されます。「Expire」ボタンでサイレンスを無効にできます
![image](https://user-images.githubusercontent.com/91726058/172299132-9d4b202e-69b0-4480-89b4-8353eae919c5.png)
