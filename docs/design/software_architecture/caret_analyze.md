# CARET_analyze

caret_analyze is a package that loads trace data and architecture files and provides developers with a Python API for configuration and evaluation

See [CARET analyze API document](https://tier4.github.io/CARET_analyze/) for the API of each class.

The following figure shows data flow in CARET_analyze.

![caret_analyze_architecture](../../imgs/architecture_caret_analyze.png)

Trace data is divided into two sections by CARET_analyze after loading trace data; Architecture and Runtime Data.

The architecture object includes descriptions of the target application's structure. This object can be reused unless the structure of the target application or names of the components is changed.

Runtime Data object has data sampled during the execution of the target application. The sampled data includes timestamps, whose values are different per execution, obtained from tracepoints.
Runtime data is combined with architecture and provided to developers via Python-API which is easy to evaluate.

Architecture object and Runtime Data object are implemented as Python classes. The structure of their classes is designed based on the structure of ROS applications which are constructed of executors, nodes, callback functions, and topic messages. ROS-based structure makes CARET's API friendly for ROS users. They can find target nodes, topic messages, or executors if they know their application structure.

CARET_analyze is composed of several python packages.
Each python packages are as follows.

| python package | role                            |
| -------------- | ------------------------------- |
| architecture   | Load and configure Architecture |
| runtime        | Provide execution data          |
| value_objects  | collection of value objects     |
| plot           | Visualization helpers           |
| records        | implement records               |
| common         | common procedure                |
| infra          | import outer files              |

Role for each component is as follows.

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

Architecture object serves APIs to search node chains and define node latency as mentioned in [tutorial/architecture file section](../tutorials/configuration.md). The architecture object is reusable after it is saved as a YAML-based file called "architecture file".

Runtime Data object serves APIs to retrieve `pandas.DataFrame`-based objects including callback latency or communication. Users can analyze temporal aspects of their applications, with visualization, as they expect. APIs for visualization are also served by CARET_analyze which plays the main role to analyze trace data.

## architecture

architecture では、以下のような構成を持ったインスタンスを生成します。
これにより、各クラスから辿っていくことで、必要な情報を取得できるようにしています。

Architecture の目的

- 静的な情報のみを取り扱う。
- Analyze で使う情報を定義する

<prettier-ignore-start>
!!! todo
        本当はレイテンシの値以外の情報は全て入れたい気持ちです。
        設計に役に立たせたい気持ちだけあります。
<prettier-ignore-end>

```plantuml

class Architecture { }
class CallbackStructValue { }
class NodeStructValue { }
class PublisherStructValue { }
class SubscriptionStructValue { }
class ExecutorStructValue { }
class TimerStructValue { }
class CallbackGroupStructValue { }
class CommunicationStructValue { }
class PathStructValue { }
class NodePathStructValue { }
class VariablePassingStructValue { }

Architecture o-- NodeStructValue
Architecture o-- CommunicationStructValue
Architecture o-- PathStructValue
Architecture o-- ExecutorStructValue
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

class Application { }
class CallbackBase { }
class Node { }
class Publisher { }
class Subscription { }
class Executor { }
class Timer { }
class CallbackGroup { }
class Communication { }
class Path { }
class NodePath { }
class VariablePassing { }

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

| Class         | API                                                                                                   | has to_dataframe? (Definitions)                              |
| ------------- | ----------------------------------------------------------------------------------------------------- | ------------------------------------------------------------ |
| Application   | [API list](https://tier4.github.io/CARET_analyze/latest/runtime/#caret_analyze.runtime.Application)   | No                                                           |
| Executor      | [API list](https://tier4.github.io/CARET_analyze/latest/runtime/#caret_analyze.runtime.Executor)      | No                                                           |
| Node          | [API list](https://tier4.github.io/CARET_analyze/latest/runtime/#caret_analyze.runtime.Node)          | No                                                           |
| Path          | [API list](https://tier4.github.io/CARET_analyze/latest/runtime/#caret_analyze.runtime.Path)          | Yes ([Definitions](../../latency_definitions/path))          |
| NodePath      | [API list](https://tier4.github.io/CARET_analyze/latest/runtime/#caret_analyze.runtime.NodePath)      | Yes ([Definitions](../../latency_definitions/node_path))     |
| Communication | [API list](https://tier4.github.io/CARET_analyze/latest/runtime/#caret_analyze.runtime.Communication) | Yes ([Definitions](../../latency_definitions/communication)) |
| Timer         | [API list](https://tier4.github.io/CARET_analyze/latest/runtime/#caret_analyze.runtime.Timer)         | Yes ([Definitions](../../latency_definitions/timer))         |
| Subscription  | [API list](https://tier4.github.io/CARET_analyze/latest/runtime/#caret_analyze.runtime.Subscription)  | Yes ([Definitions](../../latency_definitions/subscription))  |
| Publisher     | [API list](https://tier4.github.io/CARET_analyze/latest/runtime/#caret_analyze.runtime.Publisher)     | Yes ([Definitions](../../latency_definitions/publisher))     |
| Callback      | [API list](https://tier4.github.io/CARET_analyze/latest/runtime/#caret_analyze.runtime.Callback)      | Yes ([Definitions](../../latency_definitions/callback))      |

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

yaml, Lttng が入っており、
それぞれ reader/provider に与えるようになっています。
