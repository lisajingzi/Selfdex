# Selfdex 流程总指挥

> **本文件作用**：Selfdex 各功能（收集 / 分类 / 落盘 / 编译 / 升级 / 调用）之间的协调与流转规则，是 AI 执行前必看的索引。
> **何时被调用**：AI 接到任何涉及 Selfdex 读写的任务时，先读本文件确认走哪条流程，再去看对应的 `docs/rules/<part>.md` 拿执行细则。
> **关联文件**：`docs/rules/*.md`（各模块执行细则）· `docs/redlines.md`（红线）· `docs/templates/`（模板）· `docs/user-rules-template.md`（全局触发指令）

---

## 0. 三句话原则

1. **本文件只指路，不写细则**。每个动作的具体执行规则都在 `docs/rules/<part>.md` 里。
2. **每次执行前先看 `docs/redlines.md`**。红线优先级最高。
3. **不确定就反问，不要猜**。

---

## 1. Selfdex 整体流转图

```
                  ┌──────────────────────────────┐
                  │  外部输入                    │
                  │  对话 / 文章 / 灵感 / 备忘录 │
                  └──────────────┬───────────────┘
                                 │
                    ┌────────────▼────────────┐
                    │  收集判断（mine? others?│
                    │   raw? memory?）         │
                    └────────────┬────────────┘
                                 │
        ┌────────────────────────┼────────────────────────┐
        ▼                        ▼                        ▼
   ┌─────────┐              ┌─────────┐              ┌─────────┐
   │ mine/   │              │ mine/   │              │ others/ │
   │ raw/    │              │ memory/ │              │ raw/    │
   │ (观点)  │              │ (人/事) │              │ (素材)  │
   └────┬────┘              └────┬────┘              └────┬────┘
        │                        │                        │
        │   月度 / 攒到 ~20 条   │                        │
        ▼                        │                        ▼
   ┌─────────┐                   │                  ┌─────────┐
   │ mine/   │                   │                  │ others/ │
   │compiled/│                   │                  │compiled/│
   └────┬────┘                   │                  └────┬────┘
        │  满足升级条件          │                       │
        ▼                        │                       ▼
   ┌─────────┐                   │                  ┌─────────┐
   │ mine/   │                   │                  │ others/ │
   │explored/│◄─── 搬运 ─────────┼──────────────────┤explored/│
   └────┬────┘                   │                  └─────────┘
        │                        │
        └─────────┬──────────────┘
                  ▼
            ┌──────────┐
            │profile.md│ ──── 喂给任意 LLM ───►
            └──────────┘
```

---

## 2. 场景 → 流程 → 规则 调度表

| 用户意图 / 触发场景 | 走哪条流程 | 必看规则 |
|---|---|---|
| 表达观点 / 偏好 / 思考 | 写入 `mine/raw/` | [rules/raw.md](rules/raw.md) |
| 介绍一个人（关系 / 背景 / 相处） | 写入 `mine/memory/people/` | [rules/memory.md](rules/memory.md) |
| 含人名 + 时间 + 行为的事件 | 写入 `mine/memory/events/<分类>/` | [rules/memory.md](rules/memory.md) |
| 既有事件又有思考 | 拆两份：events + raw，互链 | [rules/memory.md](rules/memory.md) + [rules/raw.md](rules/raw.md) |
| 来自他人的素材（文章 / 对话 / 观点） | 写入 `others/raw/` | [rules/raw.md](rules/raw.md) §others 部分 |
| 提炼一段 LLM 对话 / 长原文到 raw | 触发「来源可追溯」流程 | [rules/raw.md](rules/raw.md) 全文 |
| 月底 / 攒到 ~20 条 raw / 单主题 4-5 条 | 触发编译 raw → compiled | [rules/compiled.md](rules/compiled.md) |
| compiled 想升级 explored | 走升级判定 | [rules/explored.md](rules/explored.md) |
| `others/explored` 影响了我，想内化 | 走「搬运」流程 | [rules/explored.md](rules/explored.md) §搬运 |
| 季度回看 explored 状态 | 标 alive / aging / outdated | [rules/explored.md](rules/explored.md) §status |
| 更新 profile.md | 用户主导，AI 协助 | [rules/profile.md](rules/profile.md) |
| 决策回顾 / 个性化建议 / 涉及个人观点的对话 | 触发读取协议（强制读 `INDEX.md` → 关键词命中 → 深读对应 compiled / memory / raw） | 本文 §5.1 |

