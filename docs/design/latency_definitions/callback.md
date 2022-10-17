# Callback

The callback latency is the time from callback start to callback end.

$$
l_{\rm{callback}} = t_{\rm{callback\ end}} - t_{\rm{callback\ start}}
$$

A simplified sequence diagram focusing only on the relevant data flow is shown below.

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

`to_dataframe` API returns following columns.

| trace points                                                                 | description         |
| ---------------------------------------------------------------------------- | ------------------- |
| [callback_start](../../trace_points/runtime_trace_points#ros2callback_start) | callback start time |
| [callback_end](../../trace_points/runtime_trace_points#ros2callback_end)     | callback end time   |

See also

- [RuntimeDataProvider API](https://tier4.github.io/CARET_analyze/latest/infra/#caret_analyze.infra.lttng.lttng.Lttng.compose_callback_records)
