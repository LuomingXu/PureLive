# J04.2 记录

## 2026-10-24

- 用户确认方案：不做后台；应用运行时检查；成功后应用内通知；失败本次运行首次弹一次；设置随备份同步；远程目录默认 `purelive`。
- 已写：
  - 6 个设置和默认值测试条目；
  - `autoBackupFileName` / `isAutoBackupFile`；
  - `exportBackup(includeSensitiveData:)`；
  - `WebDavClient.ensureFolder`；
  - `WebDavAutoBackupService`、`WebDavAutoBackupScheduler`；
  - `AppStartup` 主窗口启动；
  - 服务、调度、对话框测试和 FakeDav 的 MKCOL。
- 未完成：
  - 当前环境没有 Flutter 可执行文件，未运行 `flutter test` 和 `tools/gate/gate.sh --all`。
  - 未在真机或本机 WebDAV 上验证。
- 已验证：
  - 用 Dart 3.13.5 对改动文件做了 120 列格式化检查，稳定无变化。
  - `python3 tools/docs/docs.py --check`、`python3 tools/docs/owners.py --check` 通过。
  - GitHub Actions `Test` 运行 [40103225026](https://github.com/LuomingXu/PureLive/actions/runs/40103225026) 通过：定向 WebDAV/备份/i18n 测试和 `tools/gate/gate.sh --all` 都通过，日志结尾 `gate: passed (all, 14 members)`。
- CI 期间修正的既有问题：
  - `chat_line_roles_test.dart`、`live_danmaku/test/sites/bilibili_test.dart` 未格式化；
  - 备份列表测试依赖文件 mtime，Linux 上同毫秒写入导致“新到旧”排序不稳定，改成显式设置 mtime；
  - 新代码的 analyzer infos 和 i18n 运行时 key 模板白名单。
- 下一步：维护者在 K90/Windows 上按 README 做真机/桌面验证（目录创建、成功通知、保留清理、后台暂停/恢复、第二窗口只上传一次）。
