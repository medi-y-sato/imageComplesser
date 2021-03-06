# Dart2 で記事を書こうと思っていたら jpg 圧縮の比較 Web サイトが出来ていた

この記事は [mediba Advent Calendar 2018](https://adventar.org/calendars/3315) 二日目の記事です

```AA
　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　やつを追う前に言っておくッ！
　　　　　　　　　　　　　　　　　　　　　　おれは今やつのスタンドをほんのちょっぴりだが体験した
　　　　　　　　　　　　　　　　　　い…いや…体験したというよりはまったく理解を超えていたのだが……
　　　　　　　　 ,. -‐'''''""¨¨¨ヽ
　　　　 　 　 (.＿＿_,,,... -ｧァﾌ|　　　　　　　　 　あ…ありのまま 今　起こった事を話すぜ！
　 　 　 　 　 |i i|　 　 }!　}} /／|
　　　　 　 　 |l､{　 　j}　/,,ｨ//｜　　　　　　　『Dart2でCLIツールを作っていたと思ったら
　　　　　　　 i|:!ヾ､_ﾉ／ u {:}//ﾍ　　　　　　　　　　　　　　いつのまにかAngularDart5になっていた』
　　　　　　　 |ﾘ u' }　 ,ﾉ　_,!V,ﾊ |
　　 　 　 ／´fト､_{ﾙ{,ィ'ｅﾗ　, ﾀ人　　　　　　な…　何を言ってるのか　わからねーと思うが
　　　　 /' 　 ヾ|宀| {´,)⌒`/ |<ヽﾄiゝ　　　　　　 おれも何をされたのかわからなかった…
　　　　,ﾞ　 ／ )ヽ iLﾚ 　u' |　| ヾｌﾄﾊ〉
　　 　 |／_／　 ﾊ !ニ⊇　'／:} 　V:::::ヽ　　　　　　　　頭がどうにかなりそうだった…
　　　 /／ 二二二7'T'' ／u'　__ /:::::::/｀ヽ
　　　/'´r　-―一ｧ‐ﾞＴ´　'"´ ／::::／-‐ 　＼　　　　 催眠術だとか超スピードだとか
　　 / // 　 广¨´ 　/'　　 ／:::::／´￣｀ヽ ⌒ヽ　 そんなチャチなもんじゃあ　断じてねえ
　　ﾉ ' /　 ノ:::::`ー-､___／:::::／/ 　 　 　 ヽ}
_／｀丶　/::::::::::::::::::::::::::￣`ー-{:::...ｲ  もっと恐ろしいものの片鱗を味わったぜ…
```

出展 : https://dic.nicovideo.jp/a/%E3%81%82...%E3%81%82%E3%82%8A%E3%81%AE%E3%81%BE%E3%81%BE%20%E4%BB%8A%20%E8%B5%B7%E3%81%93%E3%81%A3%E3%81%9F%E4%BA%8B%E3%82%92%E8%A9%B1%E3%81%99%E3%81%9C%21

いいのかのっけからこんなふざけてて....

## 自己紹介

メリークリスマスまで 3 週間ちょいです、いかがお過ごしでしょうか

コミュニケーションデザイン本部 想像部 アプリ開発グループの 佐藤禎章と申します

本業としては、Android/iOS のネイティブアプリの構築をするメンバーのお手伝いをやっています

# 今回作ったもの

今回出来ていたものは、 _JPG 画像の圧縮率を比較できる SPA_ です
https://github.com/medi-y-sato/imageComplesser

