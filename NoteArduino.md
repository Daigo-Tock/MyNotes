# NoteArduino

## AUTHOR

* 宮前　俊治
* 九州大学鳥人間チーム

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
