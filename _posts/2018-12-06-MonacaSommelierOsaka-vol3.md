---
layout: post
title: "Androidでピンチインピンチアウト出来なくてピンチになった話"
date: 2018-12-11
excerpt: "AndroidでWebview表示を拡大縮小したい！！"
post: true
tags: [ブログ]
---

# Monacaソムリエ挨拶
皆さんこんにちは、この度初めてアドベントカレンダー担当する事になりました。

何を記事にしようか悩みましたが、最近悩んで解決したピンチインピンチアウトが

出来なくて焦ったが、解決した話をしたいと思います。

---
# ピンチインピンチアウトで画面を拡大縮小したい
ピンチインピンチアウトで画面を拡大縮小表示したいとの要望があったので、

index.htmlのMetaタグをいじくったら簡単だろうって思ったんですけどね。

とりあえずmetaタグを入れてみました。

~~~html
<meta name="viewport" content="width=device-width, initial-scale=1, maximum-scale=3, user-scalable=yes"/>
~~~

具体的に変えたのは
~~~html
maximum-scale=1 →　maximum-scale=3

user-scalable=no　 →　user-scalable=yes
~~~
ビルドして実行！！

**iOSピンチインピンチアウトで画面を拡大縮小が出来ました。**

はい、出来た！！（メチャ簡単）

さぁ、Androidで確認して終わりっと思ったら。。。

**うっ、出来ない。ビクともしない。。。**

JavaScriptで動的に変えてみたけど、、、、ダメだった。。。

さてどうしたものか？どうやらOSレベルで制御が入っているようなので、Webviewあたりを

調べないといけないなぁ~っと思いそのあたりを調査。

# AndroidはWebviewそのものを設定を変更する必要あり。

Monacaか？Cordovaが何か悪さしいるのか？そもそもOSレベルでダメなのか？

Androidで普通にWebサイトはピンチインピンチアウトで拡大縮小出来るし、理論的には出来るはず。

っと思い、AndroidのWebviewに絞って調査すると、どうやらAndroidではWebviewはデフォルトではスケーリングOFFになっている模様。

これはネイティブコードいじくらないといけないなぁ～っと思い。

プラグイン作らなぁしゃーないと思っていたのですが。。。

**わざわざ作るのは面倒くさい。**

っと思い、それっぽいプラグインが無いか調査。。。。あった。

# AndroidのWebviewの設定をいじくれるプラグイン発見！
下記が見つかりました。

[cordova-plugin-viewport](https://github.com/cakuki/cordova-plugin-viewport)


そもそもこのプラグインがやってること。

***
~~~java
//コンテンツを画面幅に合わせてズームアウトします
webView.getSettings().setLoadWithOverviewMode(true);
//Tureでページにビューポートメタタグが含まれている場合、タグで指定された幅の値が使用されます。
webView.getSettings().setUseWideViewPort(true);
~~~
***

なるほど、、、って当たり前のことしかしてないので

いいや、とりあえずここに設定を追加していったらええやん。って思い。下記コードを

xwalk.java、default.javaに追加

***
~~~java
//スクロールバーの領域の隙間を埋める
webView.setScrollBarStyle(WebView.SCROLLBARS_INSIDE_OVERLAY);

//ズームを有効にする。
webView.getSettings().setBuiltInZoomControls(true);

//ズームコントロールを非表示にする。
webView.getSettings().setDisplayZoomControls(false);
~~~
※なぜgetSettings().setSupportZoom(true);が不要なのかは不明。。。
***

**結果は・・・・うまく行きました！！！ピンチ脱出！！**

皆さんメリークリスマス！！

そしてよいお年を。