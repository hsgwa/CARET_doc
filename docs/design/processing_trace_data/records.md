CARET ではレイテンシ算出に関わるトレースデータの加工をテーブルで行っています。

pandas.DataFrame-like な API を備えており、処理速度を早くした C++実装版もあります。

ここではいくつかの主な API について説明します。

## merge

テーブルの内部結合、外部結合です。

## merge_sequential

時系列的なマージです。
特に thread による逐次的な処理を結合する際に使います。

## merge_sequential_for_addr_trck

アドレスを元に紐付けを行い、コピーが発生する際に使うマージです。

## to_dataframe

pandas.DataFrame に変換する関数です。
特に可視化や自前で評価する際には、こちらが使えます。
