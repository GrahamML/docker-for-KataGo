# docker-for-KataGo
[KataGo](https://github.com/lightvector/KataGo)の実行環境を構築するためのdockerfileを提供します。

# 1. 前提条件  
KataGoの動作要件に対する追加、制約要件は以下の通りです。 
+ Linux x86_64
+ NVIDIA GPU
+ Docker
+ nvidia-docker2 (Docker >= 1.12) or nvidia-container-toolkit (Docker >= 19.03)  

_[Docker](https://github.com/Microsoft/MMdnn/blob/master/docs/InstallDockerCE.md) と [nvidia-container-toolkit](https://github.com/NVIDIA/nvidia-docker#quickstart)のインストール方法に関する情報_  

# 2. インストール方法
## 2.1. ダウンロード
以下のファイルをダウンロードしてください。
1. CUDA10.2-linux用 [Katago-v1.4.5](https://github.com/lightvector/KataGo/releases) リリースパッケージ  
&emsp;`katago-v1.4.5-cuda10.2-linux-x64.zip`
1. 訓練済みウェイト・ファイル (20/30/40ブロック)  
&emsp;`g170e-b20c256x2-s5303129600-d1228401921.bin.gz`  
&emsp;`g170-b30c320x2-s4824661760-d1229536699.bin.gz`  
&emsp;`g170-b40c256x2-s5095420928-d1229425124.bin.gz`

次に本リポジトリをクローンしてください。  
```
$ git clone https://github.com/GrahamML/docker-for-KataGo.git
```
## 2.2. Dockerイメージのビルド
ダウンロードしたパッケージとウェイト・ファイルをローカルリポジトリへコピーし、ビルドしてください。
```console
$ cd ./docker-for-KataGo/dockerfile
$ cp ~/Downloads/katago-v1.4.5-cuda10.2-linux-x64.zip .
$ cp ~/Downloads/g170*.gz .
$ docker build --tag=['image_name:tag'] . 
```
+ このdockerfileは[NVIDIA CUDA official docker image](https://hub.docker.com/r/nvidia/cuda/)の上にダウンロードしたパッケージをインストールします
+ Linuxアカウントがdockerグループに属していない場合は`docker`コマンドを`sudo docker`コマンドへ置き換えてください
+ 以下はビルドコマンドの一例です  
    ```
    $ docker build --tag=katago:latest . 
    ```
+ 生成されるコンテナ内のUbuntu、CUDA、cuDNNのバージョンは以下です  

    | Ubuntu | CUDA Toolkit        | cuDNN          |
    |--------|---------------------|----------------|
    | 18.04  | 10.2.89             | 7.6.5.32       |

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
2020-06-25 10:24:19+0000: Loading model and initializing benchmark...
2020-06-25 10:24:19+0000: nnRandSeed0 = 11436641883002196670
:
Possible numbers of threads to test: 1, 2, 3, 4, 5, 6, 8, 10, 12, 16, 20, 24, 32, 40, 48, 64, 80, 96,
:
numSearchThreads =  5: (baseline)
numSearchThreads =  6:   +48 Elo
numSearchThreads = 10:  +128 Elo
numSearchThreads = 12:  +156 Elo
numSearchThreads = 16:  +175 Elo
numSearchThreads = 20:  +214 Elo
numSearchThreads = 24:  +240 Elo
numSearchThreads = 32:  +256 Elo (recommended)
numSearchThreads = 40:  +232 Elo
numSearchThreads = 48:  +226 Elo
numSearchThreads = 64:  +226 Elo

If you care about performance, you may want to edit numSearchThreads in /workspace/katago/default_gtp.cfg based on the above results!
If you intend to do much longer searches, configure the seconds per game move you expect with the '-time' flag and benchmark again.
If you intend to do short or fixed-visit searches, use lower numSearchThreads for better strength, high threads will weaken strength.
If interested see also other notes about performance and mem usage in the top of /workspace/katago/default_gtp.cfg
```

### GTP モード
```console
$ cd katago
$ ./katago gtp \
    -model g170-b30c320x2-s4824661760-d1229536699.bin.gz \
    -config default_gtp.cfg  
KataGo v1.4.5
Using TrompTaylor rules initially, unless GTP/GUI overrides this
Loaded config default_gtp.cfg
Loaded model g170-b30c320x2-s4824661760-d1229536699.bin.gz
Model name: g170-b30c320x2-s4824661760-d1229536699
GTP ready, beginning main protocol loop
```  
+ オプションや起動モードの詳細は [KataGo's readme](https://github.com/lightvector/KataGo) を参照ください

# 4. Lizzieとの連携  
この[wiki](https://github.com/GrahamML/docker_for_AQ/wiki/Communitacion-with-Lizzie)を参照ください。  

![Select](https://github.com/GrahamML/docker_for_AQ/wiki/images/Communitacion-with-Lizzie/Fig7.png)

# 5. ライセンス  
[MIT](https://github.com/GrahamML/docker_for_KataGo/blob/master/LICENSE)