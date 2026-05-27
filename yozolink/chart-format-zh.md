---
layout: post
title:  "Yozora Link"
date:   2026-04-19 12:00:00 +0800
categories: YozoLink
permalink: /yozolink/chart-format-zh
---


## 概述

Yozora Link 以 `.yzcpkg` 包的形式分发歌曲，这是标准 ZIP 压缩格式，内部结构如下：

- `song.json`：包级元数据与各难度索引
- 每个难度一个 `.yzc.json` 文件（Stable / Drifting / Faint / Relink）
- 一个音频文件（`.mp3` 或 `.ogg`）
- 一个封面图文件（可选，`.jpg` 或 `.png`）

两种 JSON 格式当前版本均为 `1`。`song.json` 与各 `.yzc.json` 中的路径相对于包根目录解析。

---

## `song.json`

包级描述文件。由编辑器写入，游戏在导入时读取。

```json
{
  "version": 1,
  "id": "mynick_mysong",
  "title": "Song Title",
  "artist": "Artist Name",
  "cover_color": [0.95, 0.70, 0.40],
  "audio": "music.ogg",
  "cover": "cover.jpg",
  "charts": [
    { "difficulty": "Stable",   "level": "5",  "chart_constant": 5.0,  "chart": "Stable.yzc.json" },
    { "difficulty": "Drifting", "level": "8",  "chart_constant": 8.0,  "chart": "Drifting.yzc.json" },
    { "difficulty": "Faint",    "level": "10", "chart_constant": 10.0, "chart": "Faint.yzc.json" },
    { "difficulty": "Relink",   "level": "12", "chart_constant": 12.0, "chart": "Relink.yzc.json" }
  ]
}
```

### 顶层字段

| 字段 | 类型 | 说明 |
|-----|------|-----|
| `version` | int | 格式版本号（当前为 `1`） |
| `id` | string | 歌曲唯一标识，需避开内置歌曲 ID 冲突；导入侧只接受小写英文字母、数字和下划线（`[a-z0-9_]`） |
| `title` | string | 显示用标题 |
| `artist` | string | 艺术家或作曲者 |
| `cover_color` | array[3] | 选曲列表中歌曲行的背景色，RGB 浮点值，范围 `[0.0, 1.0]` |
| `audio` | string | 音频文件名（包根目录相对路径） |
| `cover` | string | 封面图文件名，可选 |
| `charts` | array | 难度索引（见下） |

### `charts` 条目字段

| 字段 | 类型 | 说明 |
|-----|------|-----|
| `difficulty` | string | 必须为 `"Stable"`、`"Drifting"`、`"Faint"`、`"Relink"` 之一 |
| `level` | string | 显示给玩家的难度等级，如 `"7"`、`"9+"`、`"11"` |
| `chart_constant` | number | *可选* — rating 用的谱面定数。缺省时，整数 `level` 视为 `.0`，`"9+"` 这类整数加号视为 `.5`，非数字标签视为 `0.0` |
| `difficulty_label` | string | *可选* — 特殊谱面的显示标签覆盖；留空时使用 `difficulty` |
| `chart` | string | 难度对应的谱面文件路径 |
| `title` | string | *可选* — 覆盖顶层 `title` |
| `artist` | string | *可选* — 覆盖顶层 `artist` |
| `audio` | string | *可选* — 该难度的独立音频覆盖 |
| `cover` | string | *可选* — 该难度的独立封面覆盖 |
| `charter` | string | *可选* — 该难度的制谱者署名 |
| `cover_source` | string | *可选* — 封面来源说明（作者、链接、委托备注）；在选曲详情面板与 `charter` 并列显示 |

仅在与顶层值不同的情况下才写入覆盖字段。

---

## `<难度名>.yzc.json`

每个难度对应一个文件，描述元数据、拍号和音符内容。

```json
{
  "version": 1,
  "metadata": { ... },
  "timing": { ... },
  "notes": [ ... ]
}
```

### `metadata`

