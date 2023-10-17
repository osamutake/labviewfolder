Lib/String
==
文字列を扱うユーティリティ VI 群

- [JSON/](JSON/)
  - JSON 関連の一連のライブラリが格納されている
- AssertSubTexts.vi
  - 文字列の指定位置に候補文字列のうちどれかが現れることを確認する
  - いずれも現れなければエラーを吐く
  - 候補のうちどれが現れたかも読み取れる
  - JSON ライブラリの中で使っている
- StringArrayIntersection.vi
  - ２つの文字列配列に共通して現れる文字列だけを含めた配列を返す
  - 要素が多い場合には StringArrayToSet.vi で Set にしてから Intersection し、もう一度配列にする方が速そう
- StringArrayToSet.vi
  - 文字列配列を文字列Setに変換するだけ
- StringToComplexNumber.vi
  - `"1.23+13i"` のような文字列を複素数値(CEXT)に変換する
  - 恐らく標準では提供されていない？
