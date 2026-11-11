# 页面标记片段

每种页面都是 `<section class="slide …" style="--p:N%">` 里套一层 `<div class="wrap">`。
`.wrap` 负责内边距内的 flex 布局，`.rail` 是左侧那根竖线（可选，深色封面不加）。

---

## 封面 `.slide.dark`

```html
<section class="slide dark" style="--p:0%">
  <div class="glow"></div>
  <div class="cover-art"><svg viewBox="0 0 1600 900" preserveAspectRatio="none">
    <path class="stream" d="M0 32 L800 36 L1600 31" style="opacity:.23"/>
  </svg></div>
  <div class="wrap">
    <div class="cgrid">
      <div class="cbody">
        <div class="kicker rv" style="--i:0">组会文献速递 · GROUP MEETING DIGEST</div>
        <h1 class="rv" style="--i:1">Journal Name</h1>
        <div class="csub rv" style="--i:2">本期主题 · 十篇选读</div>
        <div class="jmeta rv" style="--i:2">Publisher　·　ISSN 0000-0000　·　Volume 00　·　2026</div>
        <p class="about rv" style="--i:3">3 行以内的期刊范围说明。</p>
      </div>
      <div class="cside rv" style="--i:3">
        <figure class="cplate" style="--ac:#8FD3E8;--tint:.55">
          <svg class="pl-art" viewBox="0 0 300 400" preserveAspectRatio="none" aria-hidden="true">
            <path class="stream" d="M-20 300 C 60 250, 120 340, 200 280 S 300 220, 330 250" style="opacity:.55"/>
          </svg>
          <div class="pl-tint"></div>
          <div class="pl-glass"></div>
          <div class="pl-mast"><b>JOURNAL NAME</b><i>2026 · 00</i></div>
          <div class="pl-foot"><span>Publisher</span><em>example.com</em></div>
        </figure>
        <p class="pl-cap">Publisher　·　Vol. 00　·　2026</p>
      </div>
    </div>
    <div class="hint rv" style="--i:5"><b>↓ / →</b> 翻页　　<b>F</b> 全屏</div>
  </div>
</section>
```

**放真期刊封面**：`figure` 加 `class="cplate has-photo"`，把上面 `.pl-art/.pl-tint/.pl-glass/.pl-mast/.pl-foot` 换成一张图：

```html
<figure class="cplate has-photo">
  <img class="pl-photo" alt="" src="data:image/jpeg;base64,…">
</figure>
```

照片宽 720px、JPEG q85 即可；`has-photo` 会自动把下缘渐隐掉，与版面融在一起。

**没有图就用官方外壳**：`--ac` 是线条色，`--tint` 是叠色强度（0–1），`.pl-mast` 顶栏、`.pl-foot` 底栏，改文字即可，不需要任何图片。

---

## 目录 `.slide`

```html
<section class="slide" style="--p:20%">
  <div class="rail"></div>
  <div class="wrap">
    <div class="runhead rv" style="--i:0"><span>JOURNAL · VOL. 00 · 2026 · 十篇</span><span>CONTENTS</span></div>
    <div class="ov">
      <div class="row rv" style="--i:1">
        <i>01</i>
        <div class="rt"><p>中文标题</p><em>English Title</em></div>
        <s>主题标签</s>
      </div>
    </div>
  </div>
</section>
```

行数随意，`.ov` 会在可用高度里均分；超过 10 行会挤，建议一页 8–10 行。`<s>` 是右侧标签列，宽度固定，长标签会被截断，控制在 6 字内。

---

## 论文页 `.slide.paper`（核心页）

```html
<section class="slide paper" style="--p:40%">
  <div class="rail"></div>
  <div class="wrap">
    <div class="runhead rv" style="--i:0">
      <span>JOURNAL · VOL. 00 · 2026</span><span>SELECTED · 01</span>
    </div>
    <div class="pgrid">
      <div class="col-l">
        <span class="topic rv" style="--i:0">主题标签</span>
        <div class="num rv" style="--i:0"><b>01</b><span>/10</span></div>
        <h3 class="zh rv" style="--i:1">中文标题</h3>
        <p class="en rv" style="--i:1">English Title</p>
        <div class="au rv" style="--i:2"><b>Zhang Wei（张伟）, Li Ming（李明）, J. Smith</b><br>Journal, 2026, 00(0): e00000</div>
        <div class="klabel rv" style="--i:2">关键词 · Keywords</div>
        <div class="kws rv" style="--i:3">
          <span class="kw">中文词<i>english term</i></span>
        </div>
        <div class="note rv" style="--i:4"><em>推荐理由</em>为什么值得看。</div>
      </div>
      <div class="col-r">
        <div class="lbl rv" style="--i:0">摘要 · Abstract</div>
        <div class="abswrap"><p class="abs rv" style="--i:1">摘要转述 200–450 字。</p></div>
        <div class="doi rv" style="--i:2">DOI · 10.0000/example.00000</div>
        <div class="aff rv" style="--i:3"><i>机构</i>示例大学（Example University）；中国科学院（Chinese Academy of Sciences）</div>
      </div>
    </div>
  </div>
</section>
```