| 字段 | 类型 | 说明 |
|-----|------|-----|
| `title` | string | 歌曲名 |
| `artist` | string | 艺术家 |
| `charter` | string | 制谱者 |
| `cover_source` | string | 封面来源说明（作者、链接、委托备注）；如 `song.json` 的 charts[] 条目里也写了会覆盖此处 |
| `difficulty_name` | string | 必须与父 `song.json` 条目中的 `difficulty` 一致 |
| `difficulty_level` | string | 难度等级，自由格式字符串 |
| `audio_file` | string | 音频文件名（包根目录相对路径） |
| `preview_time` | float | 选曲界面试听起始点（秒） |
| `preview_end_time` | float | 试听结束点（秒），`0` 表示使用默认时长 |
| `background` | string | 背景图 / 封面文件名（可选） |

### `timing`

| 字段 | 类型 | 说明 |
|-----|------|-----|
| `offset` | float | 音频偏移量（秒）；正值将谱面相对音频延迟（音频起始若有空白，使用正值把谱面零点推至音乐实际开始位置） |
| `bpm_changes` | array | BPM 变速点列表 |

每个 `bpm_changes` 条目：

| 字段 | 类型 | 说明 |
|-----|------|-----|
| `time` | float | 新 BPM 生效的时间点（秒） |
| `bpm` | float | BPM 数值 |
| `beats_per_measure` | int | *可选* — 编辑器时间轴拍号分子，默认 `4` |
| `beat_unit` | int | *可选* — 编辑器时间轴拍号分母，默认 `4` |

`beats_per_measure` 与 `beat_unit` 只影响编辑器的小节线 / 标尺显示，不改变游戏内判定时序。

### `theme_events`

可选的谱面主题切换数组。不使用该功能时会省略。

| 字段 | 类型 | 说明 |
|-----|------|-----|
| `time` | float | 切换开始的谱面时间（秒） |
| `target_t` | float | 主题混合目标，`0.0` = 暗色，`1.0` = 亮色 |
| `transition_dur` | float | 切换时长（秒），默认 `0.6` |
| `force` | bool | *可选* — 为 `true` 时强制应用该事件，即使玩家主题设置通常会覆盖谱面驱动主题 |

### `approach_speed_events`

可选的谱面内缩圈速度变更点数组。整首歌都使用普通 `1.0x` 缩圈运动时省略。
如果没有 `0.0` 秒事件，谱面等价于隐含存在 `{ "time": 0.0, "speed": 1.0 }`。

| 字段 | 类型 | 说明 |
|-----|------|-----|
| `time` | float | 该缩圈速度开始生效的谱面时间点（秒） |
| `speed` | float | 非负缩圈运动倍率。`0.0` 表示暂停，`1.0` 表示正常速度，大于 `1.0` 表示加速 |

缩圈速度事件只影响音符本体/缩圈的出现时间与缩圈进度，不改变判定时间、BPM、音频时间、玩家 Ring Speed 设置，也不替代每个 note 自身的 `ring_time_mult` / `ring_scale_mult`；这些值会和谱面内缩圈速度事件复合计算。

### `notes`

音符对象数组。建议按 `time` 升序排列，但非强制要求——引擎加载时会重新排序。

---

## 坐标系统

所有位置使用**归一化坐标**，范围 `[0.0, 1.0]`：

- `x`：`0.0` = 左边缘，`1.0` = 右边缘
- `y`：`0.0` = 上边缘，`1.0` = 下边缘

该设计保证谱面跨分辨率一致。

---

## 音符类型

每个音符必有 `type`、`time`、`x`、`y`。特定类型附带额外字段。以下两个可选字段可出现在任意类型上：

| 字段 | 类型 | 说明 |
|-----|------|-----|
| `ring_time_mult` | float | 提示圈持续时间倍率，`1.0` = 使用全局值。等于 `1.0` 时省略 |
| `ring_scale_mult` | float | 提示圈大小倍率，`1.0` = 使用全局值。等于 `1.0` 时省略 |

### Tap / Ex-Tap

标准单点音符。Ex-Tap 使用相同几何结构，不会出现 Pure+ 或 Break 以外的判定，并有视觉强调。

```json
{ "type": "tap",    "time": 1.5, "x": 0.3, "y": 0.6 }
{ "type": "ex-tap", "time": 2.0, "x": 0.4, "y": 0.6 }
```

### Hold / Ex-Hold

在 `time` 按下并持续至 `end_time`。

