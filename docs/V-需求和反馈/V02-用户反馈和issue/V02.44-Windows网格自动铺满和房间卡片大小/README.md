# V02.44 Windows 网格自动铺满和房间卡片大小

- 编号、状态、档位、规模：见[登记表](../../../tasks.toml)和 [STATUS.md](../../../STATUS.md)
- 类型：功能
- 来源：用户反馈（2026-10-25）：Windows 上窗口大小变化后每页数量不跟着铺满，且不能调整房间卡片大小
- 相关：决定 D-058；分页（U.4b）、房间卡片（A09.1）、动态适配（A04.1）

## 目标

Windows/Linux/macOS 的编号翻页保留，但“自动”每页数量改为覆盖当前窗口可用高度的**行 × 列**：最后一行可以只露出一部分，页面可小幅滚动，不留底部空白；放大窗口后继续加载补足，缩小后按新容量重算。手动选 20/40/60/80 时仍固定。“房间卡片设置 → 桌面端 → 外观”里新增“房间卡片大小”六档（XS / S / M / L / XL / XXL），改变最小卡宽和列数上限；所有房间网格共用，只在桌面端目标显示、在桌面系统生效，默认 M 与改动前完全一致。

## 3.x 和现状

| 方面 | 3.x | 现在（改动前） | 要做到 |
|---|---|---|---|
| 每页数量 | 宽于 960 固定 20，否则 12；设置 0 也只是回落 | `pageSizesOf` 把 0 回落成 20/12，`RoomFeedView` 在桌面直接跳过视口补足 | 0 = 按窗口铺满；窗口变化重算并补足 |
| 翻页条 | 数字菜单 | 只有数字，无法看出“自动” | 菜单增加“自动”，当前值显示“自动” |
| 卡片大小 | 固定断点，拖窗口忽大忽小 | 最小卡宽 160/180/200，最多 8 列，无用户设置 | 桌面六档预设；手机保持 M |

## 方案 / 结果

- c1 分页模型（`shared/rooms/paging.dart`）：`autoPageSize = 0`；`pageSizesOf` 返回 `auto` 并保留不在选项里的已存值；`PageFillScheduler` 把布局测到的容量在帧后应用；`PaginationBar` 增加 `auto` / `allowAuto` 和“自动”菜单项。
- c2 热门和分区房间（`shared/rooms/room_grid.dart`）：新增 `pageFillCount`（覆盖可用高度、最后一行可部分显示、最多 20 行）和 `RoomGridGeometry.ofGrid`；`_RoomFeedViewState` 记录自动容量、按页首卡片锚定、`_fillAutoPage` 在有进展时继续加载；`PopularCatalog.pageSize` 允许 0。
- c3 关注页（`features/favorite/favorite_page.dart`）：网格和离线条目都在 `Expanded` 内测量高度；数据已在内存，只重算切片；翻页条同样支持“自动”。
- c4 卡片大小设置：`Settings.roomCardSize`（字符串，默认 `standard` = M）；`RoomCardSize` 六档 XS/S/M/L/XL/XXL（0.8×/12 列、0.9×/10、1×/8、1.25×/8、1.5×/8、2×/8）；`watchRoomCardSize` 在手机端始终返回 M；选项在“房间卡片设置 → 桌面端 → 外观”，移动端目标不显示；只在桌面系统生效。
- c5 文案：15 种语言新增 8 个键（标题、说明和 XS/S/M/L/XL/XXL 六个档位），更新“每页默认数量”的说明（自动 = 铺满窗口）。

## 验证

- 自动测试：`test/shared/paging_test.dart`、`test/shared/card_size_test.dart`、`popular_test.dart`、`favorite_test.dart`、`settings_page_test.dart`、`settings_defaults_test.dart`、`i18n_test.dart`。
- 真机：Windows 上缩放窗口看铺满和翻页锚定；六档卡片大小；关注页、搜索、历史；Android 上没有该设置且布局不变。自动测试和门禁由 CI 跑，真机待验收。

## 留下的问题

- 分区卡片页（`platform_areas_view.dart`）不是房间卡片，未应用自动铺满和卡片大小。
- 电视用自己的网格，不受影响。
- `isPhoneDevice` 仍用 `Platform.isAndroid/isIOS`，Widget 测试无法覆盖 Android 的运行时门槛，只能由目录的 `_desktop` 覆盖显示层。
