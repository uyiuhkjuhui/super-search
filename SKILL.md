---
name: super-search
description: Use when the user wants to search across multiple platforms, compare what different sites say, do comprehensive research, mentions 搜索/搜一下/查查/全网/对比/各平台/小红书/B站/知乎/微博/GitHub/深度研究, or asks "XX平台怎么说"
requires: agent-reach
---

> **Super Search v3.0 — Agent-Reach 版** — bili · opencli · curl/Jina · gh · yt-dlp · 全面大量 · 中文不乱码

---

## 底层引擎

**不再用 `web_fetch`。** 全部走 Agent-Reach 提供的原生命令行工具：

| 平台 | 命令 | 编码 |
|------|------|------|
| 📺 B站 | `bili search "关键词" --type video --json` | ✅ UTF-8 |
| 📕 小红书 | `opencli xiaohongshu search "关键词" -f yaml` | ✅ UTF-8 |
| 🐦 Twitter/X | `opencli twitter search "关键词" -f yaml` | ✅ |
| 📖 Reddit | `opencli reddit search "关键词" -f yaml` | ✅ |
| 💻 GitHub | `gh search repos "关键词" --limit 20` | ✅ |
| 📹 YouTube | `yt-dlp "ytsearch10:关键词" --dump-json` | ✅ |
| 🌐 网页 | `curl -s "https://r.jina.ai/URL"` (Jina Reader) | ✅ |
| 🌐 全网 | `curl -s "https://r.jina.ai/https://www.baidu.com/s?wd=关键词"` | ✅ |
| 📰 RSS | `python3 -c "import feedparser..."` | ✅ |

**Python 虚拟环境**：所有命令需先激活：
```bash
source "$USERPROFILE/.agent-reach-venv/Scripts/activate"
export PYTHONIOENCODING=utf-8
```

---

## 搜索策略：1000条级别

**目标：每个平台尽可能采集 1000+ 条结果。**

### 多关键词扩展
原始搜索词 → 自动生成相关关键词组：
- 全称 + 简称 + 别名 + 英文名
- 相关话题词（从第一批结果中提取高频词）

**示例**：搜索「辽宁工程技术大学 机电」→ 自动扩展为：
```
1. "辽宁工程技术大学"
2. "辽工程"
3. "辽宁工大"
4. "辽宁工程技术大学 机电"
5. "辽宁工程技术大学 机械"
6. "Liaoning Technical University"
7. "辽宁工大 就业"
8. "辽宁工大 考研"
```
每个关键词搜 50 页，B站单平台即可采集 8000 条候选。

### 多页深挖
每个关键词搜 50 页，`bili search "XX" --page 1..50 -n 20 --json`

### 多平台并行
所有平台同时发起，不等结果。

### 去重合并
跨页、跨关键词、跨平台去重（按标题相似度 + URL）。

### 分批输出
1000 条不全量展示，而是：
1. 先展示 Top 20（按播放/热度排序）
2. 按类别分组（教程/评价/介绍/其他）
3. 用户说「更多」→ 展示下一批

---

## 命令模式

| 用户说 | 行为 |
|--------|------|
| 「搜XX」 | 全平台并行搜索，20条+ |
| 「对比XX」 | 跨平台对比表 |
| 「深度研究XX」 | 递归搜索 → 提取子话题 → 追加搜索 → 综合报告 |
| 「只搜B站+知乎 XX」 | 指定平台 |
| 「追踪XX」 | 保存话题，remember |
| 「今天热点」 | B站热门+微博热搜 |
| 「核实XX」 | 多源交叉验证 |

---

## 输出格式

```
🔍 搜索: <关键词> | 共 N 条结果 | <时间>

📺 B站 (N条)
  <结果列表>

🌐 全网 (N条)
  <结果列表>

💻 GitHub (N条) [如相关]
  <结果列表>

📊 综合洞察: <跨平台分析>
💡 一句话: <结论>
```

---

## 编码保障

- `bili --json` → 管道到 `python3 -c` 解析 → UTF-8 输出
- `opencli -f yaml` → 原生 UTF-8
- `curl` + Jina → Markdown 格式，UTF-8
- 如遇乱码 → `export PYTHONIOENCODING=utf-8` + 重试

---

## 安装前提

```bash
# 需要 Agent-Reach 已安装
source "$USERPROFILE/.agent-reach-venv/Scripts/activate"
agent-reach doctor  # 确认 bili、opencli 等可用
```