```json
{ "type": "hold",    "time": 2.0, "end_time": 3.5, "x": 0.5, "y": 0.5 }
{ "type": "ex-hold", "time": 4.0, "end_time": 5.0, "x": 0.5, "y": 0.5 }
```

### Slide / Ex-Slide

在第一个路径点的时间按下，然后**手指拖动**slide 沿路径移动到终点。外层 `time`、`x`、`y` 与第一个路径点一致。

```json
{
  "type": "slide",
  "time": 4.0, "x": 0.2, "y": 0.5,
  "path": [
    { "x": 0.2, "y": 0.5, "time": 4.0 },
    { "x": 0.5, "y": 0.3, "time": 4.5 },
    { "x": 0.8, "y": 0.5, "time": 5.0 }
  ]
}
```

每个路径点可带一个可选的 `fake` 布尔字段（默认 `false`）。**自 finger-driven 模型起，加载时所有中间路径点会被强制视为 `fake: true`，首节点和末节点强制为 `false`**——序列化时字段值仍保留以便编辑器圆角往返，但游戏判定/渲染对中间节点一视同仁地按"假节点"处理（不绘制圆点、不发出经过音效、不产生判定）。首节点和末节点始终为真节点（head 进行 tap 判定，tail 决定终点位置）。

**中间节点的 `time` 字段**：仍然有意义——驱动轨迹上 guide light（指引亮点）沿轨迹的运动节奏。Guide light 在 `path[k].time` 时刻位于 path[k] 对应的渲染曲线位置，提供"现在应该划到哪里"的视觉参考。

**Slide 玩法（finger-driven 模型）**：
- 首节点按 tap 判定（同 tap note）；
- 首节点判定确定后（真 tap 或 ±120ms 后自动 break），slide 进入拖动状态——slide 本体跟随按住它的手指（手指在 slide 当前位置 240px 内即视为持有）；
- 中段判定（slide 总时长 ≥ 300ms 时）：在中段窗口（首节点后 100ms 至末节点前 200ms）内，按"手指在持有 AND slide 位于轨迹 240px 内"的累计时长比例分档（≥0.75 → Pure+，≥2/3 → Pure，≥1/3 → Connect，否则 Break）；
- 末段判定：在末节点 ±120ms 内，slide 本体到末节点的最近距离决定末段评级（≤240px → Pure+，≤400px → Connect，否则 Break）；
- 最终判定 = min(首节点, 中段, 末段)。
- 补偿：长 slide 的首节点自动 break 但中段和末段都 ≥ Connect → 最终 Connect。短 slide（<300ms）不享受补偿。

### Drag

无需点击。判定时间内任何手指位于命中区域即可触发。

```json
{ "type": "drag", "time": 6.0, "x": 0.5, "y": 0.4 }
```

### Flick

带方向的快速滑动。

```json
{ "type": "flick", "time": 7.0, "x": 0.5, "y": 0.5, "direction": 90 }
```

`direction`：角度（度数）——`0` = 右，`90` = 上，`180` = 左，`270` = 下。

---

## 判定

每个音符的触发时机依据以下四档评定：

| 评级 | 含义 |
|-----|-----|
| Pure+ | 最高精度命中 |
| Pure | 标准命中 |
| Connect | 偏早或偏晚，但仍在判定窗口内 |
| Break | 漏掉或超出判定窗口 |

具体的时序判定逻辑依音符类型而异：

- **Tap / Ex-Tap / Hold / Ex-Hold / Slide / Ex-Slide（起始点）**：按相对判定时间的绝对距离，在判定窗口内按差值分档。
- **Drag / Flick**：只要命中在判定窗口内，一律为 Pure+。
- **Hold / Ex-Hold（持续段）与 Slide / Ex-Slide（路径）**：按维持输入的时间占比分档；最终评级由该比值推出。

Hold 和 Slide 的最终判定由起始点和持续段的判定共同决定。

---

## 包结构示例

```
mynick_mysong.yzcpkg  (ZIP)
├── song.json
├── Stable.yzc.json
├── Drifting.yzc.json
├── Faint.yzc.json
├── Relink.yzc.json
├── music.ogg
└── cover.jpg
```
