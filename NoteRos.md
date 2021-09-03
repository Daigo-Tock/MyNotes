# ROSのお勉強
宮前 俊治

九州大学鳥人間チーム


---

## 今日の内容

* ROS

---

## ROS: robot operating system
* ロボットのソフトウェアコンポーネントを作って動作させるためのフレームワーク・ミドルウェア
* Linux (Ubuntu) 上での動作がデフォルト
* サイト
	*　公式ページ: http://www.ros.org/
	*　マニュアル等: http://wiki.ros/org/ja

## ROS
* 例にもれずオープンソース

## どんなものなのか
 * ROS本体はプロセス間通信を司る
 	*　簡単な制御というのは => 一つのfor文でぐるぐる回す
 	*　複雑なことをしようと思うと人のプログラムが必要になる => for文の中に自分で組み込むのは面倒
 	*　プロセスが独立して動かして、それ同士が情報を交換すれば良いよね。
 	*　けど、プロセス同士では、メモリが独立して、直接読み取ることができない
 	*　そこで、ROSがその情報の媒をしてくれる
 	*　まあ、自分たちでソケットを開いてもいいんだけど、いちいちデータの型を決めないと行けない
 	*　統一するルールをROSが提示しているから、その煩雑さも解消される。再利用性が高い。
*　通信方式
	*　カメラみたいに垂れ流しだったりとか、ひたすらトリガーがあるまで待つとかによって、データの取扱がある
	*　そういう通信パターンがいろいろあるけど、ROSでは
	*　publish-subscribeモデルやclient-serverモデルで通信を提供している。
*　周辺べんり機能
	*　ビルドシステム
	*　パッケージ管理
	*　テストツール

## ROSをインストール

* https://github.com/mirano-Pat/ros_setup_scripts_Ubuntu20.04_server
* このサイトにある ```bash step0.bash```と```bash step1.bash```をcopy and pasteで完了
* インストールが完了したかどうか確認するには
	```bash
	$ cat ~/.bashrc
	> ...
	>     . /etc/bash_completion
	>  fi
	> fi
	> source /opt/ros/noetic/setup.bash
	> export ROS_MASTER_URI=http://localhost:11311
	> export ROS_HOSTNAME=localhost
	> ```

* 最後の３行を確認できれば多分大丈夫

## ROSを動かす
```bash
$ roscore
> 
.. logging to /home/mirano-pat/.ros/log/76ed3670-0bce-11ec-8b6f-0b3c12ebd59f/roslaunch-miranopat-Z97X-UD3H-34725.log  
Checking log directory for disk usage. This may take a while.  
Press Ctrl-C to interrupt  
Done checking log file disk usage. Usage is <1GB.  
  
started roslaunch server http://localhost:46063/  
ros_comm version 1.15.11  
  
  
SUMMARY  
========  
  
PARAMETERS  
 * /rosdistro: noetic  
 * /rosversion: 1.15.11  
  
NODES  
  
auto-starting new master  
process[master]: started with pid [34736]  
ROS_MASTER_URI=http://localhost:11311/  
  
setting /run_id to 76ed3670-0bce-11ec-8b6f-0b3c12ebd59f  
process[rosout-1]: started with pid [34749]  
started core service [/rosout]  
```

## workspaceを作る

* ワークスペスを作って設定まで
```bash
$ mkdir -p catkin_ws/src // オプション -p は、多階層のディレクトリを一回で作る
$ cd !$ // 直前に作ったディレクトリパスが!$に格納されている
$ catkin_init_workspace
> Creating symlink "/home/user/catkin_ws/src/CMakeLists.txt" pointing to "/opt/ros/noetic/share/catkin/cmake/toplevel.cmake"
$ ls
> CMakeLists.txt
$ vim ~/.bashrc // 加筆
>>>
  fi
fi
source /opt/ros/noetic/setup.bash
source ~/catkin_ws/devel/setup.bash // ここを追加
export ROS_MASTER_URI=http://localhost:11311
export ROS_HOSTNAME=localhost
``` 

```bash
$ cd ~/catkin_ws
$ pwd
> /home/user/catkin_ws // catkin_ws直下で作業すること
$ catkin_make // PCにはなにもインストールして内容で、標準でついてくるものがいろいろあるから、それのビルドが始まる。
$ source ~/.bashrc
$ echo $ROS_PACKAGE_PATH // できているかどうかの確認
> /home/user/catkin_ws/src:/opt/ros/noetic/share // これが表示されればOK
```

## ROSパッケージ
* インストールは2種類
	* aptを使う
	* ~/catkin_ws/src にファイルをおいてビルドして使う 
		* 自分でパッケージを作るときはこの方法
### 例えば、カメラ画像をwebに表示したいなら

