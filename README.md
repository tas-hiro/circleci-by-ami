# circleci-by-ami

通常、CircleCIで test-kitchen を実行すると、
CircleCI上にレシピ適用のためのコンテナを立てて、Chefレシピを適用していく。

特定のAWS-AMIを使った環境で試験したい場合には、
kitchen-ec2 ドライバを使用すると、
AWS上にインスタンスを立て試験することができる。

便宜上、CircleCIにできるAWSを呼び出すコンテナをホスト、
AWSに作成され、実際のテストが行われる環境をゲストと呼ぶ。


# Gemfile
CircleCIのホストを定義するための設定。

# Berksfile
ゲストに適用するためのCookbookを収集する。
ファイル自体は一旦ホストに置かれる。

# circle.yml
ホスト側で実行される。

今回は3つの工程を行っている。
* `bundle install`
  Gemfileを参照してホストにパッケージを入れる。
* `berks vendor coocbooks/`
  Berksfileに書かれているCookbookを収集して保存する。
* `kitchen test`
  .kitchen.yml に従いテストを行う。

# .kitchen.yml
今回のキモ。
必要な情報を入れて、`kitchen test`すれば、run_listのテストが実行される。

* aws_ssh_key_id
  AWSインスタンスへログインするためのkey pair。
  AWS画面で先に作っておく。

* subnet_id
  EIPを付けるなど、パブリック通信ができないと当然ながら失敗する。

* aws_access_key_id/aws_secret_access_key
  絶対にファイルに書かないように。
  circleciで環境変数を登録できるので、今回は環境変数から取る。

* ssh_key
  CircleCIからAWSインスタンスへSSHするための鍵。
  .ssh/config とかに書いておくと不要かもね。
