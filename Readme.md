個人的に使っている LabView 用のワーキングフォルダーです
==

公開可能なものをここに公開します。

内容一覧
--

- `applications/` この git に含めない個別のアプリケーションを置く
- [`hardware/`](hardware/) ハードウェア関連ライブラリ
  - `ADCDataRecorder.vi` : ADC 入力を一定サンプリングレートで記録、ディスクへ保存する
  - `DACSinglePoint.vi` : DAC 出力値を変更する
  - `SignalRecovery7280Lock-inAmp.vi` : ロックインアンプ 7280 の設定を行う
- [`lib/`](lib/) 汎用のサブ VI ライブラリ
  - [`lib/File/`](lib/File) ファイル関連
    - `FileMultipleBackup.vi` : 複数世代にわたるバックアップファイルを管理
    - `SequentialFileName.vi` : 連番付きファイル名の番号を1増やす
  - [`lib/Hardware/`](lib/Hardware) ハードウェア関
    - `TextControlledInstrum` : テキストコマンドで読取・設定可能なハードウェアを制御する
    - `SerialPortSetup.vi` : シリアルポートの設定を行う
  - [`lib/SetGetControlValue/`](lib/SetGetControlValue) コントロール値の読み書きを行う
    - `SetControlValue.vi` : 名前を指定してコントロールに値を書き込む
    - `GetControlValue.vi` : 名前を指定してコントロールの値を読み出す
    - `SetControlValues.vi` : リストで与えたコントローにの値を一度に書き込む
    - `GetControlValues.vi` : リストで与えたコントロールの値を一度に読み出す
    - `ControlValueToJSON.vi` : 任意の値を JSON 文字列にエンコードする
    - `VINameToVI.vi` : VI 名からリファレンスを得る (別アプリやリモートも対応)
  - [`lib/String/`](lib/String) 文字列関連
    - [`lib/String/JSON/`](lib/String/JSON) JSON 関連
      - `JSONObjectToMap.vi` : JSON オブジェクトをキー文字列から JSON 値へのマップに変換
      - `ParseJSON.vi` : JSON 文字列から要素を１つ読み出す
      - `ParseJSONString.vi` : JSON の文字列要素を LabVIEW 文字列に変換
      - `StringToJSON.vi` : LabVIEW 文字列をJSON の文字列要素に変換
    - `AssertSubTexts.vi` : 文字列の指定位置に候補文字列のうちどれかが現れることを確認
    - `StringArrayIntersection.vi` : ２つの文字列配列に共通して現れる文字列だけを含めた配列を返す
    - `StringArrayToSet.vi` : 文字列配列を文字列Setに変換
    - `StringToComplexNumber.vi` : `"1.23+13i"` のような文字列を複素数値(CEXT)に変換する
  - [`lib/UI/`](lib/UI) UI 関連
    - `DeferPanelUpdate.vi` : コントロールの値を変更する間 VI の表示更新を止める
    - `InsertMenubarItem.vi` : VI のメニューバーに新しい項目を追加する
  - [`lib/Variant/`](lib/Variant) `Variant` 関連
    - `CoerseNumericType.vi` : 数値を `Variant` で指定された型に変換する
    - `VariantArrayAccess.vi` : `Variant` に格納された配列の要素を読み書きする
    - `VariantClusterAccess.vi` : `Variant` に格納されたクラスタの要素を読み書きする
    - `VariantClusterAccessMulti.vi` : `Variant` に格納されたクラスタの複数の要素を一度に読み書きする
    - `VariantClusterToArrayOfValues.vi` : `Variant` に格納されたクラスターの要素名と値の配列を得る
  - `RaiseErrorIf.vi` : `true` が入力されるとエラーを発生する
- [`utilities/`](utilities/) 単独で利用するVIアプリ
  - `ParameterSweeper.vi` : 任意の VI の任意のコントロールの値をゆっくりと掃引する
  - `SettingManager.vi` : 複数の VI にわたる多数のコントロール値を「設定」として保存・復帰する

GitHub アドレス
--
https://github.com/osamutake/labviewfolder

このドキュメントは GitHub 上で参照すると見やすいです。

