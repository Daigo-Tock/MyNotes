# ESP32の使い方

宮前 俊治

2021年9月8日

Kyushu Univ. 

---

## 今日の内容

* ESP32-DevKitC-32E, ubuntu 20.04LTS, ros noetic

---


## ESP32の関数

* DAC出力 dacWrite
* アナログ入力 analogRead
* PWM出力 ledcWrite
* シグマデルタ出力 sigmaDeltaWrite
* ホールセンサ hallRead

* [めちゃくちゃ参考になるサイト](https://ekai.theblog.me/posts/2065288)
---
## ADC アナログ入力

* 注意 最大値が3.3V
* 読み取り値は0~4096
* SAR
  * SAR ADC1には、GPIO32 ~ 39に相当するピン、ホールセンサが接続されている
  * SAR ADC2には、それ以外のピン、deep sleep機能用の入力が接続されている
* 減衰率の設定
  * ADCの入力には減衰器を設定できる
    * ADCモジュールは0~1Vの間でAD変換を行っている
    * このモジュールに例えば11db(約1/3.6倍)の減衰器を設定することで、0~3.6Vの間のAD変換ができるようになっている
    * デフォルトでは11dBの減衰が設定されている。
    * 12bitでの最大値は3.6V
  * analogSetAttenuation(ADC_0db) // 減衰率の設定
    *　詳しくは[url](https://github.com/espressif/arduino-esp32/blob/3cbc405edf2448cf1d77b0a30a5e62ddab806a85/cores/esp32/esp32-hal-adc.h#L86)を参照

| 定数 | GPIOピン |  
| :---: | :---: |  
| A0 | 36 |  
| A0 | 36 |  
| A3 | 39 |  
| A4 | 32 |  
| A5 | 33 |  
| A6 | 34 |  
| A7 | 35 |  
| A10 | 4 |  
| A11 | 0 |  
| A12 | 2 |  
| A13	| 15 |  
| A14	| 13 |  
| A15	| 12 |  
| A16	| 14 |  
| A17	| 27 |  
| A18	| 25 |  
| A19	| 26  |  

## DAC アナログ出力

* dacWrite(pin, value)
* analogWrite()ではないことに注意
* valueは0から255までの値

| 定数 | GPIOピン |  
| :---: | :---: |  
| DAC1 | 25 |  
| DAC2 | 26 | 

## PWMなど

* LEDC機能を利用する場合、analogWrite()と同様の振る舞いをする
  * LED調光を想定した一般的なPWMと言える。 // 詳しくはSigmaDellta変調について調べる
  * SigmaDelta変調に関して
    * 0~7の8ch、1220~312500Hzの範囲で動作可能
    * IO MUX経由で物理ピッチにアタッチして用いる
    * 特に推奨されるピンはなし
    * IO MUX内部で``` gpio_sdX_out```という呼称
  * LEDC機能
    * 用いるのは0~15の16ch
  * 余談
    * tone関数が用いることができず、代替機能として、```ledWriteTone(ch, 440)```を用いると、chにアタッチされたピンに440Hzで矩形波が出力される 


---

## ただのLチカ


* LEDは2番

```arduino 
int LED = 2;

void setup() {
  pinMode(LED, OUTPUT);
}

void loop() {
  digitalWrite(LED, HIGH);
  delay(1000);
  digitalWrite(LED, LOW);
  delay(1000);
}
```

--- 

## swhich付きLチカ

* 使うスイッチはBOOTと書かれているやつ
* 
```arduino
const int LED_pin = 2;
const int sw_pin = 0;

void setup() {
  pinMode(LED_pin, OUTPUT);
  pinMode(sw_pin, INPUT_PULLUP);
}

void loop() {
  int sw_state = digitalRead(sw_pin);
  if(sw_state==HIGH){
    digitalWrite(LED_pin, LOW);
  }
  else{
    digitalWrite(LED_pin, HIGH);
  }
}
```

---

## 可変抵抗付きLチカ

```arduino
const int led_pin = 25; 
const int in_pin = 4;
int value = 0;

void setup() {
  pinMode(led_pin, OUTPUT);
  pinMode(in_pin, INPUT);
  Serial.begin(9600);
}
 
void loop() {
  value = analogRead(in_pin);
  value = value*255 / 4095;
  dacWrite(led_pin,value);

  Serial.println(value);
  delay(1000); 
}
---
