# NoteODrive.md

宮前 俊治 

九州大学鳥人間チーム

---

## 今日の内容

ODriveを使ってBLDCを遠隔で操作する

## 環境

* Ubuntuのバージョン
$ cat /etc/issue
> Ubuntu 20.04 LTS \n \l









## やったこと

---

### ubuntuをインストール

* USBからUbuntu 20.04 LTSをインストール
* アップグレード
* $ sudo apt update
* $ sudo apt upgrade
* $ LANG=C xdg-user-dirs-gtk-update
	* ホームディレクトリの中身を英語にする
* $ sudo apt full-upgrade
* $ sudo apt autoremove
* $ sudo apt install tree // tree コマンドのインストール
* $ sudo apt install git // gitのインストール 
* $ pip -V
* $ sudo apt install python3-pip
* $ pip3 -V
	* pip 20.0.2 from /user/lib/python3/dist-packages/pip ( python 3.6 )	
* $ python3 -V
	* pip3 -Vpython 3.8.10

---

## ODrive DOCSを参照

* cd ~/. //　なんとなく, /home/user に移動
* sudo pip3 install --upgrade odrive
	> Successfully built odrive  
	> Installing collected packages: IntelHex, PyUSB, backcall, traitlets, matplotlib-inline, pygments, parso, jedi, > decorator, pickleshare, wcwidth, prompt-toolkit, ipython, numpy, cycler, pyparsing, kiwisolver, matplotlib, odrive  
	> Successfully installed IntelHex-2.3.0 PyUSB-1.2.1 backcall-0.2.0 cycler-0.10.0 decorator-5.0.9 ipython-7.27.0 jedi-0.18.0 kiwisolver-1.3.2 matplotlib-3.4.3 matplotlib-inline-0.1.2 numpy-1.21.2 odrive-0.5.2.post0 parso-0.8.2 pickleshare-0.7.5 prompt-toolkit-3.0.20 pygments-2.10.0 pyparsing-2.4.7 traitlets-5.1.0 wcwidth-0.2.5  
* odrivetool // どうして...どうして成功するん.....ubuntu 16であんなにpipがバグ起こしてたのに......
