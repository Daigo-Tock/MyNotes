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

