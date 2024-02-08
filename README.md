# preact-vs-svelte

## 目的
今回、弊社としてはVueでの開発がメインとなってきておりますが、Nuxt3移行の時に下記部分を感じ頂いたかと思います。
- Reactよりの設計思想になってきている
- フレームワークの不安定さ
- Compostion APIになってからの可読性の悪さ

このことから現状でも弊社にはVueが適切なのか？他に適切なライブラリがあるか？を調査しました。

今回調査したのは下記です。
- Preact
　-　Reactから派生したライブラリでReactに比べ軽量のところがポイント
- Svlete
　-　前からあるライブラリではあるが、昨今本などもでてまた注目を浴びている

## preactとsvleteについて

下記が過去3ヶ月のトレンド結果です。（わかりやすいようにVueとReactも含めております。）
![image](https://github.com/tmo-taka/preact-vs-svelte/assets/45462276/d0a6100c-0ba8-412b-a4e7-5c8dbef61190)

### 比較表
|                  | Preact              | Svlete          | 
| ---------------- | ------------------- | --------------- | 
| バンドルサイズ   |                     |                 | 
| レンダリング方法 | 仮想DOM             | 実DOM           | 
| SSR実現性        | 可（Preact + Vike） | 可（SvelteKit） | 
| storeによる実装  | 可能                | 可能            | 
 > [!CAUTION]
 >TODO バンドルサイズあったら埋める

### Preactについて
まず今回Reactではなく、Preactを押しているのかというというと下記理由です。

#### メリット
 - :+1: Reactに比べ軽量という点 
   - Preact:9.5kB React124.1kB(本体：6.4kB + ract-dom:114.6kB)
 - :+1: Reactではネストされたコンポーネントに対して描画効率を考えmemo化等複雑化してしまう部分で少し楽になる点
   - Preactから発信された`Signals`というものでpropsされた値を使っていないところには勝手に伝達されない
  
**まとめるとReactに比べ、軽量で、少しマイルドに書けます。**
デメリットもあります。

#### デメリット
 - :-1: Reactと互換性があるとは言われているが、完全にそうではない点
   - Reactで使用可能なライブラリがPreactで使用できるかと言われるとそうでもない、省略されている可能性もあり。(Vikeもそうでした。)
   - 実際に試してみる必要あり。
 - :-1: SSRでの場合、実装に工夫が必要となる点
   -　[preact-render-to-string](https://github.com/preactjs/preact-render-to-string) か [Vike](https://vike.dev/)を使用する必要あり。
   - そのため学習コスト及び調査が複雑。

### Sveleteについて
こちらは実DOMで最近勢いが増してきているものです。

#### メリット
- :+1: SSR、SSGがデフォルトで用意されているフレームワーク「Svelte.kit」が用意されている点　　
  - ドキュメントも充実している、割とVikeに近いフォルダ、ファイル構成。（そのため今後発展可能性もありそう。）
- :+1: アクセシビリティの向上を図るようなコーディングができる点
  - form要素にactionというものをつけてsubmitをした際にAPIアクセスできるようにしたり、submit時にページ遷移をrejectする設定で可能。
- :+1: サーバーサイド実行、クライアント実行がファイルによって分かれおり、どこで実行されいてるかが一目瞭然な点（下記例）
```
test　（testというpathにアクセスした場合に下記ファイルをそれぞれ実行）
├── +page.server.ts 　　#　サーバ側でのみ実行　
├── +page.ts         # レンダリング前に実行　（サーバー/クライアントどちらでも実行されうる）
└── +page.svelte     #　　上記それぞれのデータを受け取りレンダリングするファイル
```

#### デメリット
- :-1: ファイル名「page」が膨大な数爆誕する点
  - Reactで使用可能なライブラリがPreactで使用できるかと言われるとそうでもない、省略されている可能性もあり。(Vikeもそうでした。)
- :-1: 書き方がVueやReactと大きく異なる点
  - 　例：exportが値を受け取る際に使う記述、storeはsubscribteという記述になり、computedの値は「$」で定義するなど。
- :-1: データのやり取りの幅が広く追うのが大変な点
  - 　例:layoutも子階層におけば上書きできる等。

### 実験
 Vue Preact Svelteそれぞれのコードについて弊社の環境を模して実験的にコーディングしてみました。
 ビルドツール: Vite

#### 実験1
初期描画速度計測

##### 方法
以下構成の読み込み速度(DOMContentLoaded)を開発者ツールにて5回計測し、それの平均値を求め比較。（その際cacheは毎度削除。）

##### 構成
 5階層までネストしたコンポーネント + 10個のコンポーネント　呼び出ししたAppファイル　　
 ![スクリーンショット 2024-02-07 17 52 40](https://github.com/tmo-taka/preact-vs-svelte/assets/45462276/06fcc05d-8374-4040-a792-245b3cfd595f)

##### 計測結果
|        | 初回読み込み速度の平均値(ms) | 
| ------ | ---------------------------- | 
| Vue    | 431                          | 
| Svelte | 427.7                        | 
| Preact | 425                          | 

> [!IMPORTANT]
> というように弊社で使用するレベルでしたらあまり大きな差異はなさそうです。

#### 実験2
 > [!CAUTION]
 >TODO

### まとめ

#### 個人的な結論
 > [!CAUTION]
 >TODO

#### 判断材料
|                                                                                   | Vue(Compostion API) | Preact                                              | Svelte               | 
| --------------------------------------------------------------------------------- | ------------------- | --------------------------------------------------- | -------------------- | 
| フレームワークのissueの数（単体）<br>※openのissue数/総issue数                    | 356/10012           | 150/1746                                            | 953/5890             | 
| フレームワークのissueの数（SSRフレームワークの場合）<br>※openのissue数/総issue数 | 900/13886(Nuxt)     | 193/2493(Preact + Vike)                             | 673/5048(Svelte.kit) | 
| 学習コスト                                                                        | 小                  | 小～中<br>(Reactを触ったことある方にとってはほぼ小) | 大                   | 
| 相性の良い開発規模                                                                | 小～大              | 小～中                                              | 小～大               | 
| 可読性                                                                            | ✕                  | 〇                                                  | ◎                   | 
 > [!CAUTION]
 >TODO 加筆



 




  
  
  




