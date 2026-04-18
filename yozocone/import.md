---
layout: post
title:  "Yozora Connect"
date:   2026-04-19 12:00:00 +0800
categories: YozoCone
permalink: /yozocone/import
---


将 `.yzcpkg` 谱面包导入 YozoCone 的两种方式。

## 方法一：应用内导入

1. 打开 YozoCone，进入选曲界面。
2. 点击右上角「+ Import song」按钮。
3. 在弹出的文件选择器中定位并选择 `.yzcpkg` 文件。
4. 导入完成后，曲库自动刷新，选曲光标跳转至新导入的歌曲。

## 方法二：从其他应用导入

从文件管理器、浏览器等任意应用中接收或下载的 `.yzcpkg` 文件：

- **Android**：点击文件，在「打开方式」菜单中选择 YozoCone。
- **iOS**：点击文件后选择分享按钮，在分享菜单中选择 YozoCone。

导入完成后曲库自动刷新。

## 重新导入与更新

对同一首歌曲重复导入新版本的 `.yzcpkg`：

- 旧谱面数据被完全覆盖。
- **游戏记录处理**：若某难度的音符数量未变，原最佳分数保留；若音符发生增减，该难度的分数清零，其余难度不受影响。

## 常见错误

| 提示 | 原因 | 处理方式 |
|---|---|---|
| `Song ID conflicts with a built-in song` | 导入的谱面 ID 与游戏内置歌曲 ID 冲突 | 联系制谱者在编辑器中修改 `song_id` 并重新导出 |
| `Import failed (error <n>)` | 压缩包损坏、缺少 `song.json`，或文件无法读取 | 重新获取完整的 `.yzcpkg` 文件 |

## 关于 `.yzcpkg` 格式

`.yzcpkg` 为 ZIP 压缩格式的容器，包含：

- `song.json`：歌曲元数据与各难度引用
- 各难度谱面文件（`<难度名>.yzc.json`）
- 音频文件（`.mp3` 或 `.ogg`）
- 封面图（可选，`.jpg` 或 `.png`）

详细格式规范参见 [谱面格式规范](./chart-format-zh)。编辑器导出时自动打包，无需手动操作。