* パッケージの準備 apt編
```bash 
$ sudo apt install ros-noetic-cv-camera // openCVのカメラ画像を読み込むパッケージ
$ sudo apt install ros-noetic-cv-bridge // openCVの世界とrosの世界のデータを交換するパッケージ。多分インストールはもうされているはず
```
** ros関係のパッケ０時をaptで扱う設定 
** => step1.bashが/etc/apt/sources.list.d/ros-latest.listに書き込み済み

* パッケージの準備 ソフトウェア編
```bash
$ cd ~/catkin_ws/src
$ git clone https://github.com/GT-RAIL/async_web_server_cpp.git // 
$ git clone https://github.com/RobotWebTools/web_video_server.git // 
$ cd ~/catkin_ws
$ catkin_make // このコマンドは、必ず~/catkin_makeディレクトリで行うこと
	もしいちいちディレクトリを移動するのが面倒なら、bashで行ける
	$ ( cd ~/catkin?ws/ && catkin_make -j 4)
	* -j 4 はCPUを4つ使うというパラメータ(ラズパイにそんなパワーはないので避けること)
```
* ロスサーバーを立ち上げ、ブラウザでラズパイのIPアドレスにアクセスする [ 以下、実際には実行していない]
```bash
$ pwd
> /home/user/catkin_ws/src
$ roscore & // バックグラウンドでロスサーバーを立てることができる
$ ls /dev/video*
> /dev/video0
$ rosrun cv_camera cv_camera_node // 画面は黒いままになるはず

$ source ~/.bashrc
$ ssh pi@192.168.0.0
$ source ~/.bashrc
$ rosrun web_video_server web_video_server
> [ INFO] [1605770949.484487424]: Waiting For connections on 0.0.0.0:8080 // これは、ポート番号8080でカメラ画像が配信しているという意味
```
## ROSノード

* ROS上で動くプログラムのことを「ノード」と呼ぶ
	* Unixでいうところの「プロセス」
	* さっきのやつにおける、cv_camera_node, web_video_server
* ノードは連携して動く
	* cv_camera_node: カメラ画像をROS、OpenCV用の形式に変換
	* web_video_server: 変換されたデータをもらってウェブ配信
```bash
$ rosnode list //　実行中のノードリスト
> /cv_camera
> /rosout
> /web_video_server
```
## ROSトピックとメッセージ

```bash
$ rostopic list
> /cv_camera/camera_info
> /cv_camera/image_raw
> /rosout
> /rosout_agg
```
* データをやり取りする口（トピック）が表示される
* やり取りされるデータのことをメッセージという

## ノードとトピックの関係　
* 各ノードがトピックを通じてメッセージを融通することで全体として仕事を行う
	* ノードは、いくつかの「パブリッシャ」と「サブスクライバ」を持つ
		*　データを出す側がパブリッシャ
		*　データを受ける側がサブスクライバ
	*　この構造があるおかげで、ノードの柔軟な組み換えが可能になる
		*　インターフェイスが同じならプログラムを取り替えられる
		*　例: mjpeg_server（古い） => web_video_server（新しい）
	*　いくつかのノードがゆるく連携して動くので、立ち上げる順番だったりもない

## ROSパッケージの構成
* package.xml: パッケージマニフェスト => 他パッケージの依存関係、連絡先、ライセンス等々が書かれてある
* CMakeLists/txt: CMakeのスクリプト
* コード
	*　src: ソースコード（.cppファイル）
	*　include: ヘッダファイル（.hファイル） 	

## パッケージを作ってみる

```bash
$ cd ~/catkin_ws/src
$ catkin_create_pkg mypkg rospy
$ ls 
> CMakeLists.txt  async_web_server_cpp  mypkg  web_video_server
$ cd mypkg/
$ ls
> MakeLists.txt  package.xml  src
$ vim package.xml // LICENSEとmaintainerくらいを編集すればよい
```
* gitの使い方応用
```bash
$ sudo apt install hub 
$ git init
$ git add -A
$ git commit -m "Initial commit"
$ hub create // ユーザー名とパスを入れれば、もう出来上がり
```

* 
```bash
$ mkdir scripts // pythonやったらscriptsやんね
$ cd scripts
$ pwd
> /home/user/catkin_ws/src/mypkg/scripts
$ vim count.py
```
* count.pyの中身
```bash
#!/usr/bin/env python3
imort rospy
from std_msgs.msg import Int32

rospy.init_node('count')
pub = rospy.Publisher('count_up', Int32, queue_size=1)
rate = rospy.Rate(10)
n = 0
while not rospy.is_shutdown():
    n += 1
    pub.publish(n)
    rate.sleep()
```

* 権限を付与
``` bash
$ ls -l count.py
> -rw-rw-r-- 1 mirano-pat mirano-pat 253  9月  3 02:51 count.py
$ chmod +x count.py
$ ls -l count.py
> -rwxrwxr-x 1 mirano-pat mirano-pat 253  9月  3 02:51 count.py
```

