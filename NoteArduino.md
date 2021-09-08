# NoteArduino

## AUTHOR

* 宮前　俊治
* 九州大学鳥人間チーム

## 今日やること

* Ubuntu20.04LTS、ROS noetic 環境で、Arduinoを用いて、Lチカができるようになる

## Arduinoのインストール

### Ubuntu 20.04 LTS の場合

``` bash
tar xvf ~/Downloads/arduino-1.8.16-linux64.tar.xz  
mkdir -p ~/.local
mv arduino-1.8.16 ~/.local/
cd ~/.local/arduino-1.8.16/
bash arduino-linux-setup.sh $USER
sudo systemctl reboot
```

```bash
cd ~/.local/arduino-1.8.16/
sudo bash install.sh
```

---

## Arduino IDE ROS Setup

### Introduction

* arduino ideでROSを直接扱いたい
  * [rosserial_arduino](http://wiki.ros.org/rosserial_arduino)を使う
 
### Installing the Softwar* ros

* Installing on the ROS workstation
```bash
sudo apt install ros-noetic-rosserial-arduino
sudo apt install ros-noetic-rosserial
```

*  Install ros_lib into the Arduino Environment

  * もし既にros_libがあれば、消去する
```bash
cd ~/Arduino/libraries
rm -rf ros_lib # もし既にros_libがあれば消去する。ファイルが見つからないというエラーが出ても問題ない。
rosrun rosserial_arduino make_libraries.py
```
### Finishing Up

* Arduino IDEを再起動
* {File} => {Example} => {ros_lib} があれば成功。

### 参考url

・[ROS Answers](https://answers.ros.org/question/353827/rosserial-and-arduino/)
・[ROS.org](http://wiki.ros.org/rosserial_arduino/Tutorials/Arduino%20IDE%20Setup)
---

## ESP32をArduino IDEでセットアップする

* 初心者やのに金欠でArduinoが買えずESP32をポチった人向け

* 全部[Github](https://github.com/espressif/arduino-esp32)に書いてある。必要最低限だけしか書かないので、Errorを起こしたら自力で解決して。

* {file} => {setting} => {adding bord manager url}に、``` https://raw.githubusercontent.com/espressif/arduino-esp32/gh-pages/package_esp32_dev_index.json ```を追加
* {tool} => {board} => {bord manager}を開いて「ESP32」を検索し、インストール

* 終わり
---

## ROSでLチカ

### USB接続の確認

* ターミナルでディレクトリを確認する
```bash
lsusb
cd /dev 
ls | grep ttyACM*
> 


・[Qitta-ROSのrosserialを使ってArduinoでLチカをする](https://qiita.com/nnn112358/items/059487952eb3f9a5489b)
---

