# NoteODrive.md

宮前 俊治 

九州大学鳥人間チーム

---

## 今日の内容

ODriveを使ってBLDCを遠隔で操作する

## 環境

* Ubuntuのバージョン
```bash
$ cat /etc/issue
```
> Ubuntu 20.04 LTS \n \l









## やったこと

---

### ubuntuをインストール

* USBからUbuntu 20.04 LTSをインストール
* アップグレード

```bash

$ gnome-terminal --tab
$ sudo apt update
$ sudo apt upgrade
$ LANG=C xdg-user-dirs-gtk-update // ホームディレクトリの中身を英語にする
$ sudo apt full-upgrade
$ sudo apt autoremove
$ sudo apt install tree // tree コマンドのインストール
$ sudo apt install git // gitのインストール 
$ pip -V
$ sudo apt install python3-pip
$ pip3 -V
> pip 20.0.2 from /user/lib/python3/dist-packages/pip ( python 3.6 )	
$ python3 -V
> pip3 -Vpython 3.8.10
```
---

## ODrive DOCSを参照

```bash
$ cd ~/. //　なんとなく, /home/user に移動

$ sudo pip3 install --upgrade odrive
> Successfully built odrive  
> Installing collected packages: IntelHex, PyUSB, backcall, traitlets, matplotlib-inline, pygments, parso, jedi, > decorator, pickleshare, wcwidth, prompt-toolkit, ipython, numpy, cycler, pyparsing, kiwisolver, matplotlib, odrive  
> Successfully installed IntelHex-2.3.0 PyUSB-1.2.1 backcall-0.2.0 cycler-0.10.0 decorator-5.0.9 ipython-7.27.0 jedi-0.18.0 kiwisolver-1.3.2 matplotlib-3.4.3 matplotlib-inline-0.1.2 numpy-1.21.2 odrive-0.5.2.post0 parso-0.8.2 pickleshare-0.7.5 prompt-toolkit-3.0.20 pygments-2.10.0 pyparsing-2.4.7 traitlets-5.1.0 wcwidth-0.2.5  
$ odrivetool // どうして...どうして成功するん.....ubuntu 16であんなにpipがバグ起こしてたのに......
```

## raspi 4 の場合

* USBをちゃんと認識できるようにする
```bash
$ vim ~/.bashrc
> ...  
> export PATH=$PATH:~/.local/bin  
```

## odrivetoolにて


---
* I use encorder

 * delete config
```odrivetool
odrv0.erase_configuration()
```

```

odrv0.config.enable_brake_resistor = True # True False

odrv0.axis0.encoder.config.cpr = 8 # defo 8 ppr, max 2048 ppr
odrv0.axis0.encoder.config.mode = ENCODER_MODE_INCREMENTAL
odrv0.axis0.encoder.config.calib_scan_distance = 150  
odrv0.axis0.encoder.config.bandwidth =35 

odrv0.axis0.motor.config.pole_pairs = 4
odrv0.axis0.motor.config.motor_type = MOTOR_TYPE_HIGH_CURRENT
odrv0.axis0.motor.config.torque_constant = 8.27 / 4 #4000 == <measured KV>  
odrv0.axis0.motor.config.resistance_calib_max_voltage = 4  
odrv0.axis0.motor.config.requested_current_range = 25 #Requires config save and reboot  
odrv0.axis0.motor.config.current_lim = 60
odrv0.axis0.motor.config.current_control_bandwidth = 100  

odrv0.axis0.controller.config.pos_gain = 1  
odrv0.axis0.controller.config.vel_gain = 0.02 * odrv0.axis0.motor.config.torque_constant * odrv0.axis0.encoder.config.cpr  
odrv0.axis0.controller.config.vel_integrator_gain = 0.1 *  odrv0.axis0.motor.config.torque_constant * odrv0.axis0.encoder.config.cpr  
odrv0.axis0.controller.config.vel_limit = 10  




odrv0.save_configuration()


odrv0.axis0.requested_state = AXIS_STATE_FULL_CALIBRATION_SEQUENCE

odrv0.axis0.controller.config.control_mode = CONTROL_MODE_VELOCITY_CONTROL
odrv0.axis0.controller.config.input_mode = INPUT_MODE_POS_FILTER

odrv0.axis0.requested_state = AXIS_STATE_CLOSED_LOOP_CONTROL
odrv0.axis0.controller.input_pos = 1
```
 * benri command
```odrivetool
odrv0.axis0.encoder.shadow_count
dump_errors(odrv0)
odrv0.reboot()

```




  * debugging
```odrivetool
dump_errors(odrv0)
odrv0.clear_errors()
---

```odrivetool

