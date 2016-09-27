##### 08_virtualPad

### その１ いちご画像と爆弾画像をランダムに落す
https://github.com/hosokawa9360/08_virtualPad/issues/1  
エンドレスランナーゲームのロジックとほぼ同じです。
コードをしっかり読んでください。

### その２　ゴーストボタンでカート操作する

 - 画面に右矢印、左矢印ボタンを配置する
 - 画面の右側をタッチしたら、カートが右に移動する（右矢印ボタンは半透明になる）  
 - 画面の左側をタッチしたら、カートが左に移動する（左矢印ボタンは半透明になる）  

### その３　バーチャルアナログパッド（タッチ＆ドラック）でカートを操作する
（resouce.jsに追加）
 touchorigin_png: "res/touchorigin.png",
 touchend_png: "res/touchend.png"

 //バーチャルアナログパッド用のタッチリスナーの実装
 ```
 var touchListener = cc.EventListener.create({
   event: cc.EventListener.TOUCH_ONE_BY_ONE,
   swallowTouches: true,
   onTouchBegan: function(touch, event) {
     //タッチ開始位置にスプライトを表示させる
     touchOrigin = cc.Sprite.create(res.touchorigin_png);
     topLayer.addChild(touchOrigin, 0);
     touchOrigin.setPosition(touch.getLocation().x, touch.getLocation().y);
 　　//タッチ位置にドラック用スプライトを表示させる
     touchEnd = cc.Sprite.create(res.touchend_png);
     topLayer.addChild(touchEnd, 0);
     touchEnd.setPosition(touch.getLocation().x, touch.getLocation().y);
     //タッチしているぞflagをON
     touching = true;
     return true;
   },
   onTouchMoved: function(touch, event) {
     //移動中の指の位置にドラック用スプライトを表示させる
     touchEnd.setPosition(touch.getLocation().x, touchEnd.getPosition().y);
   },
   onTouchEnded: function(touch, event) {
     //タッチ終了のときはスプライトを消す　タッチflagをOFF
     touching = false;
     topLayer.removeChild(touchOrigin);
     topLayer.removeChild(touchEnd);
   }
 })
 ```

 //カートの移動のため　Update関数を1/60秒ごと実行させる関数
  ```
 update: function(dt) {
   if (touching) {
   //touchEnd(ドラックしている位置）とタッチ開始位置の差を計算する
   //そのままだと値が大きすぎるので50で割る
   xSpeed = (touchEnd.getPosition().x - touchOrigin.getPosition().x) / 50;
     if (xSpeed > 0) {
       cart.setFlippedX(true);
     }
     if (xSpeed < 0) {
       cart.setFlippedX(false);
     }
     cart.setPosition(cart.getPosition().x + xSpeed, cart.getPosition().y);
   }
 }
 ```

 ### その４　指の動きだけでカートを操作する  
 移動方向が限られたゲームでは、ボタンやパッドで視覚的に表現しなくても直観的な操作ができます。 画面をタッチして指を左右に動かすとカートが動くようにコーディングしましょう。

 ##### グローバル変数
 ```
 var detectedX;　 //現在タッチしているX座標
 var savedX;　 //前回タッチしていたX座標
 var touching = false;　 //タッチ状況管理用flag
 ```
 ##### touchListener関数の処理
 //fingerOperation用のタッチリスナーの実装
 ```
 var touchListener = cc.EventListener.create({
   event: cc.EventListener.TOUCH_ONE_BY_ONE,
   swallowTouches: true,
   onTouchBegan: function(touch, event) {
     touching = true;
     //現在タッチ中のX座標を保持する変数へ代入
     detectedX = touch.getLocation().x;
     //前回タッチしていたX座標として代入
     savedX = detectedX;
     return true;
   },
   onTouchMoved: function(touch, event) {
     //現在タッチ中のX座標を保持する変数へ代入
     detectedX = touch.getLocation().x;
   },
   onTouchEnded: function(touch, event) {
     //タッチflagをOFF
     touching = false;
   }
 })
 ```
 `touching`変数に`true`,または`false`を設定する
 `onTouchBegan`イベントで  
 `detectedX`変数, `savedX`変数の初期化
 `onTouchMoved`イベントで  
 `detectedX`変数を更新  

 ### その５　課題
 落ちてくるリンゴを猫がキャッチするゲームを作成さいしなさい。  
 ＜仕様＞
制限時間は６０秒　画面左上に表示  
取ったリンゴは画面右下に補油時  
ゲームシーンの初期化がおこなわれてから１秒後にカウントダウン開始  
リンゴと虫は画面上部に生成され、下方向に落下する  
虫が猫に衝突すると、猫がびっくりして、２秒間震える（その間はリンゴをキャッチできない） 猫の画像もびっくり画像に変える  
虫がかごに入るとキャッチ済みのリンゴの数を１０個へらす（リンゴの数がマイナスにならないようにする）
猫の震えは0.1秒に左右＋－５移動を２０回行う  
その間（２秒間）は、猫の移動はできない  
地面に落下したリンゴやむしは１秒後消去される  
リンゴがかごに入った個数により、かごの画像を変更する  
０から２０個、２０個～３９個、４０個～７９個、８０個以上でそれぞれ画像を変える  
ゲームが終了するとタイトル画面にもどる  
タイトル画面にはヘルプ（ゲームの概要）を表示させることができるボタンを設置する  
タイトル画面にはハイスコアを表示させることができる  
ハイスコアをﾌｧｲﾙもしくはクッキーに記録し、次回起動時に読み出しできるようにする  
