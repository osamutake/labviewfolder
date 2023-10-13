Lib/String/JSON
==

JSON 関連の一連のライブラリ

これらのライブラリでは JSON の要素を表すのに型(Enum)と値(Variant)を対にした構造体を使っている。

値 は Variant であるが、その実態は以下のようになっている

- プリミティブデータ : そのまま値が格納される
- 配列 : 上記の「型(Enum)と値(Variant)を対にした構造体」の配列
- オブジェクト : キーを表す文字列と、「型(Enum)と値(Variant)を対にした構造体」を対にした構造体の配列

提供されるVI
--

- JSONObjectToMap.vi
  - JSON オブジェクトの値が含まれる Variant を与えるとキー文字列から JSON 値へのマップに直して返す
  - type を与えると object であるかどうかをチェックして、異なればエラーを吐く
  - 繋がなければチェックしないが、Variant を配列に直すところで結局エラーが出るはず
- ParseJSON.vi
  - JSON 文字列と offset を指定すると、その位置から JSON 要素を１つ読みだして上記構造体として返す
  - offset はその分だけ進む
  - 文字列に残りがあれば Remaining が true になる
- ParseJSONObjectKey.vi
  - ParseJSON.vi の中でオブジェクトのキー文字列をパースするのに使われる
- ParseJSONString.vi
  - JSON の文字列要素を LabVIEW 文字データに直して返す
  - 値を囲む `"` を取り外したり `\` によるエスケープを元に戻したりしている
- StringToJSON.vi
  - 逆に LabVIEW 文字データを JSON 文字列表現に直す
  - `"` で値を囲み `\` によるエスケープを施す

 