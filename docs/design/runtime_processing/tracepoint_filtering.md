# Tracepoint filtering

When measuring a system composed of many nodes, such as Autoware, the amount of data from tracing can be very large.

Lttng is handled in Discard mode to minimize the impact on the system.
Therefore, when there is a large amount of trace data, trace data discarding occurs.

CARET provides the ability to disable trace points associated with specific topics or nodes.
This makes it possible to exclude from recording only trace data related to rviz nodes or tf topics, allowing CARET measurements even on large systems.

This filtering function looks at the instance addresses of callback and publisher to see if they are included in the filter.
This check is O1 fast because it uses std::unordered_map.

```cpp
void ros_trace_callback_start(const void * callback, bool is_intra_process) {
  static auto & controller = Singleton<TracingController>::get_instance();

  // Record trace data only if current callback is allowed to record
  if (controller.is_allowed_callback(callback)) {
    ORIG_FUNC::ros_trace_callback_start(callback, is_intra_process);
  }
}
```

See also

- [caret_trace](../software_architecture/caret_trace)
- [Tracepoint](../../trace_points/)
- [Recording trace filtering](../../recording/trace_filtering)
