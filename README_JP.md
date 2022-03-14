# docker-for-KataGo
[KataGo](https://github.com/lightvector/KataGo) v1.10.0の実行環境を構築するためのdockerfileを提供します。  

_Click [here](https://github.com/GrahamML/docker-for-KataGo/blob/master/README.md) for the README in English._

# 1. 前提条件  
KataGoの動作要件に対する追加、制約要件は以下の通りです。 
+ Linux x86_64
+ NVIDIA GPU
+ Docker
+ nvidia-docker2 (Docker >= 1.12) or nvidia-container-toolkit (Docker >= 19.03)  

_[Docker](https://github.com/Microsoft/MMdnn/blob/master/docs/InstallDockerCE.md) と [nvidia-container-toolkit](https://github.com/NVIDIA/nvidia-docker#quickstart)のインストール方法に関する情報_  

# 2. インストール方法
## 2.1. ダウンロード
本リポジトリをクローンしてください。  
```
$ git clone https://github.com/GrahamML/docker-for-KataGo.git
```
## 2.2. Dockerイメージのビルド
```console
$ cd ./docker-for-KataGo/dockerfile
$ docker build --tag=['image_name:tag'] . 
```
+ このdockerfileは[NVIDIA CUDA official docker image](https://hub.docker.com/r/nvidia/cuda/)の上に、以下のリリースパッケージとウェイトファイルダウンロードし、インストールします  
&emsp;` katago-v1.10.0-cuda11.1-linux-x64.zip `  
&emsp;`g170e-b20c256x2-s5303129600-d1228401921.bin.gz`  
&emsp;`g170-b30c320x2-s4824661760-d1229536699.bin.gz`  
&emsp;`g170-b40c256x2-s5095420928-d1229425124.bin.gz`
+ Linuxアカウントがdockerグループに属していない場合は`docker`コマンドを`sudo docker`コマンドへ置き換えてください
+ 以下はビルドコマンドの一例です  
    ```
    $ docker build --tag=katago:latest . 
    ```
+ 生成されるコンテナ内のUbuntu、CUDA、cuDNNのバージョンは以下です  

    | Ubuntu | CUDA Toolkit        | cuDNN          |
    |--------|---------------------|----------------|
    | 18.04  | 11.1.1              | 9.0.5          |

# 3. 実行方法
## 3.1. Dockerイメージの起動
生成したDockerイメージを以下のように起動してください。  
```console
$ docker run \
    -it \
    --net host \
    --runtime nvidia \
    --entrypoint /bin/bash \
    --name [container_name] \
    [image_name:tag]
```  
+ 上の例は`nvidia-docker2`の場合です。`nvidia-container-toolkit`の場合は適宜変更してください  

## 3.2. KataGoの起動  
コンテナ内でKataGoを起動してください。以下はbenchmarkモードとGTPモードで起動する例です。
### Benchmark モード
```console
$ cd katago
$ ./katago benchmark \
    -model g170-b40c256x2-s5095420928-d1229425124.bin.gz
```

### GTP モード
```console
$ cd katago
$ ./katago gtp \
    -model g170-b30c320x2-s4824661760-d1229536699.bin.gz \
    -config default_gtp.cfg  
```  
+ オプションや起動モードの詳細は [KataGoのreadme](https://github.com/lightvector/KataGo) を参照ください  
+ ルールを変更する場合は、起動中のコンテナにおいて `/workspace/katago/default_gtp.cfg`の中の`rules = tromp-taylor`の部分を書き換えてください

# 4. Katago分散トレーニングへの参加
このDockerコンテナを使って簡単に「Katago分散トレーニング」へ参加することができます。
以下の手順を参照ください。

+ ユーザアカウントを作成します。詳細は[https://katagotraining.org/](https://katagotraining.org/)の"How to Contribute"を参照ください 
+ コンテナ内の `./katago/contribute_example.cfg` ファイルを書き換えます
  + username = Fill in your username here
  + password = Fill in your password here
  + maxSimultaneousGames = Fill in a number here  
    使用するGPUの性能に応じて4, 8, 16, ... 等の数値を設定します。詳細はcfgファイル内コメント行の解説を参照ください
+ コマンドラインから `contribute` オプションを付してKatagoを起動します
  ```sh
  $ ./katago contribute -config contribute_example.cfg
  ```  
+ 参加者のステータスは [https://katagotraining.org/contributions/ ](https://katagotraining.org/contributions/)に一覧で表示されます

# 5. Lizzieとの連携  
この[wiki](https://github.com/GrahamML/docker_for_AQ/wiki/Communitacion-with-Lizzie)を参照ください。  

![Select](https://github.com/GrahamML/docker_for_AQ/wiki/images/Communitacion-with-Lizzie/Fig7.png)

# 6. ライセンス  
[MIT](https://github.com/GrahamML/docker_for_KataGo/blob/master/LICENSE)
