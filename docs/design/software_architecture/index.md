# Software architecture

This section explains an overview of software architecture.

![architecture](../../imgs/architecture.drawio.png)

CARET is divided into two phase; recording phase and analyzing phase.

## Recording Phase

Recording phase records system execution data from trace points.

In addition to the tracepoints built into ROS2, CARET adds tracepoints by several methods; CARET_trace CARET_rclcpp and TILDE.
For flexibility in adding tracepoints, tracepoints are added by hooks if possible.
Only if it is not possible to add tracepoints by hooks, tracepoints are added by other methods as a supplementary method.

All recorded data is stored as TraceData and used in the analyzing phase.

CARET utilizes LTTng as a trace mechanism.

See also

- [Tracepoints](../trace_points)
- [Runtime Processing](../runtime_processing)
- [The LTTng Documentation](https://lttng.org/docs/)

## Analyzing Phase

In analyzing ahase, CARET analyzes trace data to calculate and visualize latency.

A trace data is divided into two sections by CARET_analyze after loading trace data; Architecture and Runtime Data.

Architecture データはパスの定義などを記述しています。
評価対象のパスの定義などは、デベロッパーが指定する必要があります。
一度設定した Architecture 情報は yaml ファイルとして保存し、次回から使用が可能です。

Runtime Data は測定値など測定毎に変化する情報です。
実行時の情報は事前に定義した Architecture 情報と結び付けられ、評価しやすい形の python-API としてデベロッパーに提供されます。

CARET_analyze は PythonAPI を提供することが重要な役割としてありますが、
jupyter 上での評価を簡単にするための可視化も提供しています。

See also

- [Processing trace data | Overview](../processing_trace_data)
- [Latency definitions | Overview](../latency_definitions)
- [Visualizations | Policy](../visualizations)

## ROS 2 Packages

CARET に関連するパッケージと主な役割を以下にまとめます。

| Package                             | Role                                             | Repository                                                                                           |
| ----------------------------------- | ------------------------------------------------ | ---------------------------------------------------------------------------------------------------- |
| [CARET_trace](./caret_trace.md)     | Add trace points via hooks. Control tracepoints. | [https://github.com/tier4/CARET_trace/](https://github.com/tier4/CARET_trace/)                       |
| CARET_rclcpp                        | Add trace points by fork                         | [https://github.com/tier4/rclcpp](https://github.com/tier4/rclcpp)                                   |
| ros2caret                           | Provide CARET CLI                                | [https://github.com/tier4/ros2caret/](https://github.com/tier4/ros2caret/)                           |
| [CARET_analyze](./caret_analyze.md) | Analyze trace data                               | [https://github.com/tier4/CARET_analyze/](https://github.com/tier4/CARET_analyze/)                   |
| CARET_analyze_cpp_impl              | Accelerate CARET_analyze                         | [https://github.com/tier4/CARET_analyze_cpp_impl/](https://github.com/tier4/CARET_analyze_cpp_impl/) |
| [TILDE](./tilde.md)                 | Add tracepoints within the system to be measure  | [https://github.com/tier4/TILDE](https://github.com/tier4/TILDE)                                     |
