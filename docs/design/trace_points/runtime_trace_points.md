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

### Tracepoints for representing flow of message transmission

```mermaid

graph LR
  source_timestamp[[source_timestamp]]
  message_dds[[message_addr]]
  message_intra[[message_addr]]
  message_inter[[message_addr]]
  publish(("publish(message)"))
  source_timestamp[[source_timestamp]]
  message_intra_sub[[message_addr]]

  rclcpp_intra_publish[rclcpp_intra_publish<ul><li>publisher_handle</li><li>message_addr</li><li>message_timestamp</li></ul>]

  message_construct_intra[message_construct<ul><li>original_message_addr</li><li>constructed_message_addr</li></ul>]
  message_construct_inter[message_construct<ul><li>original_message_addr</li><li>constructed_message_addr</li></ul>]

  rclcpp_publish[rclcpp_publish<ul><li>publisher_handle</li><li>message_addr</li><li>message_timestamp</li></ul>]

  rcl_publish[rcl_publish<ul><li>publisher_handle</li><li>message_addr</li></ul>]
  dds_write[dds_write<ul><li>message_addr</li></ul>]
  dds_bind_addr_to_addr[dds_bind_addr_to_addr<ul><li>addr_from</li><li>addr_to</li></ul>]
  dds_bind_addr_to_stamp[dds_bind_addr_to_stamp<ul><li>message_addr</li><li>source_timestamp</li></ul>]

  publish -->  rclcpp_intra_publish
  publish -->  rclcpp_publish

  subgraph message_copy_if_necessary
    rclcpp_intra_publish <--> message_intra
    rclcpp_publish <--> message_inter
    message_construct_intra -.-> message_intra
    message_intra -.-> message_construct_intra
    message_construct_inter -.-> message_inter
    message_inter -.-> message_construct_inter
  end


  message_inter <--> rcl_publish
  message_inter <--> dds_write
  dds_write <--> message_dds

  message_dds <-->  dds_bind_addr_to_stamp
  dds_bind_addr_to_stamp <--> source_timestamp

  dds_bind_addr_to_addr -.-> message_dds
  message_dds -.-> dds_bind_addr_to_addr

```

If a topic message is defined with unique_ptr and transmitted to multiple subscription by `publish` method, the topic message may be copied.  
CARET can associate an address of original message to that of copied one by `message_construct`.  
A certain message is identified with an unique address in rcl layer, it is identified with `source_timestamp` in DDS layer.  
All messages communicated via DDS have `source_timestamp` given automatically, which are introduced for QoS function.A pair of publish and subscription same `source_timestamp`.  
CARET utilizes `source_timestamp` to map transmitted message to received one.

CARET maps a `message_addr` to a published message for intra-process communication, and a `source_timestamp` to one for inter-communication.

### Tracepoints for representing flow of callback execution after message reception

```mermaid

graph LR
  source_timestamp[[source_timestamp]]
  message_intra_sub[[message_addr]]
  message_intra[[message_addr]]
  callback[[callback]]
  source_timestamp[[source_timestamp]]
  source_timestamp_sub[source_timestamp]

  dispatch_subscription_callback[dispatch_subscription_callback<ul><li>message_addr</li><li>callback</li><li>source_timestamp</li><li>message_timestamp</li></ul>]
  dispatch_intra_process_subscription_callback[dispatch_intra_process_subscription_callback<ul><li>message_addr</li><li>callback</li><li>message_timestamp</li></ul>]

  callback_end_[callback_end<ul><li>callback</li></ul>]
  callback_start[callback_start<ul><li>callback</li><li>is_intra_process</li></ul>]


  source_timestamp --Inter Process Communication--> source_timestamp_sub
  message_intra --Intra Process Communication--> message_intra_sub
  message_intra_sub --> dispatch_intra_process_subscription_callback

  dispatch_intra_process_subscription_callback --> callback

  callback --> callback_start
  callback --> callback_end_

  dispatch_subscription_callback --> callback
  source_timestamp_sub --> dispatch_subscription_callback
```

As well as flow of message transmission, a message is identified by `message_addr` for intra-process communication, but `source_timestamp` for inter-process communication.

`message_addr` and `source_timestamp` is mapped a corresponding `callback_start`.  
As explained, `message_addr` and `source_timestamp` are identifier for transmission flow using `publish` method.
This means that a invoked `publish` method is mapped to a `callback_start`.

However this does not mean that CARET can map a `callback_start` to a corresponding `publish`. CARET can trace a certain flow from `publish` to `callback_start`, but the reversed mapping, from `callback_start` to `publish`, is not supported by CARET. The capability of mapping between callback_start and publish will be improved in v0.3.\* release or later.

### ros2:callback_start

[Original tracepoints]

Sampled items

- void \* callback
- bool is_intra_process

---

### ros2:callback_end

[Original tracepoints]

Sampled items

- void \* callback

---

### ros2:message_construct

[Extended tracepoints]

Sampled items

- void \* original_message
- void \* constructed_message

---

### ros2:rclcpp_intra_publish

[Extended tracepoints]

Sampled items

- void \* publisher_handle
- void \* message
- uint64_t message_timestamp

---

### ros2:dispatch_subscription_callback

[Extended tracepoints]

Sampled items

- void \* message
- void \* callback
- uint64_t source_timestamp
- uint64_t message_timestamp

---

### ros2:dispatch_intra_process_subscription_callback

[Extended tracepoints]

Sampled items

- void \* message
- void \* callback
- uint64_t message_timestamp

---

### ros2:rcl_publish

[Original tracepoints]

Sampled items

- void \* publisher_handle
- void \* message

---

### ros2:rclcpp_publish

[Original tracepoints]

Sampled items

- void \* publisher_handle
- void \* message
- uint64_t message_timestamp

### ros2_caret:dds_write

[Hooked tracepoints]

Sampled items

- void \* message

---

### ros2_caret:dds_bind_addr_to_stamp

[Hooked tracepoints]

Sampled items

- void \* addr
- uint64_t source_stamp

---

### ros2_caret:dds_bind_addr_to_addr

[Hooked tracepoints]

Sampled items

- void \* addr_from
- void \* addr_to
