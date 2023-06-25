# k8s-behavior-localstorage
https://www.netmarvs.com/archives/5332
を参考にローカルストレージとk8sを接続する方法を調査しました
## ※ポイント※minikubeにとってHOST OSのローカルストレージとは、minikubeコンテナの中になる
https://www.netmarvs.com/archives/5332
によると、コンテナからストレージにアクセスするとふつうはHOST OSのストレージと接続されるんだけども…
minikubeの場合は、ちょっと事情が違って、Dockerの中にminikubeっていうコンテナが作られてその中でpodやらserviceが動くので
少々勝手が異なる。

https://minikube.sigs.k8s.io/docs/handbook/persistent_volumes/
に解説あるけど、全文英語なので注意

### 検証方法
1. デプロイ
```
kubectl apply -f kube-pod-deploy.yaml
kubectl apply -f kube-service.yaml
```
2. index.phpの作成
minikubeにログイン
```
minikube ssh
```
index.phpの作成(ディレクトリの権限みるとrootじゃないと権限不足で何もできない)
※デフォルトだと/dataまでしかないけどkube-pod-deploy.yamlをデプロイしたときに/data/wwwが作られる
```
sudo vi /data/www/index.php
```
3. 動作確認
```
minikube service rocky-linux --url
```
ブラウザで作成されたphpファイルが表示される

# ConfigMapを使って設定ファイルを読み込ませる感じで使う
k8sのConfigmap機能を調べてみました
参考：https://qiita.com/oguogura/items/68741b91b70962081504

## ConfigMap機能ってどんな機能？
* アプリケーションの設定情報（例：データベースの接続情報、APIキー、URLなど）を保存する
* アプリケーションの設定情報を環境変数やコマンドライン引数としてアプリケーションに渡す
* アプリケーションの設定情報をコンフィグファイルにマウントしてアプリケーションから読み込む
* ConfigMapは、アプリケーションの設定情報を一元管理することができるため、アプリケーションのデプロイやスケールアップなどの作業を簡素化することができます。
ほかにも実践的な使い方が紹介されてるのはこちら
https://qiita.com/yackrru/items/c85c5f142e1ec2441963