# Latency definition

![Latency overview](../../imgs/latency_overview.png)

| target                           | configuration required? |
| -------------------------------- | ----------------------- |
| [Path](./path)                   | Yes                     |
| [NodePath](./node)               | Yes                     |
| [Communication](./communication) | No                      |
| [Callback](./callback)           | No                      |
| [Publisher](./publisher)         | No                      |
| [Subscription](./subscription)   | No                      |
| [Timer](./timer)                 | No                      |

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
