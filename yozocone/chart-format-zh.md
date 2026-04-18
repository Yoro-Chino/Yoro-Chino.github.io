---
layout: post
title:  "Yozora Connect"
date:   2026-04-19 12:00:00 +0800
categories: YozoCone
permalink: /yozocone/chart-format-zh
---


## 概述

YozoCone 以 `.yzcpkg` 包的形式分发歌曲，这是标准 ZIP 压缩格式，内部结构如下：

- `song.json`：包级元数据与各难度索引
- 每个难度一个 `.yzc.json` 文件（Near / Medium / Distant / Spacey）
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
    { "difficulty": "Near",    "level": "5",  "chart": "Near.yzc.json" },
    { "difficulty": "Medium",  "level": "8",  "chart": "Medium.yzc.json" },
    { "difficulty": "Distant", "level": "10", "chart": "Distant.yzc.json" },
    { "difficulty": "Spacey",  "level": "12", "chart": "Spacey.yzc.json" }
  ]
}
```

### 顶层字段

| 字段 | 类型 | 说明 |
|-----|------|-----|
| `version` | int | 格式版本号（当前为 `1`） |
| `id` | string | 歌曲唯一标识，需避开内置歌曲 ID 冲突，仅允许文件名与 URL 安全字符 |
| `title` | string | 显示用标题 |
| `artist` | string | 艺术家或作曲者 |
| `cover_color` | array[3] | 选曲列表中歌曲行的背景色，RGB 浮点值，范围 `[0.0, 1.0]` |
| `audio` | string | 音频文件名（包根目录相对路径） |
| `cover` | string | 封面图文件名，可选 |
| `charts` | array | 难度索引（见下） |

### `charts` 条目字段

| 字段 | 类型 | 说明 |
|-----|------|-----|
| `difficulty` | string | 必须为 `"Near"`、`"Medium"`、`"Distant"`、`"Spacey"` 之一 |
| `level` | string | 显示给玩家的难度等级，如 `"7"`、`"9+"`、`"11"` |
| `chart` | string | 难度对应的谱面文件路径 |
| `title` | string | *可选* — 覆盖顶层 `title` |
| `artist` | string | *可选* — 覆盖顶层 `artist` |
| `audio` | string | *可选* — 该难度的独立音频覆盖 |
| `cover` | string | *可选* — 该难度的独立封面覆盖 |
| `charter` | string | *可选* — 该难度的制谱者署名 |

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

在第一个路径点的时间按下，然后沿路径移动。外层 `time`、`x`、`y` 与第一个路径点一致。

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
| Lost | 漏掉或超出判定窗口 |

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
├── Near.yzc.json
├── Medium.yzc.json
├── Distant.yzc.json
├── Spacey.yzc.json
├── music.ogg
└── cover.jpg
```