---

## 3. 写入前的强制流程（任何写入都走这一遍）

```
1. 判断归属
   - mine 还是 others？
   - raw / memory / compiled / explored 哪一层？
   - memory 里是 people 还是 events？events 是哪个分类？
   - 不确定 → 反问，不要猜

2. 看红线
   - 打开 docs/redlines.md 过一遍
   - 涉及「AI 永不做」清单的字段 → 留空让用户写

3. 看对应 rules
   - 打开 docs/rules/<part>.md
   - 按里面的执行步骤、自查清单、反模式照做

4. 起草 + 通用确认协议
   - 套对应模板（docs/templates/ 或 mine/memory/*/template.md）
   - 把【内容 / 路径 / 分类】列给用户确认
   - 用户回「确认」或「OK」才写入

5. 落盘 + commit（不 push）
   - edit_file 写入
   - git commit，commit type：raw / memory-people / memory-event / others-raw / compiled / explored / profile
   - 告知用户：「✅ 已记录到 <路径>，本地已 commit，记得定期 push。」

6. 索引联动（仅当本次写入是 compiled / explored 文件时）
   - 同步更新根 `INDEX.md` 对应主题表格（路径 + 核心关键词 + 最后更新日期）
   - 与 compiled 文件一起 commit
   - 详见 docs/rules/compiled.md §9
```

---

## 4. 编译 / 升级流程（高阶，红线密集）

```
raw 阶段：自由生长，不预设结构
  │
  │  [触发：月底 / 攒到 ~20 条 / 单主题 4-5 条]
  ▼
compiled 阶段：人机协作，AI 起草，你主导提炼
  │  规则：rules/compiled.md
  │
  │  [升级条件全部满足]
  ▼
explored 阶段：方法论 / 深度产出
  │  规则：rules/explored.md
  │  红线：status 由你判定，AI 不动
  │
  ▼
季度回看：alive / aging / outdated
```

> ⚠️ **mine 与 others 不互染**。compiled 阶段同主题如果两边都有，用双向链接关联；不合并。详见 [rules/compiled.md](rules/compiled.md) §mine↔others 关联。

---

## 5. 调用 Selfdex 的路径

### 5.1 IDE 内（任意工作区）

- 全局 User Rules（`docs/user-rules-template.md`）已经写好触发条件和检索顺序
- AI 检测到触发条件后的检索路径（单级索引）：
  ```
  read INDEX.md（强制） → 关键词命中？
     ├─ 命中 → read 对应主题文件（mine/compiled/*.md）
     │         ↓
     │   涉及具体人 → 追加 read mine/memory/people/<姓名>.md
     │   涉及最近事件 → 追加 read mine/raw/YYYY-MM.md
     │   涉及具体事件 → grep mine/memory/events/
     └─ 未命中 → 明确告知「compiled 中暂无相关主题」，不凭印象作答
  ```
- 输出时按 `[基于 mine] / [基于 others] / [AI 通用知识]` 标注来源

### 5.2 网页版 LLM

把 `profile.md` 复制粘贴到对话开头，即可让网页版 AI（GPT / Gemini / Claude 等）快速建立对你的认知。

---

## 6. 触发词速查

| 用户说 | AI 行为 | 走哪条规则 |
|---|---|---|
| 「记到 Selfdex」/「存到 Selfdex」 | 进入写入流程，先确认 | 本文 §3 + 对应 rules |
| 「记一下」 | 反问「要记到 Selfdex 吗？」 | — |
| 「翻 Selfdex」/「看我之前怎么想」 | 进入读取流程 | 本文 §5.1 |
| 「编译 / 整理 / 提炼这个主题」 | 进入编译流程 | rules/compiled.md |
| 「升级到 explored」 | 进入升级判定 | rules/explored.md |
| 「更新 profile」 | 进入 profile 维护 | rules/profile.md |

> 触发词大小写不敏感：`Selfdex` / `selfdex` 都识别。

---

## 7. 不走 Selfdex 的情况

- 纯技术问题 / 通用知识 → 不读 Selfdex，不写 Selfdex
- 用户明确说「不用看 Selfdex」/「不用记」
- 闲聊、情绪发泄（不刺激、不可复用、一次性）
- 待办事项、购物清单、电话号码 → 这些归属备忘录，不进 Selfdex

---

*相关：[redlines.md](redlines.md) · [rules/](rules/) · [templates/](templates/) · [user-rules-template.md](user-rules-template.md)*
