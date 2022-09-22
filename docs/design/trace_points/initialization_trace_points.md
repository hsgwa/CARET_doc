Some tracepoints share a same identification, such as a node ID (`node_handle`) and an address of callback instance. The shared identification associates each tracepoint to another, the association constructs structure of tracepoint relationship. The following figures show four structures.

### Tracepoints for representing structure of a node

```mermaid

graph RL
  node_handle[[node_handle]]
  subscription_handle[[subscription_handle]]
  subscription[[subscription]]
  callback[[callback]]
  timer_handle[[timer_handle]]
  client_handle[[client_handle]]
  node_handle[[node_handle]]
  publisher_handle[[publisher_handle]]

  rcl_init[rcl_init<ul><li>context_handle</li></ul>]

  rcl_node_init[rcl_node_init<br /><ul><li>node_handle</li><li>node_name</li><li>node_namespace</li><li>rmw_handle</li></ul>]

  rcl_publisher_init[rcl_publisher_init<br /><ul><li>node_handle</li><li>publisher_handle</li><li>rmw_publisher_handle</li><li>topic_name</li><li>queue_depth</li></ul>]

  rcl_subscription_init[rcl_subscription_init<ul><li>subscription_handle</li><li>node_handle</li><li>rmw_subscription_handle</li><li>topic_name</li><li>queue_depth</li></ul>]

  rclcpp_subscription_init[rclcpp_subscription_init<ul><li>subscription_handle</li><li>subscription</li></ul>]

  rclcpp_subscription_callback_added[rclcpp_subscription_callback_added<ul><li>subscription</li><li>callback</li></ul>]

  rclcpp_timer_link_node[rclcpp_timer_link_node<ul><li>timer_handle</li><li>node_handle</li></ul>]

  rclcpp_callback_register[rclcpp_callback_register<ul><li>callback</li><li>function_symbol</li></ul>]

  rmw_implementation[rmw_implementation<ul><li>rmw_impl</li></ul>]

  rcl_timer_init[rcl_timer_init<ul><li>timer_handle</li><li>period</li></ul>]


  rcl_publisher_init <--> node_handle
  node_handle <--> rcl_node_init
  rcl_subscription_init <--> node_handle
  subscription_handle <--> rcl_subscription_init
  rclcpp_subscription_init <--> subscription_handle

  publisher_handle <--> rcl_publisher_init

  subscription <--> rclcpp_subscription_init
  rclcpp_subscription_callback_added <--> subscription
  callback <--> rclcpp_subscription_callback_added

  rcl_timer_init <--> timer_handle
  rclcpp_timer_callback_added <--> timer_handle
  callback <-->  rclcpp_timer_callback_added
  timer_handle <--> rclcpp_timer_link_node
  rclcpp_timer_link_node <--> node_handle

  callback <--> rclcpp_callback_register
  rmw_implementation

```

If a certain of address of callback instance is unique, scanning the shared identification including `timer_callback_added` lets you identify a node to which the callback belongs.
On the other hand, if `node_handle` is identified uniquely, callback in the node is identified as well.

### Tracepoints for representing structure of executor and callback group

