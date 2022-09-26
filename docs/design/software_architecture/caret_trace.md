# caret_trace

caret_trace はトレースポイントの追加や管理を行うパッケージです。

caret_trace の役割は以下の通りです。

- レースポイント定義
- フックでのとレースポイント追加
- trace filtering
- simtime の記録

ここでは、実装についての説明のみを行います。

- フックについての説明は[Hook](../runtime_processing/hook.md)をご覧ください。
- 定義したトレースポイントについては[Tracepoints](../trace_points/overview.md)をご覧ください。

詳細については、[CARET_trace](https://github.com/tier4/CARET_trace)をご覧ください。

## クラス構成

```plantuml

class HashableKeys<T1, T2, ...> {
    + insert(args): void
    + has(): bool
    + hash(): size_t
}

class KeysSet<T1, T2, ...> {
    + insert(args): void
    + has(args): void
}

class Singleton<T> {
    + get_instance(): T
}

class TracingController {
    + add_node(args): void
    + is_allowed_node(args) : bool
}

KeysSet o-- HashableKeys
```

## フック箇所の実装

CARET では、フックでのとレースポイント追加のためのフックに加え、
ROS2 に組み込まれたトレースポイントもフックしています。

典型的なフック箇所の実装例を示します。

```C++
void ros_trace_rcl_node_init(
  const void * node_handle,
  const void * rmw_handle,
  const char * node_name,
  const char * node_namespace)
{
  // TracingControllerのコンストラクタで、環境変数からフィルタ対象のノード名とトピック名を取得。
  static auto & controller = Singleton<TracingController>::get_instance();

  // node_handleとノード名の紐付け
  controller.add_node(node_handle, ns + node_name);

  if (controller.is_allowed_node(node_handle)) {
    ORIG_FUNC::ros_trace_rcl_node_init)(node_handle, rmw_handle, node_name, node_namespace);
  }
}

void ros_trace_callback_start(const void * callback, bool is_intra_process) {
  static auto & controller = Singleton<TracingController>::get_instance();

  if (controller.is_allowed_callback(callback)) {
    ORIG_FUNC::ros_trace_callback_start)(callback, is_intra_process);
  }
}
```

デバッグログなどについては省略しています。

callback のアドレスからノード名などの情報へは、他のトレースポイントの情報から紐付けを行うことで取得できます。
詳細は[Initialization trace points](../trace_points/initialization_trace_points.md)をご覧ください。

## simtime recorder

CARET は可視化の際に simtime を選択できます。
simtime は simtime 記録用のトレースポイントを追加した、simtime_recorder node を起動することで記録できます。

```bash
ros2 run caret_trace simtime_recoreder
```
