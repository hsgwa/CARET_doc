Design describes the internals of CARET.
Following figure shows the tracing flow with each package added.

![architecture](../imgs/design.drawio.png)

CARET records data, which include timestamps, from tracepoints embedded in user application, ROS 2 and DDS.
The data are stored as "Trace Data".
CARET analyzes the Trace Data and provides the results to the developer.

This design chapter describes policies and internal processes for each steps listed below.

1. Recording
1. [Runtime processing](./runtime_processing/)
1. [Tracepoints](./trace_points/)
1. Analyzing
1. [Processing trace data](./processing_trace_data/)
1. [Latency calculation](./latency_definitions/)
1. [Visualization](./visualizations/)

In addition, the followings are explained.

- [Software architecture](./software_architecture/)
- [Architecture file](./architecture_file/)
- [Latency definition](./latency_definitions/)
- [Limits and constraints](./latency_definitions/)

See also

- [CARET analyze API document](https://tier4.github.io/CARET_analyze/)
