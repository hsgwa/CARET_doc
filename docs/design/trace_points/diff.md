# Differences from original ROS

## v0.2 vs galactic

[caret.repos](https://github.com/tier4/caret/blob/main/caret.repos) には caret のリポジトリの他に、以下のリポジトリを含んでいます。

- <https://github.com/ros2/rcl.git>
- <https://github.com/tier4/rclcpp/tree/galactic_tracepoint_added>
- <https://github.com/tier4/ros2_tracing/tree/galactic_tracepoint_added>

それぞれのリポジトリの差分について説明します。

### rcl

ソースコードの変更なし。
トレースポイントを有効にするための再ビルド。

### rclcpp

リポジトリ：<https://github.com/tier4/rclcpp/tree/galactic_tracepoint_added>

追加したトレースポイントについては、[トレースポイントの定義](./supported_tracepoints.md)の **[CARET フォーク実装]**となります。

> rclcpp 実装の理由
>
> 上記トレースポイントはテンプレートで実装された関数内に挿入しています。  
> テンプレート実装はヘッダーに含まれ、アプリケーションのバイナリ内に生成されます  
> LD_PRELOAD でのフックが不可能な測定箇所なため、 rclcpp にトレースポイントの追加しています。

また、ビルドを通すために ros2_tracing の include ファイルを追加しています。

> rclcpp に ros2_tracing の include ファイルを追加した理由
>
> LD_PRELOAD では実行開始直後にカスタムの共有ライブラリを優先して読み込ませることが可能になります。  
> 一方で、上記のようなヘッダーに追加されたトレースポイントには、ビルド時のヘッダー探索にトレースポイント追加版ヘッダーが優先して読み込まれる必要あります。  
> gcc の -I フラグや CPATH などを検討しましたが、トレースポイント追加版 rclcpp/include, /opt/ros/galactic/include （ros2_tracing 含む）,トレースポイント追加版 tracetools/include の順となり、追加したトレースポイント未定義のエラーでビルドが通せません。  
> そのため、 トレースポイント追加版 tracetools/include に追加されていないトレースポイントは、一時的に rclcpp/include 配下に追加しました。  
> ros2 本家へのトレースポイントのマージを検討していく際には、rclcpp への ros2_tracing の include ファイル追加は必要ありません。

### ros2_tracing

rclcpp で追加したトレースポイントの定義を追加

リポジトリ：<https://github.com/tier4/ros2_tracing/tree/galactic_tracepoint_added>

フックではなく、rclcpp へトレースポイントを追加した理由については、「rclcpp 実装の理由」を参照。

## v0.3 vs humble
