---
title: "結婚式の余興向けで、Flutter Webを使ったあみだくじアプリを短期間で作った話"
emoji: "💒"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["flutter", "dart", "flutterweb", "結婚式"]
published: true
---


昨年、籍を入れ1月の3連休（先週）に友人だけの披露宴を開催しました
その余興として、Flutter Webを使って簡単なあみだくじアプリケーションを作った話になります

製作期間は、お正月から流行りのAIツール（GPTやClaude）を活用しながら進め、約1週間でした（結婚式の前日まで作業していました）

# 経緯

妻「なんか余興やりたい その余興で新婚旅行のお土産を渡したい クイズとかは？」

僕「うーーん」

プランナーさん「(なんか色々と提案してくれたけど忘れた)こういうのどうですか？」

妻「どうしよ、まあ何をやるかはあんまこだわりなくて、尺作りと新婚旅行のお土産を景品として友達に渡したいな」

僕「あれ？ 会場のモニターにあみだくじのサイト映してやればいいんじゃね？」
「なんか、世の中に公開されている無料のWebアプリとかでないかな？」

・・・・

僕「んー、入力項目多いし、披露宴当日に生きているかわからんな・・・」
「よし、自分で作ろう！！！」

Flutterエンジニアなら、Flutter Webで実装すればいけるのでは？と思ったのがきっかけでした

# 作ったもの

GitHub Pages + Flutter WebであみだくじのWebアプリを作成しました

アプリはこちらからアクセスできます
https://kentomiya89.github.io/amidakuji_app/

GitHub Pagesを選んだ理由は、以下の2点です

* 手軽にホスティングができること
* GitHub Actionsを利用して簡単にデプロイできること（参考記事：[こちら](https://zenn.dev/nekomimi_daimao/articles/26fd2e3b763191)）

また、GitHubで障害が発生した場合でも、最悪の場合はlocalhost上でアプリを動作させて代替できるという気軽さも理由の一つです

### あみだくじの仕様について

1. CSVのアップロード

* 姓と名だけのカラムを持つCSVファイルをアップロードする
* アップロードが成功すると、名前と景品入りのあみだくじが描画される
    * 景品の位置はランダムで決定
    * あみだの横線は各列7本ずつ引く

2. 当選者発表の流れ

* 「結果を発表」ボタンを押すと、アニメーション付きで線を引き、当選者を発表
    * 当選者はそれぞれ2名ずつ、新郎タブと新婦タブの2つある
    * 合計で4名が当選する

3. 景品の設定

* 新郎の景品：ビール
* 新婦の景品：チョコレート

といったシンプルな仕様にしてます

### データ管理の選択理由

当初、FirebaseやSupabaseなどのmBaaSを利用して参加者データを管理しようと考えましたが、以下の理由でCSVの運用を取ってます

* インシデントリスクを軽減し、手軽に管理できるため
* 式場側が提供している結婚式の管理画面から、参加者一覧のCSVをダウンロードできたため、管理が容易だったこと

### 画面イメージ

実際の画面としてはこちらになります↓

**CSVアップロードページ**

![](/images/8166fdce36cdbc/csv_body.png)

**あみだくじページ**

![](/images/8166fdce36cdbc/amida_body.png)

**当選者発表**

![](/images/8166fdce36cdbc/amida_body_with_win_line.png)


# 実装の進め方や技術的な話

## 過程

実装部分は、時間があまり取れず、特にあみだくじの部分を一から考えるのは大変だったため、GPTやClaudeに助けてもらいました

```
適当にフリー素材のあみだくじ画像を渡す
↓
「この画像を元にあみだくじのコードをFlutterで用意してください！」と指示
↓
もらったコードをビルド
↓
描画がうまくいってなかったら、失敗した画像を渡しより具体的な指示を出す
↓
...

```

を繰り返しました

ある程度できたコードを元に、自分の考えた仕様に合わせて調整を加えました

## 細かい技術面

あみだくじのUIはCustomPainterで描画してます

* TextPainter: 参加者のテキスト
* Canvas drawLine, drawImage : あみだの線や景品画像

パッケージ利用は

* [csv](https://pub.dev/packages/csv): csvデータを扱いやすくしたもの
* [file_picker](https://pub.dev/packages/file_picker): csvアップロード時のファイル取得
* [flutter_gen_runner](https://pub.dev/packages/flutter_gen_runner): assetsのリソースパス生成

を主に使っています

## あみだくじのUIで盛り上がりを意識した工夫

あみだくじのUIを実装する際、「どうすれば盛り上がるか？」を考える場面がいくつかありました

### 横線の配置

1つ目は、縦線と縦線の間に引く横線のY座標が、隣の横線と重複しないようにする点です

基本的に、あみだくじでは横線のY座標が隣と被っているイメージはなく、ゲーム性を考えるとバラツキがあった方が「誰に当たるか分からない」状況を作り出せます
盛り上がるように、重複しないロジックを工夫しました

![](/images/8166fdce36cdbc/no_duplication.png)

基本的にあみだくじは横線のY座標が隣りと被っているイメージはないですし、ゲーム性を考えるとバラツキがある方が誰に当たるか分からず盛り上がると思います

### アニメーションの方向

2つ目は、当選者から景品まで線を引くアニメーションにするか、景品から当選者まで線を引くアニメーションにするかという点です

実装序盤で悩みましたが、妻に相談したら

「当選者から線を引くと、すぐ結果が分かっちゃうじゃん！ギリギリまで分からない方が楽しいよ！」

と言われ確かにその通りだと思い、景品から線を引く方法を採用しました
後述で動画も貼りますが、いい感じに盛り上がりました

# 打ち合わせや当日

開発観点とは別に、式場側との成果物の共有や設備の事前確認、余興の段取りなどについても認識合わせを行いました

式場側にイメージを掴んでもらいやすくするため、プロトタイプとしてサイトや挙動の動画を撮影し、メールでやり取りを進めました

**送ったメール文**

![](/images/8166fdce36cdbc/mail_screen_shot_1.png)

**届いたメール文**

![](/images/8166fdce36cdbc/mail_screen_shot_2.png)

当日は、介添人の方にあみだくじの挙動を見せながら、余興の段取りを説明しました
また、最終的には司会者の方も盛り上げてくださり、会場全体が楽しめる雰囲気になりました

↓ 新婦側の友人が撮影してくれた動画

https://youtu.be/UGyVGQhXEA0

いい感じに盛り上がってよかったです！！

# まとめ

最初は、あみだくじのUI実装が難しそうで、「当日までに間に合うのか？実装できてもうまく動くのか？」と少し不安でした
しかし問題なく完成し、しかも盛り上がったのでよかったです

また、CustomPainterを使ってさまざまな描画ができることを学べたのも収穫で、技術的な知見を得られた点も非常に良かったです

リポジトリはこちら↓

https://github.com/kentomiya89/amidakuji_app