要点：

- `.num b` 是大号序号（`clamp(46px,4.9vw,182px)`），`span` 是 `/10` 的页码。
- `.klabel` 有 `margin-top:auto`，关键词会被压到左栏底部，和右栏的 DOI 对齐成一条水平基线——**这是版式的一部分，不要删**。
- `.note` 左侧那根青色竖线由 `border-left:2px solid var(--cyan)` 给出。
- `.abs` 由脚本定字号；`.abswrap` 必须闭合。
- **`.aff` 是机构行，放在 `.doi` 之后、是右栏最后一个元素**。`.doi` 靠 `margin-top:auto` 压到底，`.aff` 跟在它下面，形成"DOI 细线 + 机构"的收尾。格式固定为 `中文译名（English original）`，多个机构用 `；` 分隔，最多 6 个。
- **作者用原文拼写**，不要音译猜测；只在能核实中文名时用 `Zhang Wei（张伟）` 的写法补注。海外作者不补中文。原文只有首字母缩写（`J. Smith`）就照原样抄。
- 中文标题超过 2 行会让左栏变挤，超过 3 行基本放不下——控制在 39 字内。

---

## 三点观察 `.slide.dark`

```html
<section class="slide dark" style="--p:80%">
  <div class="glow"></div>
  <div class="rail"></div>
  <div class="wrap">
    <div class="cmain">
      <div class="kicker rv" style="--i:0">SUMMARY</div>
      <h2 class="big rv" style="--i:1">三点观察</h2>
      <ul class="cl">
        <li class="rv" style="--i:1"><i>01</i><p>第一条判断。</p></li>
        <li class="rv" style="--i:2"><i>02</i><p>第二条。</p></li>
        <li class="rv" style="--i:3"><i>03</i><p>第三条。</p></li>
      </ul>
    </div>
    <div class="cfoot rv" style="--i:4">Journal　·　Volume　·　本期主题</div>
  </div>
</section>
```

每条 40–70 字；超过三行会越出 `.cmain` 的居中区块。

---

## 参考文献 `.slide.refs`

```html
<section class="slide refs" style="--p:100%">
  <div class="rail"></div>
  <div class="wrap">
    <div class="runhead rv" style="--i:0"><span>JOURNAL · VOL. 00 · 2026 · 参考文献</span><span>REFERENCES · 10</span></div>
    <h2 class="refhead rv" style="--i:1">参考文献</h2>
    <div class="reflist">
      <div class="ref rv" style="--i:1">
        <b>01</b>
        <div><p>Author A, Author B. Title[J]. Journal, 2026, 00(0): e00000.</p>
          <a href="https://doi.org/10.0000/example.00000" target="_blank" rel="noopener">10.0000/example.00000</a></div>
      </div>
    </div>
  </div>
</section>
```

`.reflist` 是两列自动流式排布（`columns`），条目数会自己平衡；最多两列放得下 10–12 条。

---

## 双 deck（两本期刊）

左侧那排 `.jsw` 按钮切换两个 `.deck`：

```html
<div class="jsw">
  <button class="on" data-k="we" onclick="switchDeck('we');this.blur()">中文标签</button>
  <button data-k="ae" onclick="switchDeck('ae');this.blur()">另一本</button>
</div>
<nav class="dots" data-k="we"></nav>   <!-- 每个 deck 一套 dots -->
<nav class="dots" data-k="ae" hidden></nav>
<div class="deck" data-k="we"> …14 页… </div>
<div class="deck" data-k="ae" hidden> …14 页… </div>
```

`switchDeck(k)` 会做：淡出 → 换 deck → 瞬时定位到封面 → 重排摘要字号 → 纵向漂移淡入，全程约 1.2s 锁，防止连点。没有 `.jsw` 时脚本自动跳过，单 deck 不用管它。