[now](https://zeit.co/now) に publish しておきました
https://build-fbillkollb.now.sh/

## 中身の解説

画像を指定すると、JPG でクオリティ 10%刻み 10 段階の画像を生成し、リストします

![結果例](https://raw.githubusercontent.com/medi-y-sato/imageComplesser/master/%E7%B5%90%E6%9E%9C%E4%BE%8B.png "結果例")

### AngularDart のはじめかた

作るときのざっとした流れだけ、記載しておきます

- Dart for Web の[Get Started](https://webdev.dartlang.org/guides/get-started)の 2 と 3 を実施して、Dart の SDK と`webdev`、`stagehand`を導入しておきます
- プロジェクト用のディレクトリを作成後、そのディレクトリ上で`stagehand web-angular`とし、初期テンプレを展開してもらいます
- その後 `pub get`を実行すると、カレントにある`pubspec.yaml`を参照してパッケージを導入してくれます
- `webdev serve`とすると開発モードでローカルにサーバが立ち上がるので、出力にある URL(大概 http://localhost:8080 だと思います)を開いて確認してみてください
- `webdev build` とするとカレントの`./build/`ディレクトリにビルド結果が出力されるので、適当な所に設置してください

AngularDart で取り扱うコードは `./lib/` 以下にありますので、`./lib/src/app_component.dart`とかテンプレコンポーネントとかを眺めて構成を確認すれば、Angulra いじったことがある人なら「あーなるほど」と思うでしょう

### 今回やったこと

Dart っぽい事はあんまりやっていません

TypeScript 書いてた人なら Visual Studio Code のサジェストとカンで対応させられるレベルなので、Angular2 以降を触ったことがある人なら AngularDart はそう難しくないと思います

#### base64 を取り扱うための `crypto` 、画像処理を行うための `image`パッケージを`pub`から導入

```yaml
dependencies:
  crypto: any
  image: any
```

#### ファイルのアップロードみたいな UI を作成

ファイルの選択が完了したらすぐにコンバート処理を走らせたかったので、`(change)`でいきなりメソッド呼び出す行儀の悪い仕掛けにしてます

```html
<input
  style="display: none"
  type="file"
  accept="image/png"
  (change)="onFileChanged($event)"
  #fileInput
/>
<button (click)="fileInput.click()">Select File</button>
```

#### 画像は全部 Base64 でハメコミ

img の DOM にバイナリとして放り込むのが正しいのかもしれませんが、めんどくさかったので以前から知ってる方法で

```dart
target.src = 'data:image/jpeg;base64,${Encoded64}';
```

# なんでこんなものが出来たの？

- 別件でファイル加工処理のバッチを Perl で作っていた
- でも今どきバッチ処理程度で Perl 環境インストールしてもらうのも大変だよなあ、モジュールとか面倒だし、と Perl を思い直した
- そこで node/typescript で書いてみたが、割とファイルの取扱がめんどくさかった
- そんな折に Dart2 と Flutter のニュースを耳にした
- 調べてみたら [pub](https://pub.dartlang.org/)というパッケージ管理の仕組みが(-npm の再発明かと思ったけど-)ちゃんとしてて、Web サーバ側やローカルでの処理もちゃんと書けそうに見えた
- ので、ファイル加工のバッチを Dart2 で書いたら、すごいあっさりさっぱり書けてびっくりした

※ このバッチ自体はちょっと機密なものなので公開できないです、ごめんなさい

※ やってることはファイル読んで RegExp の match や replace ゴリゴリする、よくある奴です

で、このお手軽さを誰かに伝えたい、と思い、 *特定ディレクトリにある画像全部を PNG の圧縮レベル最大でコンバートし直したらどれだけ小さくなるかツール*を作ってみたのですね

_そしたら全然変わんなかったんです、みんなちゃんと最大レベルで圧縮してやんの_

なんか悔しくなったので、画像ごとに jpg の圧縮レベルを変えて画像を沢山生成してみたら、意外と面白い結果になったのですね

自然画だったらクオリティ 50%くらいに落としても結構違いが分からないんだなー、とか、その割に容量あんまり変わらないんだなー、とか

---

で、ファイル生成自体は簡単に出来るんですけど、並べて比べるのが面倒だったんですね

Finder とか Explorer でサムネイル表示にして見比べて、とかやる必要があったので

だったらこの比較をする Web ページを生成しちゃえばいいじゃん、と思いまして、作り始めました

それでページ生成方法を考えたのですが、真面目に HTML 吐き出すよりはフレームワーク使ったほうが楽だよなー、と思いましてちょっと調べたら、あるじゃないですか [AngularDart](https://webdev.dartlang.org/angular)ってのが

以前 [ionic を扱っていた](https://qiita.com/medi-y-sato/items/9d8f97cdbb663fc48e44)こともあって Angular は分かっているので、 ~~昔打った篠塚~~ 昔取った杵柄とばかりにさくっと表示させてみたら、さくっと出来ました

---

....で、ここまで来て気づいたんです

**Dart2 の CLI ツール作るんじゃなかったんかい**

すみません他にもいろいろ考えたんですけどどうしても面白い CLI ツールにならなかったのです、ごめんなさい

※ Dart CLI で Google Analytics のデータ引っ張り出して加工して Slack に投げる、とかやればよかったのかな....

# あとがき

Dart の強力なところは _サーバサイドもクライアントサイドも同じ気持ちで書ける_ という、JavaScript と node が持っていた特徴をそのまま引き継ぎ、 _モバイルアプリも同じ気持ちで書ける_ という特徴を Flutter で足してある所です

**CI ツールから Web アプリにさらっと転向して、出来ちゃう** という辺りから、そのお手軽さを感じていただけたとしたら、コレ書いた甲斐があったというものです

加えて言語仕様として型の取り扱いを頑張っているので、TypeScript で感じた安心感を、Dart でも感じています

はじめから async/await や stream などもありますし、大体のものはもう書けちゃうでしょう

Dart、結構未来、あるかもしれません

---

というわけで、mediba では言語やフレームワークにとらわれずに _何を作るか_ で考えられるエンジニアを募集しています

特に[ネイティブアプリのエンジニア](https://hrmos.co/pages/mediba/jobs/00000071)をですね....
