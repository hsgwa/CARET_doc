# Callback

$$
l^{cb} = t^{cb}_{end} - t^{cb}_{start}
$$

コールバックの開始時刻から終了時刻までの時間差です。

関連トレースポイント

- [callback_start](../../trace_points/runtime_trace_points/#ros2callback_start)
- [callback_end](../../trace_points/runtime_trace_points/#ros2callback_start)

<prettier-ignore-start>
!!! todo
        コールバックはエグゼキュータによりスケジューリングされる「タスク」です。
        エグゼキュータのスケジューリングには左右されないレイテンシです。
<prettier-ignore-end>
