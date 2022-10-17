# Callback

コールバックの開始時刻から終了時刻までの時間差です。

```plantuml


participant "UserCode \n Callback" as Callback
participant "rclcpp" as Rclcpp
participant LTTng

activate Rclcpp

Rclcpp -> LTTng : callback start
Rclcpp -> Callback : execute callback

activate Callback
Callback -> Rclcpp
deactivate Callback
Rclcpp -> LTTng : callback end
```

ここで、コールバックのレイテンシは callback start から callback end までの時間です。

$$
l_{\rm{callback}} = t_{\rm{callback\ end}} - t_{\rm{callback\ start}}
$$

| trace points                                                                 | description         |
| ---------------------------------------------------------------------------- | ------------------- |
| [callback_start](../../trace_points/runtime_trace_points#ros2callback_start) | callback start time |
| [callback_end](../../trace_points/runtime_trace_points#ros2callback_end)     | callback end time   |

See also

- [RuntimeDataProvider API](https://tier4.github.io/CARET_analyze/latest/infra/#caret_analyze.infra.lttng.lttng.Lttng.compose_callback_records)
