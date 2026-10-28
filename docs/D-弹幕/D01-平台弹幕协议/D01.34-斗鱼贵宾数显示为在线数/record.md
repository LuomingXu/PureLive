# D01.34 记录

## 做了什么

- `packages/live_danmaku/lib/src/sites/douyu.dart`：`_vip` 改为 `_online`，`oni` 的 `vn` 报成 `LiveMessageType.online` + `LiveAudienceUpdate(kind: onlineViewers)`，不再生成“贵宾”聊天行；非数字不报。
- `packages/live_danmaku/test/douyu_test.dart`：协议用例改为断言在线数；S13 录制期望里的 6 条“贵宾”聊天行按新行为变换；S14“其他包”用例的期望同步；`_project` 把 `LiveAudienceUpdate` 展开成 `{kind, value}` 便于对照。

## 结果

- 当前环境没有 Flutter/Dart（PATH 里的 `D:\flutter\bin` 不存在），未运行 `dart test` 和门禁；待有工具链的环境运行后补测试数量和结果。
- 真机未看：要在 K90 上进一个斗鱼直播间，确认聊天列表没有“贵宾”行、观众栏出现“在线 N”。

## 停在哪

代码和测试已改完，未提交、未跑门禁、未真机。下一步：运行 `packages/live_danmaku` 的测试和 `bash tools/gate/gate.sh --all`，然后真机。
