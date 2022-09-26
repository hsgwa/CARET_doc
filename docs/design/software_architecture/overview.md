# Architecture overview

This section explains an overview of software architecture of CARET.

![architecture](../../imgs/architecture.drawio.png)

CARET は ROS2 に組み込まれたトレースポイントに加え、いくつかの方法によりトレースポイントを追加します; CARET_trace, CARET_rclcpp, TILDE
柔軟性をもたせるために、可能な限りフックによるトレースポイントの追加を行い、補助的に他の方法でトレースポイントを追加しています。

トレースポイントで取得した情報は、TraceData として全て保存されます。

A set of trace data is divided into two sections by CARET_analyze package after loading trace data; Architecture and Runtime Data.

Architecture データはパスの定義などを記述しています。
評価対象のパスの定義などは、デベロッパーが指定する必要があります。
一度設定した Architecture 情報は yaml ファイルとして保存し、次回から使用が可能です。

Runtime Data は測定値など測定毎に変化する情報です。
実行時の情報は事前に定義した Architecture 情報と結び付けられ、評価しやすい形の python-API としてデベロッパーに提供されます。

CARET_analyze は PythonAPI を提供することが重要な役割としてありますが、
jupyter 上での評価を簡単にするための可視化も提供しています。

それぞれのパッケージと主な役割を以下にまとめます。

| package                | role                                             |
| ---------------------- | ------------------------------------------------ |
| CARET_trace            | フックでのとレースポイント追加や、ランタイム処理 |
| CARET_rclcpp           | フォークによるトレースポイント追加               |
| ros2caret              | CARET CLI                                        |
| CARET_analyze          | トレースデータの解析                             |
| CARET_analyze_cpp_impl | CARET_analyze の一部高速化実装                   |
