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

<prettier-ignore-start>
!!! todo
        スケジューリングの影響を受けるようになっています。通信レイテンシだけではありません。
<prettier-ignore-end>

## プロセス内通信

TODO: 処理のシーケンスを正しく直す。

```plantuml


participant "UserCode\n(Publisher Side)" as Callback
participant "rclcpp\n (Publisher/Subscription)" as Rclcpp
participant "rclcpp\n (Buffer)" as Buffer
participant rcl
participant LTTng

== Publisher ==

-> Callback
activate Callback

Callback -> Rclcpp : publish
activate Rclcpp
Rclcpp -> LTTng : intra process publish

Rclcpp -> Buffer : enqueue
activate Buffer
Rclcpp -> rcl : notify
deactivate Rclcpp
activate rcl

deactivate Callback

== Subscription ==

rcl -> Rclcpp : dispatch
deactivate rcl

activate Rclcpp

Rclcpp -> Buffer : take
Buffer -> Rclcpp: dequeue
deactivate Buffer

Rclcpp -> LTTng : callback start
Rclcpp -> Callback : execute callback

activate Callback

```

## プロセス間通信

```plantuml
@startuml
title: Definition of major tracepoints

participant UserCode
participant ROS2
participant DDS
participant LTTng

== Publisher Side ==

activate ROS2
activate UserCode
UserCode -> ROS2: publish()
activate ROS2
ROS2 --> LTTng: [rclcpp_publish] \n[rclcpp_intra_publish]


ROS2 -> DDS: dds_write()
deactivate ROS2
activate DDS
DDS --> LTTng: [dds_write]

UserCode -> ROS2 : callback_end
deactivate UserCode
deactivate ROS2
deactivate DDS


== Subscription Side ==

UserCode -> ROS2 : spin()
activate DDS
activate ROS2

loop
    ROS2 -> ROS2 : wait until next event
    activate ROS2
    DDS -> ROS2: <notify> on_data_available
    ROS2 --> LTTng : [on_data_available]
    deactivate ROS2

    deactivate DDS
    ROS2 -> DDS : reset ready-set, take messages
    activate DDS
    DDS -> ROS2
    deactivate DDS

    group execute timer callbacks
    end

    group execute subscription callbacks
        ROS2 --> LTTng: [callback_start]
        ROS2 -> UserCode: callback start
        activate UserCode
        UserCode -> ROS2: callback end
        deactivate UserCode
        ROS2 --> LTTng: [callback_end]
    end

    group execute service callbacks
    end

    group execute client callbacks
    end
end

deactivate ROS2
@enduml
```
