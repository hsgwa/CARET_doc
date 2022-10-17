# Subscription

## Inter process communication

```plantuml
@startuml
title: Definition of major tracepoints

participant UserCode
participant rclcpp
participant rcl
participant rmw
participant DDS
participant LTTng

activate DDS
activate rmw
activate rcl
activate rclcpp

DDS -> rmw
deactivate DDS

rmw -> rcl
deactivate rmw

rcl -> rclcpp
deactivate rcl


rclcpp --> LTTng: [dispatch_subscription_callback]
rclcpp --> LTTng: [callback_start]
rclcpp -> UserCode: callback start
activate UserCode
UserCode -> rclcpp: callback end
deactivate UserCode

deactivate rclcpp
@enduml
```

- callback_start_timestamp
- message_timestamp
- source_timestamp

## Intra process communication

```plantuml
@startuml
title: Definition of major tracepoints

participant UserCode
participant rclcpp
participant LTTng

activate rclcpp


rclcpp --> LTTng: [dispatch_intra_subscription_callback]
rclcpp --> LTTng: [callback_start]
rclcpp -> UserCode: callback start
activate UserCode
UserCode -> rclcpp: callback end
deactivate UserCode

deactivate rclcpp
@enduml
```

| trace points             | description                                            |
| ------------------------ | ------------------------------------------------------ |
| callback_start_timestamp | system time                                            |
| message_timestamp        | time of header.stamp. zero when header is not defined. |

See also

- [Subscription API](https://tier4.github.io/CARET_analyze/latest/infra/#caret_analyze.infra.lttng.records_provider_lttng.RecordsProviderLttng.subscribe_records)
- [Runtime trace points](../../trace_points/runtime_trace_points)
