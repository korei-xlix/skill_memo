# Twitter小技集

<h1 id="aHowto">このドキュメントについて / About this document</h1>  
このドキュメントは、Twitter使用時に覚えた小技、謎仕様について掲載します。  
  





<h1 id="aMokuji">目次 / Table of contents</h1>  

* [リスト制限について）](#aListLimit)

* [古いツイートを追う）](#aOldTweet)

* [Twitter Blueの月支払い→年払いへの切替について）](#aTwitterBlue)

  





<h1 id="aListLimit">リスト制限について</h1>  
Twitterのリストの追加には、APIのリミット制限がある。  
Twitter仕様だと300/15分の記載があるが、実際には20/10分でリミット制限でリストに追加できなくなる。  
　　[レート制限](https://developer.twitter.com/ja/docs/twitter-api/rate-limits)  
  
この制限解除は1時間～最長翌日まで継続する。  
サポートに連絡しても解除手続きは実施してもらえず、返答が解除後にあるほど放置される。  
この動作が仕様なのか、不具合なのかは謎である。  
  





<h1 id="aOldTweet">古いツイートを追う</h1>  
Twitterでは、だいたい1週間前のツイートはweb表示できない。  
しかし、検索コマンドを使えば、過去のツイートでも指定期間のツイートをダウンロードできる。  
  
「from:[ユーザ名 screen_name]」でユーザが指定できる。  
「since:[日付] until:[日付]」で囲む。  
  

**使用例**  
以下のようにすると、2023年3月18～20の @korei_xlix のツイートをダウンロードできる。  

```text
from:korei_xlix since:2023-03-18 until:2023-03-20  

後ろにタグを付けると、タグ付きでも絞れる  
from:korei_xlix since:2023-03-18 until:2023-03-20 #korei_xlix  

```
  





<h1 id="aTwitterBlue">Twitter Blueの月支払い→年払いへの切替について</h1>  
Twitter Blueを月払いから、年払いに切り替えるには、一度月のサブスクリプションをキャンセルし、
サブスクリプションが終了した時点で年払いを登録すればいい。  
  
なお、月のサブスクリプションを終了しても、その月の有効日まではサブスクリプションは有効でTwitter Blueの機能や青マークは利用できる。  
サブスクリプションが終了すると、Twitter Blueの機能と青マークが無効になる。  
  
年払いのサブスクリプションを契約すると直後にTwitter Blueの機能が再度利用できるが、
青マークについては再度審査が必要で、審査が受かるまでは青マークがつかない。
その間に表示名、名前、アイコン、を変更すると再度審査が受かるまで時間がかかる。  
  





***
[[トップへ戻る]](/readme.md)  
  
::Admin= Korei (@korei-xlix)  
::github= [https://github.com/korei-xlix/](https://github.com/korei-xlix/)  
::Web= [https://website.koreis-labo.com/](https://website.koreis-labo.com/)  
::Twitter= [https://twitter.com/korei_xlix](https://twitter.com/korei_xlix)  
