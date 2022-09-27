# CARET_analyze

caret_analyze はトレースデータやアーキテクチャファイル読み込み、デベロッパーに Python API を提供するパッケージです

なお、各クラスの API については以下をご覧ください。
[CADRET analyze API document](https://tier4.github.io/CARET_analyze/)

CARET_analyze のデータの流れを以下に示します。

![caret_analyze_architecture](../../imgs/architecture_caret_analyze.png)

Architecture object includes description of target application's structure. This object can be reused unless structure of the target application or name of the components is changed. Runtime Data object has data sampled during execution of the target application. The sampled data includes timestamps, whose value are different per execution, obtained from tracepoints.

Architecture object and Runtime Data object are implemented as Python classes. The structure of their classes are designed based on the structure of ROS applications which are constructed of executors, nodes, callback functions, and topic messages. ROS-based structure makes CARET's API friendly for ROS users. They are able to find target nodes, topic messages, or executors if they know their application structure.

CARET_analyze は複数の python パッケージで構成されています。

それぞれのパッケージの役割については、以下に示したとおりです。

| python package | role                             |
| -------------- | -------------------------------- |
| architecture   | Architecture の読み込み          |
| runtime        | 実行状態の読み込み               |
| value_objects  | 各パッケージ間のインターフェース |
| plot           | 表示系のヘルパー                 |
| records        | レイテンシ算出の基礎処理         |
| common         | 共通処理                         |
| infra          | 読み込み                         |

各コンポーネントの主な関係は以下の通りです。

```plantuml
package architecture {
  component Architecture
}
package runtime {
  component Application
}
package infra {
  component Lttng
  component Yaml
}
package value_objects {
  component NodeStructValue
  component NodeValue
}
package records {
  component Records
  component RecordsCppImpl
}

package plot {
  component Plot
}

interface "Runtime Data Provider" as Idata_provider
interface "Architecture Reader" as Iarchitecture_reader

interface "RecordsInterface" as Irecords


Lttng -- Idata_provider
Lttng -- Iarchitecture_reader
Yaml -- Iarchitecture_reader
Irecords -up- Records
Irecords -up- RecordsCppImpl
Application o-left- Architecture
Iarchitecture_reader <.down. Architecture : use
Idata_provider <.down. Application : use
Application <.. Plot : use
NodeValue <.. infra
architecture o-- NodeStructValue
Irecords <.. infra : use
```

Architecture object serves APIs to search node chains and define node latency as mentioned in [tutorial/architecture file section](../tutorials/configuration.md). Architecture object is reusable after it is saved as a YAML-based file called "architecture file".  
Runtime Data object serves APIs to retrieve `pandas.DataFrame`-based objects including callback latency or communication. Users can analyze temporal aspects of their applications, with visualization, as they expect. APIs for visualization are also served by CARET_analyze which plays main role to analyze trace data.

## architecture

architecture では、以下のような構成を持ったインスタンスを生成します。
これにより、各クラスから辿っていくことで、必要な情報を取得できるようにしています。

```plantuml

class Architecture {
}


class CallbackStructValue {
}

class NodeStructValue {
}

class PublisherStructValue {
}
class SubscriptionStructValue {
}
class ExecutorStructValue {
}

class TimerStructValue {
}

class CallbackGroupStructValue {
}

class CommunicationStructValue {
}

class PathStructValue {
}

class NodePathStructValue {
}

class VariablePassingStructValue {
}


ApplicationStructValue o-- NodeStructValue
ApplicationStructValue o-- CommunicationStructValue
ApplicationStructValue o-- PathStructValue
ApplicationStructValue o-- ExecutorStructValue
PathStructValue o-d- CommunicationStructValue
PathStructValue o-d- NodePathStructValue
CommunicationStructValue o-- PublisherStructValue
CommunicationStructValue o-- SubscriptionStructValue
CommunicationStructValue o-l- NodeStructValue
NodeStructValue o-- CallbackGroupStructValue
NodeStructValue o-- VariablePassingStructValue
NodeStructValue o-l- NodePathStructValue
NodePathStructValue o-- PublisherStructValue
NodePathStructValue o-- SubscriptionStructValue
NodePathStructValue o-- CallbackStructValue
CallbackGroupStructValue o-d- CallbackStructValue
CallbackStructValue o-d- PublisherStructValue
CallbackStructValue o-d- TimerStructValue
CallbackStructValue o-d- SubscriptionStructValue
ExecutorStructValue o-- CallbackGroupStructValue
```

## runtime

トレース結果も含む runtime は、architecture の関係に加え、　測定結果の算出関数が追加されています。

```plantuml

class Application {
}


class CallbackBase {
    + to_records()
    + to_dataframe()
    + to_timeseries()
    + to_histogram()

}
class Node {
}
class Publisher {
    + to_records()
    + to_dataframe()
    + to_timeseries()
    + to_histogram()
}
class Subscription {
    + to_records()
    + to_dataframe()
    + to_timeseries()
    + to_histogram()
}
class Executor {
}

class Timer {
    + to_records()
    + to_dataframe()
    + to_timeseries()
    + to_histogram()
}

class CallbackGroup {
}

class Communication {
    + to_records()
    + to_dataframe()
    + to_timeseries()
    + to_histogram()
}

class Path {
    + to_records()
    + to_dataframe()
    + to_timeseries()
    + to_histogram()
}

class NodePath {
    + to_records()
    + to_dataframe()
    + to_timeseries()
    + to_histogram()
}
class VariablePassing {
    + to_records()
    + to_dataframe()
    + to_timeseries()
    + to_histogram()
}


Application o-- Node
Application o-- Communication
Application o-- Path
Application o-- Executor
Path o-d- Communication
Path o-d- NodePath
Communication o-- Publisher
Communication o-- Subscription
Communication o-l- Node
Node o-- CallbackGroup
Node o-- VariablePassing
Node o-l- NodePath
NodePath o-- Publisher
NodePath o-- Subscription
NodePath o-- CallbackBase
CallbackGroup o-d- CallbackBase
CallbackBase o-d- Publisher
CallbackBase o-d- Timer
CallbackBase o-d- Subscription
Executor o-- CallbackGroup
```

### Application

計測対象の全てにアクセス可能なクラスです。

See also: [Application API list](https://tier4.github.io/CARET_analyze/latest/runtime/#caret_analyze.runtime.Application)

### Executor

Executor に関連したクラスです。

See also: [Executor API list](https://tier4.github.io/CARET_analyze/latest/runtime/#caret_analyze.runtime.Executor)

### Node

Executor に関連したクラスです。

See also: [Node API list](https://tier4.github.io/CARET_analyze/latest/runtime/#caret_analyze.runtime.Node)

### Path

| column         | type |
| -------------- | ---- |
| rclcpp publish |      |

See also: [Path API list](https://tier4.github.io/CARET_analyze/latest/runtime/#caret_analyze.runtime.Path)

### NodePath

| column         | type |
| -------------- | ---- |
| rclcpp publish |      |

See also: [NodePath API list](https://tier4.github.io/CARET_analyze/latest/runtime/#caret_analyze.runtime.NodePath)

### Communication

| column         | type |
| -------------- | ---- |
| rclcpp publish |      |

See also: [Communication API list](https://tier4.github.io/CARET_analyze/latest/runtime/#caret_analyze.runtime.Communication)

### VariablePassing

| column         | type |
| -------------- | ---- |
| rclcpp publish |      |

See also: [VariablePassing API list](https://tier4.github.io/CARET_analyze/latest/runtime/#caret_analyze.runtime.VariablePassing)

### Timer

| column         | type |
| -------------- | ---- |
| rclcpp publish |      |

See also: [Timer API list](https://tier4.github.io/CARET_analyze/latest/runtime/#caret_analyze.runtime.Timer)

### Subscription

| column         | type |
| -------------- | ---- |
| rclcpp publish |      |

See also: [Subscription API list](https://tier4.github.io/CARET_analyze/latest/runtime/#caret_analyze.runtime.Subscription)

### Publisher

| column         | type |
| -------------- | ---- |
| rclcpp publish |      |

See also: [Publisher API list](https://tier4.github.io/CARET_analyze/latest/runtime/#caret_analyze.runtime.Publisher)

## value_objects

ValueObjects では、同値性の持ったクラスを定義しています。
紐付けのための情報を持つ Value クラスと、紐付け後を行い複数のクラスで構造を有する StructValue が定義されています。

## plot

表示に関連したクラスがあります。

CARET_analyze が提供する可視化では、bokeh や graphviz をベースとしています。

Plot 関連の設計については[Visualizations](../visualizations/policy)をご覧ください。

## records

CARET ではレイテンシの算出はテーブルの独自の結合処理によって行います。
records パッケージでは、独自の結合処理を持ったテーブルを定義しています。

records 内で行われる処理の内容については[Records](../processing_trace_data/records)をご覧ください。

## common

各パッケージで共通として扱える、個別の処理が記述されています。

## infra

外部から読み取る処理を記述しています。
infra 以外は CARET だけに依存するようにつくっています。
