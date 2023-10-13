utilities/
==

ここには単独で利用可能なVIアプリが含まれます。

- [utilities/](#utilities)
  - [ParameterSweeper.vi](#parametersweepervi)
  - [SettingManager.vi](#settingmanagervi)
  - [WebAPI/](#webapi)

ParameterSweeper.vi
--

![](image4md/panel-ParameterSweeper.png)

- `vi` と `ctrl` で指定した数値コントロールの値を `start` から `stop` まで `step` ずつ `interval (s)` 間隔で掃引する
- `Sweep Start` を押すと掃引が始まり、もう一度押すまで繰り返し掃引する
- `round trip` はクリックでトグルする `on` だと掃引が往復になる
- `Second Dimension` が `on` だと、上の行で指定された掃引が終わるたびに下の行で指定された値が `step 2` ずつ変更され、二次元的な掃引が行われるようになる
- `Swap` を押すと1行目と2行目の値が入れ替わる
- `Period (s)` に一周期あたりにかかる時間が表示される
- `Record Sync` を `on` にしておくと、`Sweep Start` を押すと同時に [`ADCDataRecorder.vi`](../hardware/Readme.md#adcdatarecordervi--adc-の電圧値を記録するアプリ) の `Measure` ボタンが押される
- `Interval (s)` をあまり小さくすると処理が間に合わなかったり設定タイミングの誤差が問題になったりするけれど、時定数が `100 ms` 以上のロックインアンプを使った測定を念頭に置くと、この VI と [`ADCDataRecorder.vi`](../hardware/Readme.md#adcdatarecordervi--adc-の電圧値を記録するアプリ) を使うだけでどんなパラメータでも掃引測定が行えてとても便利
- `Interval (s)` は 0.5 秒くらいで使うことを想定している

SettingManager.vi
--

この VI を使うと複数の VI にわたる多数のコントロール値を「設定」として保存しておき、後からダブルクリックで簡単に復帰できる。必要な VI を開いたり、VI 位置まで保存・復帰できるので、複数の VI を強調させて行うような測定において開始時の手間を大幅に減らせる。

![](image4md/panel-SettingManager.png)

- VI を立ち上げると左側に VI とそのコントロールの一覧が、現在の値と共に表示される
- `Reload Control Tree` を押すとこの表示を更新できる
- VI やコントロールの行をクリックあるいは Ctrl+クリック すると選択・選択解除を行える
- 保存しておきたいコントロールをすべて選択して `Save` ボタンを押すと右側に新しい設定項目が追加される
  - コントロールではなく VI を選択した場合には VI の表示位置が保存される
- 設定項目のタイトルは右下の欄で変更可能
- 詳細説明も追加しておける
- 設定リストの `N` の欄は保存項目数を表す
- 設定リストの項目をダブルクリックするか、選択しておいて `Restore` を押すと、保存されていたコントロール値、VI 位置を復帰できる
  - VI がロードされていなければ自動的にロードするかどうかを聞いてくる
- 設定項目を選択すると、保存されているコントロール値が左のコントロール一覧に表示されるので、慣れるまではこれを確認してから `Restore` した方が間違いない
- 設定項目を選択しておいて `Delete` を押すと消せる キーボードの `Delete` ボタンでも消える


WebAPI/
--

LabVIEW 上に http サーバーを建てて、http 経由でコントロールの値を読み書きできるようにするためのプロジェクト。

これを使うと LabVIEW 以外のプログラムから VI 上のコントロールにアクセスできるようになる。

詳細は [WebAPI/Readme.md](WebAPI/Readme.md) を参照のこと
