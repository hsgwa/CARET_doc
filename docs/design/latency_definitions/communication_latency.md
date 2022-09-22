CARET では、通信レイテンシを以下のように定義しています。

$$
l^{comm} = t^{msg}_{in} - t^{msg}_{out}
$$

メッセージがノードまたはコールバックの所有権に出る時刻から、入る時刻までとなっています。

これは将来 tf を含めたレイテンシでも計測するためになっています。

- スケジューリングによって影響を受ける時間になっています。

特に、publish-subscription の場合には、以下のように定義されます。

$$
l^{comm} = t_{sub} - t_{pub}
$$

## プロセス内通信

```plantuml

participant UserCode
participant rclcpp
participant LTTng

activate UserCode
UserCode -> rclcpp : publish
deactivate UserCode

```

## プロセス間通信
