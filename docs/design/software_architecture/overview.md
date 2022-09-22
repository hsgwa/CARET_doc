# Architecture overview

This section explains an overview of software architecture of CARET.

![architecture](../../imgs/architecture.drawio.png)

Architecture object serves APIs to search node chains and define node latency as mentioned in [tutorial/architecture file section](../tutorials/configuration.md). Architecture object is reusable after it is saved as a YAML-based file called "architecture file".  
Runtime Data object serves APIs to retrieve `pandas.DataFrame`-based objects including callback latency or communication. Users can analyze temporal aspects of their applications, with visualization, as they expect. APIs for visualization are also served by CARET_analyze which plays main role to analyze trace data.

A set of trace data is divided into two sections by CARET_analyze package after loading trace data; Architecture and Runtime Data. Architecture object includes description of target application's structure. This object can be reused unless structure of the target application or name of the components is changed. Runtime Data object has data sampled during execution of the target application. The sampled data includes timestamps, whose value are different per execution, obtained from tracepoints.

Architecture object and Runtime Data object are implemented as Python-based classes. The structure of their classes are designed based on the structure of ROS applications which are constructed of executors, nodes, callback functions, and topic messages. ROS-based structure makes CARET's API friendly for ROS users. They are able to find target nodes, topic messages, or executors if they know their application structure.

| package                | role                                             |
| ---------------------- | ------------------------------------------------ |
| CARET_trace            | フックでのとレースポイント追加や、ランタイム処理 |
| CARET_rclcpp           | フォークによるトレースポイント追加               |
| ros2caret              | CARET CLI                                        |
| CARET_analyze          | トレースデータの解析                             |
| CARET_analyze_cpp_impl | CARET_analyze の一部高速化実装                   |
