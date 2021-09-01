# ROSのお勉強
宮前 俊治

九州大学鳥人間チーム


---

## 今日の内容

* ODriveの基本的な使い方
* RosとODrive

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
    * デスクトップ上で，Ubuntuエミュレータを使用して行った．=> WSL2は，USBデバイスを認識しないため以下の操作は無為．別のPCか，素直にWindowsマシーンとして使うべき．
    * 最初からpython3はインストールされている
    * python3-pipをインストール
      * sudo apt install python3-pip
    * odrivetoolをインストール
      * $ sudo pip3 install --upgrade odrive
    * ここで何故かUSBを認識しないことが発覚 => WSL2の仕様で有ることが判明．
    * 再度laptopで同じことを行う．
    * $ sudo pip3 install --upgrade odrive
     * Errorが再び．
> You are ysubg ouo version 8.1.1, however version 21.2.4 is available.
> You should consider upgrading via the'pip install --upgrade pip'

    * 指示通りにコマンドを流すも，同じことを返される．「〇〇が必要だ」「わかった〇〇が必要なんだな.じゃあ✕✕を用意しろ」「✕✕が必要だ」「わかった✕✕が必要なんだな．じゃあ〇〇を用意しろ」
    * 解決済み => $ sudo python3 -m pip install --upgrade pip
    
    * 結局このやり方はだめだった．
    * https://github.com/odriverobotics/ODrive にアクセス．ダウンロード．
    * $ odrivetool
    * なんでこれでできるんやぁ......?
    

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
* M0の位置制御を行う
  * $ odrv0.axis0.requested_state = AXIS_STATE_FULL_CALIBRATION_SEQUENCE
    * M1なら，substitute $ axs1 wherever it says axis0
    * このコマンドは，最初にモーターの電気的特性を測定し，次にモーターの電気的位相とエンコーダー位置の間のオフセットを測定する
  * $ odrv0.axis0.requested_state = AXIS_STATE_CLOSED_LOOP_CONTROL
    * このコマンドは，モーターがその位置を保持するけど，めっちゃ弱々しい． 
  * $ odrv0.axis0.motor.config.current_lim
    * このコマンドも，モーターがその位置を保持して，めっちゃ津用意．
  * odrv0.axis0.controller.input_pos = 1
    * このコマンドは新しい位置設定値を送信する．
* ほかの制御モード
  * デフォルトの制御モードは 完全なエンコーダー基準のフレームで，フィルタリングされていない位置制御です．
  * 代わりに，軌道制御をおすすめする（controlled trajectory）
  * もし連続回転を大きな数を使わずに行いたいのなら，サークルフレーム内での制御を用いることで可能です．
    * Filtered position control
    * Trajectory control
      * https://docs.odriverobotics.com/control-modes.html#trajectory-control
    * Circular position control
    * Velocity control
    * Ramped velocity control
    * Torque control

