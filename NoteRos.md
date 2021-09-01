# ROSのお勉強
宮前 俊治

九州大学鳥人間チーム


---

## 今日の内容

* 動機付け（なんでこの講義があるのか）

---

## 今日の内容

* ODriveの基本的な使い方
* RosとODrive

## ロボットシステム

* とはなんだろう？
* この講義で言うところのロボットシステム
   * 目的を遂行するために動く
   * 高度に自動化されている
       * 例1: サッカーロボット、つくばチャレンジのロボット、ルンバ
       * <span style="color:red">例2: 新幹線、ゆりかもめ、自動ドア、エレベータ</span><br />　
* 少し拡大解釈すると、ロボット（的なもの）<br />は十分世の中に普及している
   * 趣味のものではないので、どう効率よく開発するかを大真面目に考えないといけない

---

## ODrive

* 仕様
  * 12V to 24V 

* プロトコル
  * Many types of command modes
    – Goto (position control with trajectory planning)
    – Position commands
    – Velocity command
    – Torque command
    
* ハードウェア要件
  * 最大2つのブラシレスモーター
  * 最大2つのエンコーダー
  * 電力抵抗(パワーレジスタ)は50W or over
 
## 公式

* ホームページ
  https://odriverobotics.com/

## やったこと

* ツールのダウンロードとインストール
  * ガイドのほどんどの手順が，odrivetoolと呼ばれるユーティリティを参照しているため，最初にそれをインストールする
    * デスクトップ上で，Ubuntuエミュレータを使用して行った．
    * 最初からpython3はインストールされている
    * python3-pipをインストール
      * sudo apt install python3-pip
    * odrivetoolをインストール
      * $ echo 'SUBSYSTEM=="usb", ATTR{idVendor}=="1209", ATTR{idProduct}=="0d[0-9][0-9]", MODE="0666"' | sudo tee /etc/udev/rules.d/91-odrive.rules
      * $ sudo udevadm control --reload-rules
      * $ sudo udevadm trigger

* odrivetoolを始める
  * $ odivetool 
  * 詳しく知りたいヒトは，https://docs.odriverobotics.com/odrivetool.html を参照すること
    * ここで仮に $ odrv0.vbus_voltage と入力すると， ボードの主電源電圧を調べることができます。次のように表示される
> ODrive control utility v0.5.1  
> Please connect your ODrive.  
> Type help() for help.  
>  
> Connected to ODrive 306A396A3235 as odrv0  
> In [1]: odrv0.vbus_voltage  
> Out[1]: 11.97055721282959  

* M0を構成する
  * 要注意！破壊の可能性あり！

  * 制限の設定
    * 電流制御
      * $ odrv0.axis0.motor.config.current_lim      [A]
      * デフォルトで電流は10Aに設定されている（弱いね！） 
      * ODriveを調整したら，60Aまで増やして，パフォーマンスを向上させられるよ
      * 60Aを超えると，電流アンプのゲインを変更する必要があることに注意
    * 速度制限
      * $ odrv0.axis0.controller.config.vel_limit      [turn/s]
      * 速度もだいぶ控えめ
  * 他のハードウェアパラメータの設定
    * ブレーキ抵抗を仕様する場合
      * $ odrv0.config.enable_brake_resistor を $ True に設定．
      * そのあと，ODriveのconfigurationを保存し，ODriveをrebootする
      * $ odrv0.config.brake_resistance [Ohm] は，ブレーキ抵抗の抵抗値．
      * なにか，ブレーキ中に問題が発生した場合は，0.05 Ohm くらい増やすと良いらしい
      * odrv0.config.dc_max_negative_current [A] は電源に逆流できる電流の量．
      * 慣例的に，負．デフォルトでは10mAと，控えめ．
      * $ odrv0.axis0.motor.config.pole_pairs は，ローターの磁極数を2で割った値になる．
      * $ odrv0.axis0.motor.config.torque_constant は，モーターに供給される電流の1Aあたりに，モータに生成されるトルクのratio．
      * これは．8.27 / (motor kV) に設定する必要がある．
      * アンペア単位でトルクを指令する場合は、トルク定数を1に設定すればいいらしい．
      * $ odrv0.axis0.motor.config.motor_type は，使用されているモーターのタイプ
      * 現在，High-current motors (MOTOR_TYPE_HIGH_CURRENT)とgimbal motors (MOTOR_TYPE_GIMBAL)がサポートされている
        * If you’re using a regular hobby brushless motor like this one, you should set motor_mode to MOTOR_TYPE_HIGH_CURRENT. For low-current gimbal motors like this one, you should choose MOTOR_TYPE_GIMBAL. Do not use MOTOR_TYPE_GIMBAL on a motor that is not a gimbal motor, as it may overheat the motor or the ODrive.    Further detail: If 100’s of mA of current noise is “small” for you, you can choose MOTOR_TYPE_HIGH_CURRENT. If 100’s of mA of current noise is “large” for you, and you do not intend to spin the motor very fast (Ω * L « R), and the motor is fairly large resistance (1 ohm or larger), you can chose MOTOR_TYPE_GIMBAL. If 100’s of mA current noise is “large” for you, and you intend to spin the motor fast, then you need to replace the shunt resistors on the ODrive.  
        * odrv0.axis0.encoder.config.cpr はエンコーダ1回転あたりのカウント数です．
        * これは，一回転あたりのパルス(PPR)値の4倍らしい．
  * 構成を保存するには
    * $ odrv0.save_configuration() と入力する



---
<iframe width="560" height="315" src="https://www.youtube.com/embed/7xXnXHc0roA" frameborder="0" allow="autoplay; encrypted-media" allowfullscreen></iframe>


$\Longrightarrow$組み合わせることを実現する<br />コンピュータや社会の仕組みを学習

---

## 手っ取り早さの実現


* <span style="color:red">プラットフォーム</span>の存在

## <span style="text-transform:none">Raspberry Pi</span>
<img width="35%" src="md/images/raspi4.jpeg" />

---

## <span style="text-transform:none">Raspberry Pi</span>について詳しく

* イギリスで開発された教育用のシングルボードコンピュータ<br />$ $
* 開発の動機・歴史

---
---

## 講義で使う道具
---

## ウェブサイト等

* [講義のサイト](https://lab.ueda.tech/?page=robosys_2020)<br />$ $
* 連絡: 他人に見られてもいい内容はTwitterで[@ryuichiueda](https://twitter.com/ryuichiueda)あるいは[@uedalaboratory](https://twitter.com/uedalaboratory)
    * 質問も重要なコントリビューション<br />$ $
* [この講義資料](https://github.com/ryuichiueda/robosys2020)にプルリクくれて私がマージしたら加点

