個人的に使っている LabView 用のワーキングフォルダーです
==

公開可能なものをここに公開します。

- [個人的に使っている LabView 用のワーキングフォルダーです](#個人的に使っている-labview-用のワーキングフォルダーです)
  - [GitHub アドレス](#github-アドレス)
  - [ライセンス](#ライセンス)
  - [フォルダ一覧](#フォルダ一覧)
  - [LabVIEW メタプログラミングの勧め](#labview-メタプログラミングの勧め)
  - [改訂履歴](#改訂履歴)

GitHub アドレス
--
https://github.com/osamutake/labviewfolder

ローカルに落とした状態だと良い markdown ビュアーがないと画像などが表示されないので、ドキュメントは GitHub 上で参照すると見やすいと思います。

ライセンス
--
MIT ライセンスとしますので自由にご利用ください。

フォルダ一覧
--

- `applications/` この git に含めない個別のアプリケーションを置く
- [`hardware/`](hardware/) ハードウェア関連ライブラリ
- [`lib/`](lib/) 汎用のサブ VI ライブラリ
- [`utilities/`](utilities/) 単独で利用するVIアプリ

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

