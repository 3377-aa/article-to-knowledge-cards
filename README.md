# make-knowledge-cards

一个把文章变成知识卡片的 Agent Skill（技能包）。本仓库包含 1 个 Skill 及其完整测试记录，可直接开源。

## 1. 项目解决什么问题

读了一篇文章，想把重点留下来复习，但手动整理太费时间；直接让 AI 总结，又常常遇到这些问题：

- 一张卡片塞了三四个知识点，复习时抓不住重点
- 开头结尾重复说的观点被当成多个知识点，重复出卡
- 文章信息太少，AI 硬凑出一堆空洞卡片
- 编造原文里没有的内容

本 Skill 用一份明确的工作流程说明书（SKILL.md）约束 AI 的行为，解决上述全部问题。

## 2. 主要功能

把用户粘贴的文章，或本地 Markdown / TXT 文件，转换成 5~8 张知识卡片。

每张卡片固定四个字段：

| 字段 | 说明 |
| --- | --- |
| 标题 | 短、具体，同一组卡片内不重复 |
| 核心知识 | 一句话陈述知识点，必须来自原文 |
| 简明解释 | 通俗语言解释，2~4 句 |
| 例子 / 自测 | 原文中的例子，或可用原文自查答案的自测问题 |

核心规则：

- 一张卡片只讲一个知识点；宁可少出卡片，也不合并知识点凑数
- 自动去重，开头/结尾重复表述只算一次
- 不编造原文没有的信息，数字、术语、结论与原文一致
- 信息不足时不强行凑数量：不足 5 张会说明原因，连 3 张都凑不出时直接拒绝生成
- 卡片语言跟随原文语言（中文文章出中文标签，英文文章出英文标签）

**暂不支持**：网页抓取（URL）、PDF、Anki、图形界面。

## 3. 安装方法

无需安装任何依赖，把 Skill 目录复制到目标技能目录即可：

```powershell
# 用户级安装（所有会话可用）
Copy-Item "skills/make-knowledge-cards" "$env:USERPROFILE\.workbuddy\skills\" -Recurse

# 项目级安装（仅某个项目可用）
Copy-Item "skills/make-knowledge-cards" "<项目>\.workbuddy\skills\" -Recurse
```

## 4. 使用方法

安装后在对话中直接触发，两种输入方式：

```
用这篇文章做知识卡片：（粘贴正文）
```

```
把 D:\notes\http-cache.md 做成知识卡片
```

支持 `.md` 和 `.txt` 文件路径。AI 会按 SKILL.md 的流程执行：读全文 → 筛选重要知识点 → 去重 → 生成卡片 → 输出前自检。

## 5. 输入输出示例

**输入**（`tests/test_tech_tutorial.md` 节选）：

```markdown
强缓存通过响应头控制。服务器返回 `Cache-Control: max-age=31536000` 表示资源在
31536000 秒（一年）内可以直接从本地缓存读取，完全不需要请求服务器。
另一个常用指令是 `no-cache`。注意它的名字有误导性：`no-cache` 不是"不缓存"，
而是"缓存但每次使用前必须向服务器验证是否过期"。
```

**输出**（节选，完整见 `tests/outputs/output_01_tech.md`）：

```markdown
## 卡片 1：强缓存靠 max-age

- **核心知识**：`Cache-Control: max-age=31536000` 表示资源在 31536000 秒内
  可直接从本地缓存读取，完全不请求服务器。
- **简明解释**：强缓存是性能最好的缓存方式，因为浏览器连网络请求都不发。
  有效期内的资源直接读本地副本。
- **例子/自测**：`app.js` 的响应头写了 `max-age=86400`，同一用户当天第二次
  打开页面时，浏览器会向服务器发请求吗？（答：不会）
```

英文文章自动切换为英文标签：

```markdown
## Card 1: A pomodoro is a fixed 25-minute interval

- **Key point**: ...
- **Explanation**: ...
- **Example or self-test**: ...
```

## 目录结构

```
First_Skill/
├── README.md
├── skills/
│   └── make-knowledge-cards/
│       ├── SKILL.md            # 技能说明（唯一必需文件）
│       └── agents/
│           └── openai.yaml     # 平台展示元数据（名称/简介/默认提示词）
├── examples/
│   └── 古文志怪-知识卡片.md     # 真实使用示例（文言叙事文本 → 8 张母题卡片）
└── tests/
    ├── test_*.md / .txt        # 5 个测试样本
    └── outputs/                # 对应的 5 份生成结果
```

## 测试结果

| 样本 | 类型 | 结果 |
| --- | --- | --- |
| test_tech_tutorial.md | 中文技术教程 | 5 张卡片 |
| test_science.md | 中文科普 | 6 张卡片 |
| test_essay.md | 中文议论文（首尾重复观点） | 5 张卡片，重复观点只出现一次 |
| test_english_short.md | 英文说明文 | 5 张卡片，标签全英文 |
| test_thin_note.txt | 无知识密度的会议纪要 | 0 张卡片，说明信息不足后拒绝生成 |

## 结构校验

```powershell
# Windows 需先设置 UTF-8，避免校验脚本按 GBK 读取中文
$env:PYTHONUTF8=1
python quick_validate.py skills/make-knowledge-cards
# Skill is valid!
```

## 许可证

MIT License，详见 [LICENSE](LICENSE)。

## 说明

- 仅本地开发与测试，未初始化 Git，未上传到任何远程仓库。
- 开源复用时只需复制单个 `skills/<name>/` 目录，无任何依赖。
