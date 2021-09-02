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
```bash
$ mkdir -p catkin_ws/src // オプション -p は、多階層のディレクトリを一回で作る
$ cd !$ // 直前に作ったディレクトリパスが!$に格納されている
$ catkin_init_workspace
> Creating symlink "/home/user/catkin_ws/src/CMakeLists.txt" pointing to "/opt/ros/noetic/share/catkin/cmake/toplevel.cmake"
$ ls
> CMakeLists.txt
```