```mermaid

graph RL
  subscription_handle[[subscription_handle]]
  executor_addr[[executor_addr]]
  entities_collector_addr[[entities_collector_addr]]
  timer_handle[[timer_handle]]
  client_handle[[client_handle]]
  service_handle[[service_handle]]
  callback_group_addr[[callback_group_addr]]

  construct_executor[construct_executor<ul><li>executor_addr</li><li>executor_type_name</li></ul>]

  construct_static_executor[construct_static_executor<ul><li>executor_addr</li><li>executor_type_name</li><li>entities_collector_addr</li></ul>]

  add_callback_group[add_callback_group<ul><li>executor_addr</li><li>callback_group_addr</li><li>group_type_name</li></ul>]

  add_callback_group_static_executor[add_callback_group_static_executor<ul><li>entities_collector_addr</li><li>callback_group_addr</li><li>group_type_name</li></ul>]

  callback_group_add_timer[callback_group_add_timer<ul><li>callback_group_addr</li><li>timer_handle</li></ul>]

  callback_group_add_subscription[callback_group_add_subscription<ul><li>callback_group_addr</li><li>subscription_handle</li></ul>]

  callback_group_add_service[callback_group_add_service<ul><li>callback_group_addr</li><li>service_handle</li></ul>]


  callback_group_add_client[callback_group_add_client<ul><li>callback_group_addr</li><li>client_handle</li></ul>]

  executor_addr <--> construct_executor
  entities_collector_addr <--> construct_static_executor
  add_callback_group <--> executor_addr
  callback_group_addr <--> add_callback_group

  add_callback_group_static_executor <--> entities_collector_addr
  callback_group_addr <--> add_callback_group_static_executor

  callback_group_add_timer <--> callback_group_addr
  timer_handle <--> callback_group_add_timer

  callback_group_add_subscription <--> callback_group_addr
  subscription_handle <--> callback_group_add_subscription

  callback_group_add_service <--> callback_group_addr
  service_handle <--> callback_group_add_service

  callback_group_add_client <--> callback_group_addr
  client_handle <--> callback_group_add_client
```

A handler such as `timer_handle` and `subscription_handle` are assigned to a callback group. A callback group belongs to an executor.

### ros2:rcl_init

[Original tracepoints]

Sampled items

- void \* context_handle

---

### ros2:rcl_node_init

[Original tracepoints]

Sampled items

- void \* node_handle
- void \* rmw_handle
- char \* node_name
- char \* node_namespace

---

### ros2:rcl_publisher_init

[Original tracepoints]

Sampled items

- void \* publisher_handle
- void \* node_handle
- void \* rmw_publisher_handle
- char \* topic_name
- size_t queue_depth

---

### ros2:rcl_subscription_init

[Original tracepoints]

Sampled items

- void \* subscription_handle
- void \* node_handle
- void \* rmw_subscription_handle
- char \* topic_name
- size_t queue_depth

---

### ros2:rclcpp_subscription_init

[Original tracepoints]

Sampled items

- void \* subscription_handle
- void \* subscription

---

### ros2:rclcpp_subscription_callback_added

[Original tracepoints]

Sampled items

- void \* subscription
- void \* callback

---

### ros2:rcl_timer_init

[Original tracepoints]

Sampled items

- void \* timer_handle
- int64_t period

---

### ros2:rclcpp_timer_callback_added

[Original tracepoints]

Sampled items

- void \* timer_handle
- void \* callback

---

### ros2:rclcpp_timer_link_node

[Original tracepoints]

Sampled items

- void \* timer_handle
- void \* node_handle

---

### ros2:rclcpp_callback_register

[Original tracepoints]

Sampled items

- void \* callback
- char \* function_symbol

---

### ros2_caret:rmw_implementation

[Hooked tracepoints]

Sampled items

- char \* rmw_impl

---

### ros2_caret:construct_executor

[Hooked tracepoints]

Sampled items

- void \* executor_addr
- char \* executor_type_name

---

### ros2_caret:construct_static_executor

[Hooked tracepoints]

Sampled items

- void \* executor_addr
- void \* entities_collector_addr
- char \* executor_type_name

---

### ros2_caret:add_callback_group

[Hooked tracepoints]

Sampled items

- void \* executor_addr
- void \* callback_group_addr
- char \* group_type_name

---

### ros2_caret:add_callback_group_static_executor

[Hooked tracepoints]

Sampled items

- void \* entities_collector_addr
- void \* callback_group_addr
- char \* group_type_name

---

### ros2_caret:callback_group_add_timer

[Hooked tracepoints]

Sampled items

- void \* callback_group_addr
- void \* timer_handle

---

### ros2_caret:callback_group_add_subscription

[Hooked tracepoints]

Sampled items

- void \* callback_group_addr
- void \* subscription_handle

---

### ros2_caret:callback_group_add_service

[Hooked tracepoints]

Sampled items

- void \* callback_group_addr
- void \* service_handle

---

### ros2_caret:callback_group_add_client

[Hooked tracepoints]

Sampled items

- void \* callback_group_addr
- void \* client_handle
