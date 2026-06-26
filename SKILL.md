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

## 搜索策略：全面大量

**原则：宁多勿少，能搜尽搜。**

1. **并行搜索所有可用平台**（同时发起，不逐个等待）
2. **每个平台默认搜 15-20 条**结果（不限制 3-5 条）
3. **自动扩展搜索词**：原词 + 同义词 + 英文翻译 + 简称/全称
4. **递归深挖**：从第一批结果中提取新关键词，自动追加搜索
5. **去重合并**：同内容跨平台出现时合并展示

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
