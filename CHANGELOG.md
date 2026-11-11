# Changelog

遵循 [Keep a Changelog](https://keepachangelog.com/zh-CN/1.1.0/)；日期为本地时间。

## [Unreleased] — 2026-11-11

实测来源：用本模板产出的一份 28 页双 deck 汇报稿（RSER 14 页 + Applied Energy 14 页，1920×1080）在 Chrome 125 下逐页回归，发现两处只在"多 deck + 深链"场景才暴露的问题。

### Fixed

- **切换 deck 后摘要字号可能漏测**：`switchDeck()` 原本只在过渡结束的下一帧调一次 `fitAbs()`；若此时字体或封面图仍在加载，量到的 `clientHeight` 偏小，摘要会被永久压到 18px，且之后不再自动纠正。现在过渡结束后 **420ms 再复测一次**（`fitAbs/fitLeft/sync`），覆盖字体替换、图片解码晚于首帧的情况。
  - 复现条件：首次切到第二个 deck，且该 deck 的 `.abs` 所在栏位高度依赖尚未加载完的字体。
  - 验证：切到第二个 deck 后 3s 读取 10 页 `.abs` 的 `fontSize`，均为 22–30px 的自适应值，无一停留在 18/20px。

- **深链 `#N` 无法跳到第二个 deck**：`go(h-1)` 里的 `h` 是跨 deck 的全局页号，但 `slides` 只指向当前 deck，`go()` 内部又用 `Math.min(n-1,i)` 截断，导致 `#15`–`#28` 全部落回第一套的第 14 页。现在检测到 `h > slides.length` 时先 `switchDeck()` 切到另一个 deck，再按偏移 `h-1-本deck页数` 跳页。
  - 验证：`file://…html#20` 加载后 `deck=ae`，正确落到第二套第 6 页。

### Added

- `SKILL.md` 新增「多 deck（两本期刊 / 两套选读）」一节：容器结构、三处同步按 deck 各自计算、`switchDeck` 的职责、深链跨 deck 规则、以及"隐藏 deck 里 `.abswrap` 量不到是设计而非 bug"的说明。此前 `template.html` 的注释引用了这一节，但正文缺失。
- 本 `CHANGELOG.md`。

### Not changed

- `fitAbs()` 的阶梯（31→18px、步长 0.5、行高 1.70/1.80/1.90、兜底 18px/1.86）保持原样。曾有需求希望"减少字号档位"改成 24→18 步长 2，实测会在长摘要页底部留出约 300px 空白，与「摘要填满右栏」的目标相悖，故不采纳。
- 页面骨架、CSS、动效参数、`references/*` 均未改动。
