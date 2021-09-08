# ESP32の使い方

宮前 俊治

2021年9月8日

Kyushu Univ. 

---

## 今日の内容

* ESP32-DevKitC-32E, ubuntu 20.04LTS, ros noetic

---

## ADC アナログ入力

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
const int led_pin = 2; 
const int in_pin = 13
 
void setup() {
  pinMode(ledPin, OUTPUT);
  pinMode(swPin, INPUT_PULLUP);
}
 
void loop() {
 
  int sw_state = digitalRead(swPin);
 
  if (sw_state==HIGH){ 
    digitalWrite(ledPin, LOW); 
  }else{
    digitalWrite(ledPin, HIGH); 
  }
}

---
