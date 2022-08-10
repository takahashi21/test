# XDデザインカンプからのエクスポート

事前準備：XDのプラグイン「Web Export」を導入しておく

## ページを分割する[1/3]：ガイドの作成
- ヘッダー、フッター、メイン部分（メインカラム、サブカラム）のブロックごとにページを分割する
- 下図のように、ブロックの境界となるガイドを作成する

![](/image_01.JPG)

- Ctrl+; または 表示 > ガイド > ガイドをすべて表示 でガイドを表示する
- 元からガイドが引いてある場合もある。足りない場合は作成する

![ガイドの引きかた](https://nomad-cafe-20.com/wp-content/uploads/2021/04/guide-line-Xd.gif)
## ページを分割する[2/3]：アートボードの作成
- ガイドを参考に、分割したブロックごとのアートボードを作成する

![](/image_05.JPG)

- 作成したアートボードを選択して、右側のタブ「変形」でX方向に移動する

![](/image_06.png)
## ページを分割する[3/3]：オブジェクトの移動
- 画面左側で選択ツールをクリックする

![](/image_07.png)
- 各ブロックのオブジェクトをドラッグで範囲選択する

![](/image_08.JPG)
- 作成したアートボードを動かした値と同じ値だけ、右側のタブ「変形」で移動させる

![](/image_09.png)

## Web Exportで書き出し
- プラグイン > Web Export > 輸出Artboad
### エクスポート設定
- Stylesheet, Script > External stylesheet, scriptにチェック
- Export Folder > 出力するブロックごとにフォルダを分ける

![エクスポート設定](/image_03.png)

## 書き出したデータを編集する- idとクラス、画像ファイルにブロック名prefixを追加
- 書き出したHTMLのいちばん外側のdivにインラインで style="trasnform-origin: 0 0;" を設定
- その外側にdivを追加して、idを設定する

```html
<div id="block-wrapper"> <!-- 書き出し後追加したdiv !-->
  <div id="block" style="trasnform-origin: 0 0;"> <!-- 書き出し後いちばん外側にあるブロック !-->
  ...
  </div>
</div>
```

- CSSで外側に追加したdivにposition:relativeを指定

```css
#block-wrapper {
  position: relative;
}
```

- JavaScriptでブロックの幅・高さを設定します
- Customize.js で const blockList にブロック名を追記するか、

```js
const blockList = {
  ...
  ...
  block: {
    block: document.getElementById("block"),
    wrapper: document.getElementById("block-wrapper"),
  }
};
```
- Twig内にJavaScriptを記述します

```js
{% block javascript %}
<script type="text/javascript">
$(window).on("load resize", function () {
  const windowWidth = document.body.clientWidth;
  const scale = windowWidth < 1366 ? windowWidth / 1366 : 1;
  const block = document.getElementById("block");
  block.style.transform = `scale(${scale})`;
  $("#block-wrapper").css("height", block.clientHeight * scale);
});
</script>
{% endblock %}
```
