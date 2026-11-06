# 动效规格

全套动效只有三类：**入场**（一次性，随滚动触发）、**换 deck**（一次性，纵向漂移）、**常驻**（极慢循环，几乎察觉不到）。没有视差、没有光标跟随、没有逐字打字机。

## 1. 入场

```css
.rv{opacity:0;transform:translate3d(0,18px,0);
  transition:opacity .88s cubic-bezier(.22,1,.36,1),
             transform .88s cubic-bezier(.22,1,.36,1);
  transition-delay:min(calc(var(--i,0)*68ms),520ms)}
.slide.in .rv{opacity:1;transform:none}
```

- `--i` 是元素在**本页内**的顺序（0,1,2,3…），HTML 里手写。
- 68ms × i 封顶 520ms：12 个元素也不会拖过半秒。
- `cubic-bezier(.22,1,.36,1)` 是"快起、长缓"曲线——元素先快速到位，最后 30% 时间里慢慢贴合。

**按层级分位移**（越大的字走得越远，读起来是"落下来"而不是"飘上来"）：

| 选择器 | 位移 | 时长 |
|---|---|---|
| `h1.rv` | 26px | 1.05s |
| `h2.big.rv`、`h2.refhead.rv` | 22px | .98s |
| `.rv`（默认，正文） | 18px | .88s |
| `.ref.rv`、`.ov .row.rv` | 13px | .76s |
| `.runhead` `.kicker` `.lbl` `.klabel` `.cfoot` | 9px | .66s |

小字位移要小：9px 的位移配 26px 的字会很躁，配 11px 的跑马标签才刚好。

触发靠 `sync()`：`slides.forEach((s,i)=>s.classList.toggle('in', i<=cur))`——滚过的页保持 `.in`，往上滚不会重播。这是单向的"读过就留着"，避免回翻时整页重演。

## 2. 换 deck

```css
.slide.sw-out{transform:translate3d(0,-18px,0) scale(.994);opacity:0;
  transition:transform .34s cubic-bezier(.4,0,.72,.22),opacity .3s cubic-bezier(.4,0,.72,.22)}
.slide.sw-in{animation:swIn .54s cubic-bezier(.22,1,.36,1) both}
@keyframes swIn{0%{transform:translate3d(0,22px,0) scale(.994);opacity:0}
  62%{opacity:1}100%{transform:none;opacity:1}}
```

关键：**纵向**。整套稿子本来就是上下滚的，换 deck 若横向滑入会突然出现第二个方向轴。旧的退场往上 18px、新的入场从下 22px 回来，视觉上是"翻过一叠纸"。

`62%` 处就把 opacity 拉满而 transform 还没完——避免新页在最后 38% 的时间里"半透明地滑进来"，那一段会显得脏。

`switchDeck()` 的时序：加 `.sw-out` → 300ms 后换 deck 并瞬时定位 → 加 `.noanim` 让新页的 `.rv` 不做入场 → rAF 内重排字号 → 去掉 `.noanim`、加 `.sw-in` → 580ms 后移除。全程 `switching` 锁 1180ms。

## 3. 常驻

```css
@keyframes glowBreath{0%,100%{opacity:.82;transform:scale(1)}50%{opacity:1;transform:scale(1.035)}}
.glow{animation:glowBreath 19s ease-in-out infinite}

@keyframes plateFloat{0%,100%{transform:translate3d(0,0,0)}50%{transform:translate3d(0,-6px,0)}}
.cplate{animation:plateFloat 14.5s cubic-bezier(.45,0,.55,1) infinite}
```

深色页的环境光晕 19s 呼吸一次，封面那块"刊面"14.5s 浮动 6px。周期都超过 14s：投影时没人会盯着看出循环，但屏幕不会是一张死图。

`.cplate` 加了 `will-change` 之外不要再用 `filter` 动它——那块区域有 `mask-image` 渐隐，滤镜会强制它走慢路径。

## 4. 进度条

```js
function progTo(v){progTarget=v;if(!progRaf)progRaf=requestAnimationFrame(progStep)}
function progStep(){progNow+=(progTarget-progNow)*.18;prog.style.transform='scaleX('+progNow+')';
  if(Math.abs(progTarget-progNow)<.0005){progNow=progTarget;prog.style.transform='scaleX('+progTarget+')';
    progRaf=0;return}progRaf=requestAnimationFrame(progStep)}
```

滚动事件里只写目标值，实际位移由 rAF 逼近，系数 `.18`（约 3 帧到 50%）。原来直接绑 `scrollY` 的写法在触控板上抖，且和 `scroll-snap` 抢帧。

## 5. 减少动态

```css
@media (prefers-reduced-motion:reduce){
  html{scroll-behavior:auto}
  .rv{transition:none;opacity:1;transform:none}
  .slide.sw-out{transition:none}
  .slide.sw-in{animation:none}
  .cplate{animation:none}
  .stream{animation:none}
}
```

`.rv` 在这里直接归零而不是"取消动画"——否则元素停在 `opacity:0` 上，整页空白。

## 调参表

| 想要 | 改哪里 |
|---|---|
| 入场更快/更慢 | `.88s` 改 0.6–1.2s（同步改 `.rv` 与分层那几条） |
| 入场更"软" | 曲线末段再拖长：`cubic-bezier(.16,.84,.22,1)` |
| 错峰更明显 | `68ms` 调 90ms，封顶 `520ms` 一起调 |
| 换 deck 更利落 | `.54s` → `.4s`，`sw-in` 的 `62%` → `55%` |
| 关掉呼吸/浮动 | 删 `.glow`/`.cplate` 两行 `animation` |

## 不要碰

1. `.slide.in .rv{opacity:1;transform:none}` —— `sync()` 的判据靠它。
2 . `@keyframes swIn` 里的 `62%{opacity:1}` —— 删掉会让新页半透明滑入。
3. `progTo/progStep` 的 rAF 结构 —— 换回 `scroll` 事件直写会抖。
4. `--i` 的封顶 `min(…,520ms)` —— 去掉后长页面（参考文献 10 条）最后一条要等 1s 以上。
