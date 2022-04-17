## 2022/04/15
### 非同期通信について
- rails6 以降は form_with の非同期化は local_false。
- link_to は remote_true で OK!
## 2022/04/17
### GitHub Actionsについて
#### GitHub上での設定
- デプロイするアプリのリポジトリでsettings => secrets => actions => new secretで、下記3つを作成する
- １つ目がHOST_NAME = EC2のパブリックIPアドレス
- USER_NAME = ec2-user
- PRIVATE_KEY = EC2上かCloud9上に存在する~/.ssh/id_rsaの値 (cat ~/.ssh/id_rsaででたののBEGIN RSA PRIVATE KEYからEND RSA PRIVATE KEYまでをコピペ)
- add secretで3つを保存
#### ワークフローの作成