- [個人的に使っている LabView 用のワーキングフォルダーです](#個人的に使っている-labview-用のワーキングフォルダーです)
  - [内容一覧](#内容一覧)
  - [GitHub アドレス](#github-アドレス)
  - [ライセンス](#ライセンス)
  - [LabVIEW メタプログラミングの勧め](#labview-メタプログラミングの勧め)
  - [改訂履歴](#改訂履歴)

ライセンス
--
MIT ライセンスとしますので自由にご利用ください。


LabVIEW メタプログラミングの勧め
--
このライブラリには [`SetControlValue.vi`](lib/SetGetControlValue/#setcontrolvaluevi) / [`GetControlValue.vi`](lib/SetGetControlValue/#getcontrolvaluevi) という「異なるVI上のコントロールの値を読み書きするためのサブVI」が含まれています。

これを使って「LabVIEW のコントロールの値を制御するプログラム」を LabVIEW で作ることを、ここでは「LabVIEW メタプログラミング」と呼ぼうと思います。

なぜこれを勧めるのか？

複数の機器を組み合わせた測定を LabVIEW で制御しようとするとき、普通にやるとすべての機器の制御器や制御コードが１つの VI に乗るため、VI のパネルには非常にたくさんの制御器、表示器が並び、ブロックダイアグラムは画面からはみ出すくらいに大きくなってしまいます。

１つの装置を動かすのに１０個のパラメータを使うとして、３個の装置を動かすなら３０個。通常の LabVIEW プログラムだと３０本の信号線が複雑に入り組んだ VI が出来上がります。

コードを機能別に分離するには個々の装置の制御を別々の VI に分けるのがいいのですが、そうすると

- 初期化時にすべての VI の制御器に決まった順序で値を設定する
- 測定時に特定の順序で制御器の値を変更する
- データ保存時にすべての VI の制御器から値を読んで、測定データと一緒にファイルに保存する

などをどのように実現するか、迷ってしまうことが多いです。

こんなときに、

- VI 名とコントロール名と設定値を文字列で指定して値を書き込める [`SetControlValue.vi`](lib/SetGetControlValue/#setcontrolvaluevi)
- VI 名とコントロール名を文字列で指定して、その値を文字列として得られる [`GetControlValue.vi`](lib/SetGetControlValue/#getcontrolvaluevi)

があると、個々の装置の制御を行うだけの単機能の VI を組み合わせて複雑な測定を行うことが容易にできるようになります。

例として、、、

このライブラリに含まれる [`DACSinglePoint.vi`](hardware/#dacsinglepointvi--dac-の電圧値を制御するアプリ) は DAC の出力電圧を変更するための制御器が並ぶ、非常に単純な VI です。

![](hardware/image4md/panel-DACSinglePoint.png)

`Physical channels` に DAC チャンネルを指定して、対応する `Value` 欄を書き換えると電圧が出ます。

これだけだと何の面白みもないのですが、[`ParameterSweeper.vi`](utilities/#parametersweepervi) と組み合わせると、急に利用価値のあるものになってきます。

![](utilities/image4md/panel-ParameterSweeper.png)

このアプリは、`vi` と `ctrl` で指定する制御器の値を `start` から `stop` まで `step` 間隔で `Interval (s)` ごとに値を変更することを繰り返すプログラムです。例えば `DACSinglePoint.vi` が出力する電圧を 0V から 5V まで 0.5 秒おきに 0.1 V ずつ増やすことを繰り返すことができます。

`ParameterSweeper.vi` はどの制御器の値を変更するかを `vi` と `ctrl` で変更可能なので、どんな機器のどんなパラメータでも周期的に掃引できます。これが１つあれば「値を掃引する」というプロブラムを作る必要がなくなります。このように、メタプログラミング的なやり方により、制御アルゴリズムを制御される VI と完全に独立させて開発できるようになります。

制御するだけでなく測定も行いたいので [`ADCDataRecorder.vi`](hardware/#adcdatarecordervi--adc-の電圧値を記録するアプリ) を使います。

![](hardware/image4md/panel-ADCDataRecorder.png)

このプログラムは ADC で周期的に電圧を測定して表示したり、データファイルに保存したりできます。

`ParameterSweeper.vi` の `Record Sync` を `on` にしておくと、パラメータの掃引開始と同期してデータの記録を開始できます。時間の同期は精度が低いですが、100 ms 以上の時定数を指定したロックインアンプの出力を記録するというような用途では十分な同期が得られます。

注目すべきは `Parameters to Save` の欄です。ここに VI 名とコントロール名を列挙するとデータ保存の開始時にコントロールから値を読み出してデータファイルのヘッダーに追加できます。「このデータを測定したときの設定値があやふや」という事態を防ぐには VI のコントロールの値をデータファイルに保存しておくのが一番ですが、普通にそれをしようと思うとデータ書き込みロジックのところで数十個のコントロールから値を読みだして、すべてを文字列に直さなければならず、新しい測定用に VI を作るたびにそれを書くのはものすごくおっくうです。

このリポジトリに含まれるライブラリはコントロールからの値を文字列として読み出せるため、そのままファイルに保存できます。また、複数のVIにある複数のコントロールの値をまとめて読み書きするための [`SetControlValues.vi`](lib/SetGetControlValue/#setcontrolvaluesvi) / [`GetControlValues.vi`](lib/SetGetControlValue/#getcontrolvaluesvi) も提供されています（分かりにくいですが Value's' と s が付いています）。

これで、任意の機器制御 VI の任意の制御器の値を掃引して ADC でデータを測定し、測定パラメータと共にファイルに保存する、という難しいタスクを、「制御器の値が変わったら機器の設定を変更する」という単機能 VI を作成するだけで行えるようになりました。

あとは複数のの機器制御 VI の上にある制御器の値を測定の種類ごとに初期化することができれば言うことがありません？

[SettingManager.vi](utilities/#settingmanagervi) は、複数の VI にまたがって多数の制御器の値を「設定」として複数保存しておき、ダブルクリック一発で値を復元することが可能なアプリケーションです。

![](utilities/image4md/panel-SettingManager.png)

左側に VI とその上の制御器の一覧が出るので、保存したいものを選んで `Save` を押すと右側に設定項目ができます。この項目をダブルクリックすると保存したときの値が書き戻されます。

保存する際にコントロールだけでなく VI を選んでおくと VI の表示位置も復元されます。

また、復元時に VI が開かれていない場合には自動で開くこともできます。

上記の `ParameterSweeper.vi` や `DataRecorder.vi` は非常に汎用性の高いプログラムなので、異なる測定には異なる設定で利用したくなるのですが、それぞれに対する設定を `SettingManager.vi` で保存しておけば簡単に測定を開始できます。

ということで、LabVIEW メタプログラミングをお勧めしています。

改訂履歴
--
- 2023-10-13 初出
- 2023-10-16
  - [`lib/SetGetControlValue`](lib/SetGetControlValue) や [`utilities/SettingManager.vi`](utilities/#settingmanagervi) でコントロール名に含まれる改行やタブ文字をエスケープするように/できるようにした
  - [`lib/#技術的な話`](lib/#技術的な話)　で LabVIEW 標準の VI の関数 GetControlIndexByName からはコントロールのリファレンスを得られないことを追記した
  - 未保存の `Untitled 1.vi` のような VI に対しては VI のリファレンスを取れない。これが原因で [`utilities/SettingManager.vi`](utilities/#settingmanagervi) がエラーを吐くのを抑制した。エラーの生じた VI の下にはコントロールが表示されず空になる。
  - [lib/SetGetControlValue/#クローン V Iについて](lib/SetGetControlValue/#クローン-VI-について) にてクローン VI に対しては VI リファレンスを取る手段が提供されていないという記載を LabVIEW ヘルプに発見したので追記した
  - [`utilities/SettingManager.vi`](utilities/#settingmanagervi) コントロールのリロードボタンにショートカットキー F5 を割り当てた
  - [`GetControlValues.vi`](lib/SetGetControlValue#getcontrolvaluesvi) に `As List` オプションを追加した
- 2023-10-17
  - [`InsertMenubarItem.vi`](lib/UI/#insertmenubaritemvi) を追加した
- 2023-10-20
  - [`GetControlValue.vi`](lib/SetGetControlValue#getcontrolvaluevi), [`SetControlValue.vi`](lib/SetGetControlValue#setcontrolvaluevi) に `control ref` 端子を追加し、アクセスするコントロールを直接指定できるようにした。
- 2023-10-21
  - [lib/Hardware](lib/Hardware) に以下の２つを追加した
    - `ParamClusterControl.vi` ハードウェアの設定をテキストコマンドで読み書きするプログラムを作成するのに用いる
    - `SerialPortSetup.vi` シリアルポートの設定を行う UI を提供する
- 2023-10-23
  - 複数の `ParamClusterControlCore.vi` クローンで `IO Queue` を共有できるようにし、その使用例を追加した `example2-ParamClusterControlCore.vi`
- 2023-10-24
  - `CoerseNumericType.vi` の `Fixed Point` 値の扱いを改善した
  - それに合わせて `lib/Variant` のドキュメントを拡充した
  - `ControlValueToJSON.vi` のドキュメントを追加した
- 2023-10-25
  - `SettingManager.vi` の改造
    - `Read From INI` で `Encode` をしくじっていたのを直した
    - 設定にオプションを追加した
      - VI ロードの確認なし
      - VI の自動実行
      - サブ設定の自動実行
      - 実行後のウェイト
    - メッセージ処理を見直して `Reload` が複数回呼ばれる問題を解消
    - `Update Modified State` で `DeferPanelUpdate` を効かせた
- 2023-10-27
  - `lib/Hardware/ParamClusterControl.vi` を削除して、代わりに `lib/Hsardware/TextControlledInstrum` ライブラリを作成した
    - １つのクラスタを制御するだけでなく、クラスタを含む複数のコントロールの制御がより簡単に行えるようになった
  - 同ライブラリを使って `hardware/SignalRecovery7280Lock-inAmp.vi` を作成した