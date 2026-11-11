---
name: group-paper-deck
description: 组会论文汇报 HTML 演示稿。把"一本期刊的十篇选读"做成 16:9 整屏翻页的网页 PPT：深色封面 + 目录 + 每篇一页（中文标题/作者出处/关键词/推荐理由/摘要/DOI）+ 三点观察 + 参考文献，带入场动效、纵向翻页吸附、滚动进度条与阅读轨道。Use when the user asks for 组会汇报、文献汇报、论文汇报、期刊速递、journal club、paper digest、文献综述 PPT、"把这几篇论文做成 PPT"，or any multi-slide academic reading report that should be a single portable HTML file. Triggers include 组会、文献、论文、期刊、精选、选读、汇报、演示稿、slides、deck、ppt.
---

# 组会论文汇报 HTML 演示稿

一份可直接双开的单文件网页 PPT：**一个 HTML 文件 = 整套汇报**，键盘翻页、滚动吸附、入场动效、深链跳页。版式是"期刊速递"式的：左栏是书目信息与推荐理由，右栏是摘要，读起来像杂志内页而不是 PPT。

## 何时用

- 用户要把若干篇论文/一期期刊做成组会汇报、文献速递、reading group 分享
- 用户已有讲稿式的文字（标题、作者、摘要、推荐理由），需要套成成套演示稿
- 用户明确要求"网页 PPT / HTML 幻灯片 / 能直接打开不用装软件"

不适合：需要 .pptx 交付物（另走 pptx 技能）、需要大量图表排版（另走通用 deck 技能）。

## 快速开始

```bash
cp assets/template.html 我的汇报.html
# 打开改文字即可；无构建、无依赖、离线可用
```

模板自带 6 页骨架：封面 / 目录 / 论文页 ×2 / 三点观察 / 参考文献。**论文页整段复制**即可扩到 10 篇。

## 文件

- `assets/template.html` — 唯一交付物，6 页骨架 + 全套 CSS/JS（约 40 KB，无外链）
- `references/markup.md` — 每种页面的标记片段，写新页时直接抄
- `references/motion.md` — 动效规格与调参表

## 硬规矩（改之前先读）

1. **只让人改文案，别动版式。** 字号全部是 `clamp()`，跟着视口走；改 padding/字号会立刻破坏整册的一致性。
2. **每页一个 `<section class="slide">`**，`style="--p:N%"` 是右侧阅读轨道的高度百分比，按页序算：第 i 页（从 0 起）填 `i/(总页数-1)*100`。
3. **页数与三处同步**：`.dots` 的 `<a>` 个数、`.pg` 的 `NN / NN` 文本、`--p` 百分比。加页必须三处一起改。
4. **摘要不要手动调字号**：`fitAbs()` 从 31px 往下逐档试探，取第一个装得下的字号（18–31px），让 `.abs` 刚好填满右栏。不要自己加 `font-size`，也不要改回二分查找——早期布局未稳定时二分容易误判，之后不再恢复，右栏底部就会空出一大片。摘要写 200–450 字；压到 18px 仍溢出，就是该删句子了。
5. **`.abswrap` 必须闭合**：`<div class="abswrap"><p class="abs">…</p></div>` 漏掉 `</div>`，DOI 会被挤进 flex 行里变成竖排窄条。
6. **图片内联**：封面照用 base64（宽 720px、JPEG q85 足够），保证单文件可拷走。
7. **减少动态**已内置：`prefers-reduced-motion` 下入场/浮动/换页动画全部关闭，不要另加一套。
8. **每页必须写作者与机构**（`.au` + `.aff`），这是 2026-11 之后的新要求。机构写成 `中文译名（English original）`，多个用 `；` 分隔；作者保留原文拼写，只在能核实中文名时写成 `Zhang Wei（张伟）`，**绝不音译猜名**。机构拿不到就写 `原文未列明`，不要编。

## 页面类型速查

完整片段见 `references/markup.md`。骨架：

