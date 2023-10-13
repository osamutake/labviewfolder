Lib/
==

汎用的に使いまわせそうな VI ライブラリー

- [Lib/](#lib)
  - [サブフォルダ](#サブフォルダ)
  - [RaiseErrorIf.vi = true が入力されたときのみエラーを発生する](#raiseerrorifvi--true-が入力されたときのみエラーを発生する)

サブフォルダ
--

- [`File/`](File/Readme.md) ファイル関連
- [`SetGetControlValue/`](SetGetControlValue/Readme.md) コントロール値の読み書きを行う
- [`String/`](String/Readme.md) 文字列関連、JSON 関連
- [`UI/`](UI/Readme.md) UI関連
- [`Variant/`](Variant/Readme.md) `Variant` 関連


RaiseErrorIf.vi = true が入力されたときのみエラーを発生する
--

![](image4md/pins-RaiseErrorIf.png)

- `error?` 入力が `true` のときに `error our` 端子にエラーを出力する
- エラー発生部をケースストラクチャで囲わなくて済むようになるのでとても便利
- ダイアログは表示しない
- ソース文字列を `error message` 端子から指定可能
  - 内部で呼び出しスタック情報を自動的に追加する
- デフォルトのエラーコードは `5888`

典型的な使い方：

![](image4md/example-RaiseErrorIf.png)
