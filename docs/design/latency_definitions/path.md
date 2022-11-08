# Path

Path latency is the sum of node latency and communication latency included in the path.

$$
l_{path} = \sum_{\in path} l_{node} + \sum_{\in path} l_{comm} \\
l_{node} = t_{pub} - t_{sub} \\
l_{comm} = t_{sub} - t_{pub} \\
$$

Here, path is one of the paths chosen from the node graph.

In CARET, the path is defined as `[node_name]-[topic_name]-... -[topic_name]-[node_name]`.  
For example, in the following case, the path definition is `[A]-[/a]-[C]-[/e]-[E]`.

![node path](../../imgs/node_path.drawio.png)

<prettier-ignore-start>
!!! Info
    In the above definition, the start time of the latency is the start node's Publish and the end time of the latency measurement is the end node's Subscribe.
    When trying to measure a actual system, it is desirable to be able to define various start and end times depending on the implementation.
    For example, the start time might be more appropriately a stamp value in the sensor message.
    It's current issue to support for such more precise latency.
<prettier-ignore-end>