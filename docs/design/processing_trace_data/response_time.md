# Response time

レスポンスタイムは、システムや装置などに要求や入力が与えてから、反応を送り返すまでにかかるの時間を指します。

autoware を含め、安全に関わるシステムには重要な指標の一つです。
caret は message flow からレイテンシを算出できますが、response time の評価には向きません。

CARET では、message flow を加工して response time を算出しています。
本章では CARET が算出する response time の定義について説明します。

## Deriving response time

CARET におけるレスポンスタイムは、任意の時間に対する入力に対して、最初に出力されるまでの時間として算出します。
