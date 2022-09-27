Some tracepoints share a same identification, such as a node ID (`node_handle`) and an address of callback instance. The shared identification associates each tracepoint to another, the association constructs structure of tracepoint relationship. The following figures show four structures.

### Tracepoints for representing structure of a node

```mermaid
erDiagram
 rcl_init{
 address context_handle
 }

 rcl_node_init{
 address node_handle
 address rmw_handle
 string node_name
 string node_namespace
 }

 rcl_publisher_init{
 address publisher_handle
 address node_handle
 address rmw_publisher_handle
 string topic_name
 size_t queue_depth
 }

 rcl_subscription_init{
 address subscription_handle
 address node_handle
 address rmw_subscription_handle
 string topic_name
 size_t queue_depth
 }

 rclcpp_subscription_init{
 address subscription_handle
 address subscription
 }

 rclcpp_subscription_callback_added{
 address subscription
 address callback
 }

 rcl_timer_init{
 address timer_handle
 int64_t period
 }

 rclcpp_timer_callback_added{
 address timer_handle
 address callback
 }

 rclcpp_timer_link_node{
 address timer_handle
 address node_handle
 }

 rclcpp_callback_register{
 address callback
 string function_symbol
 }

 rmw_implementation{
 string rmw_impl
 }

    rcl_node_init ||--|| rcl_publisher_init : node_handle
    rcl_node_init ||--|| rcl_subscription_init : node_handle
    rcl_node_init ||--|| rclcpp_timer_link_node : node_handle

    rcl_node_init ||--|| rcl_publisher_init : node_handle
    rcl_node_init ||--|| rcl_subscription_init : node_handle
    rcl_node_init ||--|| rclcpp_timer_link_node : node_handle


    rcl_publisher_init ||--|| PUBLISHER_HANDLE : node_handle
    rcl_subscription_init ||--|| SUBSCRIPTION_HANDLE : node_handle
    rcl_timer_init ||--|| TIMER_HANDLE : node_handle

    rcl_subscription_init ||--|| rclcpp_subscription_init : subscription_handle
    rclcpp_subscription_init ||--|| rclcpp_subscription_callback_added: subscription

    rclcpp_timer_callback_added ||--|| rclcpp_callback_register: callback
    rclcpp_subscription_callback_added ||--|| rclcpp_callback_register: callback

    rclcpp_timer_callback_added ||--|| rcl_timer_init : timer_handle
    rclcpp_timer_link_node ||--|| rcl_timer_init: timer_handle









```

```mermaid
erDiagram
 construct_executor{
 address executor_addr
 string executor_type_name
 }

 construct_static_executor{
 address executor_addr
 address entities_collector_addr
 string executor_type_name
 }

 add_callback_group{
 address executor_addr
 address callback_group_addr
 string group_type_name
 }

 add_callback_group_static_executor{
 address entities_collector_addr
 address callback_group_addr
 string group_type_name
 }

 callback_group_add_timer{
 address callback_group_addr
 address timer_handle
 }

 callback_group_add_subscription{
 address callback_group_addr
 address subscription_handle
 }

 callback_group_add_service{
 address callback_group_addr
 address service_handle
 }

 callback_group_add_client{
 address callback_group_addr
 address client_handle
 }


 construct_executor ||--|| add_callback_group : executor_addr
 construct_static_executor ||--|| add_callback_group_static_executor : entities_collector_addr

    add_callback_group_static_executor ||--|| callback_group_add_timer : callback_group_addr
    add_callback_group_static_executor ||--|| callback_group_add_subscription : callback_group_addr
    add_callback_group_static_executor ||--|| callback_group_add_service : callback_group_addr
    add_callback_group_static_executor ||--|| callback_group_add_client : callback_group_addr
    add_callback_group ||--|| callback_group_add_timer : callback_group_addr
    add_callback_group ||--|| callback_group_add_subscription : callback_group_addr
    add_callback_group ||--|| callback_group_add_service : callback_group_addr
    add_callback_group ||--|| callback_group_add_client : callback_group_addr

     callback_group_add_timer ||--|| TIMER_HANDLE : callback_group_addr
    callback_group_add_subscription ||--|| SUBSCRIPTION_HANDLE : callback_group_addr
    callback_group_add_service ||--|| SERVICE_HANDLE : callback_group_addr
    callback_group_add_client ||--|| CLIENT_HANDLE : callback_group_addr


```

If a certain of address of callback instance is unique, scanning the shared identification including `timer_callback_added` lets you identify a node to which the callback belongs.
On the other hand, if `node_handle` is identified uniquely, callback in the node is identified as well.

### Tracepoints for representing structure of executor and callback group

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