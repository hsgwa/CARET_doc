Design では、CARET の内部について説明します。
以下に、Tracing Flow に各パッケージを書き加えた測定フローを示します。

![architecture](../imgs/design.drawio.png)

CARET collects data, which include timestamps, from tracepoints embedded in user application, ROS 2 and DDS. The data set is stored into a storage as "Trace Data".
CARET は Trace Data を解析し、デベロッパーに結果を提供します。

Design の各章では、以下に示す各工程に対して、方針や内部処理について説明をします。

1. トレースポイントの実行時処理
1. トレースポイントの追加
1. データの加工
1. レイテンシの算出
1. 可視化

また、以下についても説明します。

- ソフトウェアアーキテクチャ
- レイテンシの定義
- 制限

なお、各クラスの API については以下をご覧ください。
[CADRET analyze API document](https://tier4.github.io/CARET_analyze/)
