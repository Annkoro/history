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
- pushやpull requestした時に行いたいことの設定をする
name: Rails CI/CD

on:
  push:
    branches: [main]

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@v2
    - name: Deploy 
      env:
        PRIVATE_KEY: ${{ secrets.PRIVATE_KEY }}
        USER_NAME: ${{ secrets.USER_NAME }}
        HOST_NAME: ${{ secrets.HOST_NAME }}
      run: |
        echo "$PRIVATE_KEY" > private_key && chmod 600 private_key
        ssh -o StrictHostKeyChecking=no -i private_key ${USER_NAME}@${HOST_NAME} 'cd アプリケーション名 &&
        git pull origin ブランチ名 &&
        ~/.rbenv/shims/bundle install &&
        ~/.rbenv/shims/bundle exec rails assets:precompile RAILS_ENV=production &&
        ~/.rbenv/shims/bundle exec rails db:migrate RAILS_ENV=production &&
        if [[ -e tmp/pids/puma.pid ]];then sudo kill $(cat tmp/pids/puma.pid); echo kill puma process;fi &&
        ~/.rbenv/shims/rails s -e production'
        #### 公開鍵をEC2サーバーに
        - c9から公開鍵を転送
        - scp -i ~/.ssh/practice-aws.pem ~/.ssh/id_rsa.pub ec2-user@ipアドレス:.ssh/id_rsa.pub
        - EC2側でauthorized_keysに公開鍵を許可
        - cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys
## 2022/04/17
### スライドショー機能を作る
- Skippr(スキッパー)は、スライドショー機能を作成できるjQueryプラグイン
- Skippr公式サイト => GitHub
