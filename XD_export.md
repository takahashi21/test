# XDデザインカンプからのエクスポート

事前準備：XDのプラグイン「Web Export」を導入しておく

## ページをブロックに分割する

- 以下は１ページをヘッダー、フッター、サブカラム、メインカラムに分割する場合
- ヘッダー、フッター、サブカラムを一度作成したら、以降はメインカラム部分のみ出力する

<details>
  <summary>ページを分割する[1/3]：ガイドの作成</summary>

- ヘッダー、フッター、メイン部分（メインカラム、サブカラム）のブロックごとにページを分割する
- 下図のように、ブロックの境界となるガイドを作成する

![](/image_01.JPG)

- Ctrl+; または 表示 > ガイド > ガイドをすべて表示 でガイドを表示する
- 元からガイドが引いてある場合もある。足りない場合は作成する

![ガイドの引きかた](https://nomad-cafe-20.com/wp-content/uploads/2021/04/guide-line-Xd.gif)

</details>

<details>
  <summary>ページを分割する[2/3]：アートボードの作成</summary>

- ガイドを参考に、分割したブロックごとのアートボードを作成する

![](/image_05.JPG)

- 作成したアートボードを選択して、右側のタブ「変形」でX方向に移動する

![](/image_06.png)

</details>

<details>
  <summary>ページを分割する[3/3]：オブジェクトの移動</summary>

- 画面左側で選択ツールをクリックする

![](/image_07.png)
- 各ブロックのオブジェクトをドラッグで範囲選択する

![](/image_08.JPG)
- 作成したアートボードを動かした値と同じ値だけ、右側のタブ「変形」で移動させる

![](/image_09.png)

</details>

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
<div id="block-wrapper"> <!-- 書き出し後に追加したdiv !-->
  <div id="block" style="trasnform-origin: 0 0;"> <!-- 書き出し直後いちばん外側にあるブロック !-->
  ...
  </div>
</div>
```

- CSSで外側に追加したdivにposition:relativeを指定

```css
#block-wrapper {
  position: relative;
}
/* 自動出力された記述はそのままでよい */
#block {
  position: absolute;
  ...
```

- position: absolute; の要素は幅・高さ = 0 として扱われてしまう
- JavaScriptでブロックの幅・高さを設定する
```js
// デザインデータの幅 = 1366pxより画面幅が小さいときブロックを縮小し
// ブロック親要素に高さを設定する
const block = document.getElementById("block");
const wrapper = document.getElementById("block-wrapper");

$(window).on("load resize", function () {
  const windowWidth = document.body.clientWidth;
  const scale = windowWidth < 1366 ? windowWidth / 1366 : 1;
  
  block.style.transform = `scale(${scale})`;
  $(wrapper).css("height", block.clientHeight * scale);
});
```