```html
<section class="slide dark" style="--p:0%">    <!-- 封面：.cgrid 左文右图 -->
<section class="slide" style="--p:20%">         <!-- 目录：.ov > .row -->
<section class="slide paper" style="--p:40%">   <!-- 论文页：.pgrid 左 .col-l 右 .col-r -->
<section class="slide dark" style="--p:80%">    <!-- 三点观察：.cmain + .cfoot -->
<section class="slide refs" style="--p:100%">   <!-- 参考文献：.reflist > .ref -->
```

论文页左栏顺序固定：`.topic`（主题胶囊）→ `.num`（01/10）→ `h3.zh` → `p.en` → `.au`（作者 + 期刊卷期）→ `.klabel` → `.kws` → `.note`（推荐理由）；右栏：`.lbl` → `.abswrap > p.abs` → `.doi` → `.aff`（机构，右栏最后一格）。

## 多 deck（两本期刊 / 两套选读）

一个文件装两套汇报时，用 `.deck` 分容器：

```html
<div class="deck" data-k="we"> …14 页… </div>
<div class="deck" data-k="ae" hidden> …14 页… </div>
<nav class="dots" data-k="we">…</nav>
<nav class="dots" data-k="ae" hidden>…</nav>
<div class="jsw"><button data-k="we" class="on">RSER</button><button data-k="ae">APPLIED ENERGY</button></div>
```

要点：

- **三处同步按 deck 各自算**：`--p` 用 `i/(本 deck 页数-1)*100`，`.pg` 写本 deck 的页数，`.dots` 只放本 deck 的锚点。两个 deck 页数不同也没关系。
- **切换靠 `switchDeck(k)`**：切隐藏、恢复该 deck 上次停留页、重跑 `fitAbs/fitLeft/sync`；过渡结束后 420ms 再量一次（字体/封面图此时才完成布局）。
- **摘要字号在隐藏 deck 里量不到**：`display:none` 的 deck 内 `.abswrap` 的 `clientHeight` 是 0，`fitAbs()` 会 `return` 等下一次——所以必须由 `switchDeck` 触发，这不是 bug。
- **深链跨 deck**：`#1`–`#14` 落在第一个 deck；`#15` 起自动切到第二个 deck 再跳（第 15 页 = 第二套的第 1 页）。加第三个 deck 时页数累计即可，不用改代码。

## 动效

入场用 `.rv` + `--i` 序号（`--i` 越大越晚，`min(…*68ms, 520ms)` 封顶，12 个元素也不会拖过 0.5s）。层级越大位移越大：`h1` 26px、`h2.big` 22px、正文 18px、标签 9px。

细节见 `references/motion.md`，三条不要碰：

- `.slide.in .rv` 的归零规则（`sync()` 靠它做单向"滚过就显示"）
- `swIn` 关键帧（换 deck 的纵向漂移）
- `progTo/progStep` 的 rAF 插值（进度条平滑靠它）

## 自检

```bash
# 单页截图（绕开滚动吸附，一次看一页）
python - <<'PY'
import re,io
s=open('我的汇报.html',encoding='utf-8').read()
for i in (1,3,5):
    inj=('<style>.slide{display:none!important}.slide.stay{display:flex!important}</style>'
         '<script>addEventListener("load",function(){var a=document.querySelector(".deck")'
         '.querySelectorAll(".slide");[].forEach.call(a,function(e){e.classList.add("in")});'
         'a[%d].classList.add("stay")});</script></body>'%(i-1))
    open('p%d.html'%i,'w',encoding='utf-8').write(s.replace('</body>',inj))
PY
chrome --headless=new --window-size=1600,900 --virtual-time-budget=3000 \
       --screenshot=cover.png "file:///$PWD/p1.html"
```

检查清单：封面图不糊、目录 10 行不折行、每页标题不超三行、关键词不溢出左栏、摘要底部与 DOI 之间无夹层、深色页文字与底色对比够、末页 DOI 链接可点。

## 交付

给用户 `xxx.html` 单文件 + 2–3 张截图（封面 / 一篇论文页 / 深色页）。告诉他们：双击打开、`F` 全屏、`↑↓/→` 翻页、`#7` 直跳第 7 页。