odrv0.axis0.motor.config.pole_pairs = 4
odrv0.axis0.motor.config.motor_type = MOTOR_TYPE_HIGH_CURRENT

odrv0.axis0.encoder.config.cpr = 4096 / 2
```
  * save
```odrivetool
odrv0.save_configuration()
```

  * caliburation
```odrivetool
odrv0.axis0.requested_state = AXIS_STATE_ENCODER_OFFSET_CALIBRATION
odrv0.axis0.requested_state = AXIS_STATE_FULL_CALIBRATION_SEQUENCE
```

 * wakaran
```odrivetool
odrv0.axis0.requested_state = AXIS_STATE_FULL_CALIBRATION_SEQUENCE 
dump_errors(odrv0)


odrv0.axis0.requested_state = AXIS_STATE_MOTOR_CALIBRATION  
odrv0.axis0.motor  
odrv0.axis0.motor.config.pre_calibrated = True

odrv0.axis0.requested_state = AXIS_STATE_ENCODER_HALL_POLARITY_CALIBRATION
odrv0.axis0.encoder
odrv0.axis0.requested_state = AXIS_STATE_ENCODER_OFFSET_CALIBRATION
odrv0.axis0.encoder
odrv0.axis0.encoder.config.pre_calibrated = True
```

* config
```bash
odrv0.axis0.motor.config.current_lim = 10  
odrv0.axis0.controller.config.vel_limit  // [turn/s]
odrv0.axis0.motor.config.calibration_current // [A]  
odrv0.config.brake_resistance // [Ohm] 
odrv0.config.dc_max_negative_current // [Amps]
odrv0.axis0.motor.config.torque_constant // This should be et to 8.27/(motor KV)  
odrv0.axis0.motor.config.motor_type = MOTOR_TYPE_HIGH_CURRENT  
```

* I use hall sensor feedback
```bash
odrv0.axis0.motor.config.resistance_calib_max_voltage = 4  
odrv0.axis0.motor.config.requested_current_range = 25 #Requires config save and reboot  
odrv0.axis0.motor.config.current_control_bandwidth = 100  
odrv0.axis0.motor.config.torque_constant = 8.27 / 0.024 #24 == <measured KV>  


odrv0.config.enable_brake_resistor = True

odrv0.axis0.encoder.config.mode = ENCODER_MODE_HALL  
odrv0.axis0.motor.config.pole_pairs = 4 # This is the number of magnet pokes in the rotor, divided by two.  
odrv0.axis0.encoder.config.cpr = 24 #the num of pole pairs * the num of states of the hall feedback  
odrv0.axis0.encoder.config.calib_scan_distance = 150  
odrv0.config.gpio9_mode = GPIO_MODE_DIGITAL  
odrv0.config.gpio10_mode = GPIO_MODE_DIGITAL  
odrv0.config.gpio11_mode = GPIO_MODE_DIGITAL  

odrv0.axis0.encoder.config.bandwidth =35 
odrv0.axis0.controller.config.pos_gain = 1  
odrv0.axis0.controller.config.vel_gain = 0.02 * odrv0.axis0.motor.config.torque_constant * odrv0.axis0.encoder.config.cpr    
odrv0.axis0.controller.config.vel_integrator_gain = 0.1 * odrv0.axis0.motor.config.torque_constant * odrv0.axis0.encoder.config.cpr  
odrv0.axis0.controller.config.vel_limit = 10  
odrv0.axis0.controller.config.control_mode = CONTROL_MODE_VELOCITY_CONTROL  

odrv0.save_configuration()    
odrv0.reboot()  

odrv0.axis0.requested_state = AXIS_STATE_FULL_CALIBRATION_SEQUENCE 
dump_errors(odrv0)


odrv0.axis0.requested_state = AXIS_STATE_MOTOR_CALIBRATION  
odrv0.axis0.motor  
odrv0.axis0.motor.config.pre_calibrated = True

odrv0.axis0.requested_state = AXIS_STATE_ENCODER_HALL_POLARITY_CALIBRATION
odrv0.axis0.encoder
odrv0.axis0.requested_state = AXIS_STATE_ENCODER_OFFSET_CALIBRATION
odrv0.axis0.encoder
odrv0.axis0.encoder.config.pre_calibrated = True

odrv0.save_configuration()
odrv0.reboot()

odrv0.axis0.requested_state = AXIS_STATE_CLOSED_LOOP_CONTROL
odrv0.axis0.controller.input_vel = 2
# Your motor should spin here
odrv0.axis0.controller.input_vel = 0
odrv0.axis0.requested_state = AXIS_STATE_IDLE
```
odrv0.save_configuration()    
