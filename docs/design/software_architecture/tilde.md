# TILDE

ROS のノードには任意の処理が記述されます。
そのため、ひとえにノードレイテンシといっても、その定義は多様に存在します。

たとえば、こういう実装です。

- subscribe して publish するまでにバッファが入っているケース
- message filter を使用しているケース

CARET では、デフォルトの挙動として use_latest_message を提供していますが、[Limits and constraints](../limits_and_constraints/)で挙げている通り、正しく計測できないです。
CARET ではこのような任意のノードレイテンシに対して対応するために、ノードへの実装変更を必要とします。
実装変更としては、姉妹ツールの TILDE を使います。

TILDE については[TILDE の公式サイト](https://github.com/tier4/TILDE)をご覧ください。

## Cooperation with TILDE, a framework tools to detect deadline overrun

CARET can cooperate with TILDE, a framework tool to detect deadline overrun. TILDE lets CARET trace events in user applications which cannot be traced from ROS/DDS layer.

One example of the events is consumption of message buffered in nodes. The events can be observed in only application layer, but CARET cannot observe the events. On the other hand, TILDE can trace application-layer events. It is able to trace execution of callback function to consume a certain buffered message since it annotate message consumption per single message. TILDE serves CARET this capability to trace consumption of buffered message.

<prettier-ignore-start>
!!! todo
        TILDE is now under development. Please wait for more details.
<prettier-ignore-end>

ノードレイテンシについては、[ノードレイテンシの定義](../latency_definitions/node_latency.md)をご覧ください。

CARET では、TILDE の内部にもトレースポイントを追加し、subscribe したメッセージと publish したメッセージの依存関係を取得できるようにしています。
