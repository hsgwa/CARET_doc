Design では、CARET の内部について説明します。
以下に、各パッケージを書き加えた測定フローを示します。

![architecture](../imgs/design.drawio.png)

CARET collects data, which include timestamps, from tracepoints embedded in user application, ROS 2 and DDS. The data set is stored into a storage as "Trace Data".

CARET は以下を行います。

1. トレースポイントの実行時処理
1. トレースポイントの追加
1. データの加工
1. レイテンシの算出
1. 可視化

上記の方針や内部処理に加え、以下についても記述します。

- ソフトウェアアーキテクチャ
- レイテンシの定義
- 制限
