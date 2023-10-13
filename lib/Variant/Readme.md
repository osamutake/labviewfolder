Lib/Variant
==
Variant 関連のライブラリ

提供されるVI
--
### CoerseNumericType.vi

数値を含む Variant の型を読み取って、与えられた数値をそれと同じ型に変換した上で Variant として出力する。

複素数値にも対応している。

Variant を使っていると細かい数値型を気にする必要のないことがほとんどだが、配列やクラスターの一部を書き換えたりする時に必要になる。

### VariantArrayAccess.vi

Variant に格納された配列の要素を読み書きするための VI。

読むだけなら Variant の配列に変換すれば簡単に行えるのだけれど、次元数で分岐しなければならないのがちょっと面倒なのと、書き込みではこの配列の一部を書き換えて Variant に戻すとバイナリ互換が失われてしまう問題が発生する。

![](image4md/StandardVariantArrayReading.png)

この VI を使えば多次元配列に簡単にアクセスでき、またバイナリ互換を保ったまま要素を書き換えることができる（「バイナリ互換性」は配列自体を別の配列に入れたり、構造体に入れたりする時に重要になる）。

この VI は1次元を扱う VariantArrayAccess1D.vi と多次元を扱う VariantArrayAccessMulti.vi とからなる Polymorphic な VI になっている。

`index` あるいは `indices` を指定する端子に数値が入るか数値の配列が入るかで機能が変化する。

多次元の時はインデックスに配列を指定する。

値を読む際には `Variant` 型の `value` 端子を読むだけで良い

指定インデックスの値を書き換えるにはアクロバティックな操作が必要。

![](image4md/example-VariantArrayAccess.png)

標準的な方法は提供されていないので、配列をバイナリに直し、指定指標位置のデータを入れ替える必要がある。`VariantArrayAccess.vi` はこのための機能として、元配列のバイナリデータのうち指定位置の前と後の部分を `prepend` と `rest` に出力する。

新しく入れたい値を `Variant To Flattened String` でバイナリに直してこれらで挟み、元の配列の型情報を与えて `Flattened String To Variant` で `Variant` に戻せばバイナリ互換の配列Variantが得られる。

このとき、`New Value` として与える `Variant` 値の「中身」は元の配列の要素と正確に同じ型を持つ必要がある。そうでないとバイナリ互換が失われ、再構成された配列を正しく読み取れなくなる。[`CoerseNumericType.vi`](#coersenumerictypevi) は数値の型を元の値と合わせる機能を持つためこの手の作業に役立つ。

非常にアクロバティックでなおかつ非効率なのだけれど、たぶんこれしかバイナリ互換を保ったまま配列要素を書き換える方法は無いのだと思う。

### VariantClusterAccess.vi, VariantClusterAccessMulti.vi

`VariantArrayAccess.vi` のクラスター版。

Variant として与えられたクラスターの特定要素のみを読み書きする。`Multi` 付きの方は一度に複数の要素へアクセス可能。

`VariantArrayAccess.vi` とは異なり Polymorphic というわけではない。

使い方は以下の通り：

- `value in` に `Variant` 化されたクラスター値を入れる
- `indices` または `labels` にアクセスしたい要素番号あるいは要素名を指定する
  - 両方指定すると `indices` が使われる
- `values` に読み取られた値が出る
- 値を書き換えたければ、それぞれの値の前に `prepends` を付け、最後に `rest` を付けて、`type string` を使って `Variant` に戻す

![](image4md/example-VariantClusterAccessMulti.png)

### VariantClusterToArrayOfValues.vi

`Variant` 化されたクラスター値からサブ要素名の配列とその値の配列とを得る。
