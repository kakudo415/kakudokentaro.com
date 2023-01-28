---
title: "AmpliTube 5の音がMOTU M2から出ないのを直す"
date: 2023-01-28T21:21:04+09:00
description: "AmpliTube 5使用時、オーディオインターフェースから音が出ない問題を直せたので備忘録として手法を書き留めます"
tags: []
draft: false
---

備忘録です。

ご多分に漏れず「[ぼっち・ざ・ろっく！](https://bocchi.rocks/)」のぼっちちゃんに影響されて全く弾けないくせにギターを引っ張り出してきました。

## 環境

- Windows 10 (Version 21H2, Build 19044.2486)
- AmpliTube 5 (Version 5.0.1)
- MOTU M2 (Firmware Version 1.02, Driver Version 4.0.8.8030)

## 症状

- AmpliTubeでは、Input, Output deviceともに正常に設定されているように見える
- AmpliTubeでは、INPUTもOUTPUTも正常に振れる
- MOTU M2では、OUTが振れず音も出ない

![設定画面](../option.png)

## 解決策

### 結論

出力デバイスが正常に設定されていなかった

### 手順

1. `%APPDATA%\IK Multimedia\AmpliTube 5\`にある`settings.properties`を開く。
2. `<DEVICESETUP>`の`audioOutputDeviceName`が空欄になっていたら、`audioInputDeviceName`を参考に埋める。

以下は例です。上記の通り8行目を編集しています。

```xml
<?xml version="1.0" encoding="UTF-8"?>

<PROPERTIES>
  <VALUE name="StandaloneScale">
    <GuiScale ScaleRatioWidth="1.0" ScaleRatioHeight="1.0"/>
  </VALUE>
  <VALUE name="audioDeviceState">
    <DEVICESETUP deviceType="ASIO" audioOutputDeviceName="MOTU M Series" audioInputDeviceName="MOTU M Series"
                 audioDeviceRate="192000.0" audioDeviceInChans="10"/>
  </VALUE>
  <VALUE name="inputChannel0" val="1"/>
  <VALUE name="inputChannel1" val="1"/>
  <VALUE name="outputChannel0" val="0"/>
  <VALUE name="outputChannel1" val="1"/>
  <VALUE name="midiDeviceState">
    <MIDISETUP deviceName=""/>
  </VALUE>
</PROPERTIES>
```

## 参考文献

- [Windowsにて、AmpliTube 5で音が出ないときの対処法](https://note.com/ikmultimedia_jp/n/n0b11b4fb4d08)
