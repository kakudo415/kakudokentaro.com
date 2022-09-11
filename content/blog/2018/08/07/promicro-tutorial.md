---
title: "初マイコンにPro Microを選んでしまったので、使い方メモ"
date: 2018-08-07T00:00:00+09:00
tags: ["Former Blog Post"]
draft: false
---

<p class="warn">
  <strong>この記事は以前のブログから引っ越してきたものです！</strong><br>
  <a href="https://github.com/kakudo415/blog">以前のブログのGitHubリポジトリ</a>
</p>

こんにちは Kakudo です
今回は、タイトルの通り初めての電子工作（自作キーボード）のためにPro Microを買ったけど使い方全く分からなかったので、同じように爆死した同志達のためにメモを残したいと思います。  
## 他の有名マイコンとの違い
この記事を見ている人はたいてい「Arduino Leonardo」とか「Arduino Micro」と同じことができる安いボードとして「Pro Micro」を買っていると思うのですが、Leonardoとかを前提にした解説サイトが多いのでどう読みかえればいいのかを書いていきます  
[SparkFunの公式ガイド](https://learn.sparkfun.com/tutorials/pro-micro--fio-v3-hookup-guide/hardware-overview-pro-micro)、読める人はこれ読んだほうが絶対正確ですｗ  
### そもそもArduino IDEにPro Microないんやけど！
残念ながらPro MicroはArduino公式マイコンではないので公式サポートがありません  
しかし、Pro Micro製造元のSparkFunが配布しているアドオンを追加することで普通に使えるようになります  
`https://raw.githubusercontent.com/sparkfun/Arduino_Boards/master/IDE_Board_Manager/package_sparkfun_index.json`
このURLをArduino IDEの環境設定内の追加のボードマネージャのURLのところに追記します  
### どこが5Vピンなんだよ！
Leonardoとかには常に5Vを出力してくれる5Vピンがあるらしい、だけどPro Microには無い・・・  
自分が初めにぶつかったのがこの問題、結論から言うと公式に書いてありましたｗ  
結論から言うとRAWかRSTかVCCを使うと良さげです。  
テスターを使って図ってみたところRAW-GND間、RST-GND間、VCC-GND間のどちらにも4.7V程度の電気が流れていました（GNDはマイナスとして働くとのことです）  
## やってみる
### とりあえずLチカやろうず
電子工作の世界ではLEDをチカチカさせるのがプログラムでいう Hello World 的な立ち位置らしい  
だけど、外部にわざわざLEDと導線用意するのはめんどくさいのでProMicroに乗っているLEDを使ってやってみる  

<pre><code>void setup() {}              // Pro Microの起動時に一回だけ呼ばれる関数、今回は使わない

void loop() {                // その名の通り起動中はこの関数が繰り返し呼ばれる
	digitalWrite(17, HIGH);  // 17番ピンに電気を流す（今回は何も繋いでいないが内部でオンボードLEDと繋がっている）
	delay(1000);             // 1000ミリ秒 = 1秒待つ
	digitalWrite(17, LOW);   // 17番ピンの電気を止める（LEDが消える）
	delay(1000);             // また1秒待つ
}</code></pre>

このプログラムをArduino IDEを使って書き込んでみるとゆっくりLEDが点滅させることができる  
### 電気が流れてるか判定しよう
キーボード作るときに必要になる電気が流れているかの判定（スイッチがオンかどうか）  
さっき何気なく使ったdigitalWriteやdelay関数、またHIGHなどの定数値はどこかで宣言されているみたいなので気にせず使っていいです  
**下のプログラムは実行しないで！！！！！**

<pre><code>void setup() {
	pinMode(7, INPUT);              // pinMode関数を使って7番ピンを入力として使うことを宣言
	digitalWrite(9, HIGH);          // 9番ピンに電気を流す ここからスイッチ等を通して7番ピンに繋ぐ
}

void loop() {
	if (digitalRead(7) == HIGH) {   // digitalRead関数を使って7番ピンに電気が通っているかを判定
		digitalWrite(17, HIGH);     // LED点灯
	} else {
		digitalWrite(17, LOW);      // LED消灯
	}
}</code></pre>

これで7番と9番の間につなげたスイッチがオンの時のみ、LEDが点灯する予定だったのですが  
なぜかスイッチオン時に消灯する・・・  
しかもなんか一瞬煙も出たので一時中断・・・（焦った）  
ということで続きは次回に・・・  
