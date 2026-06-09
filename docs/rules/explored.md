# explored 规则

> **本文件作用**：定义 `mine/explored/` 与 `others/explored/` 的升级条件、status 管理、季度回看、搬运流程。
> **何时被调用**：用户说「升级到 explored」，或季度回看 explored 状态时。
> **关联**：[flow.md](../flow.md) · [redlines.md](../redlines.md) · [compiled.md](compiled.md)

---

## 0. 一句话原则

> **explored 是「想透了」的产出，不是「写完了」的归档。status 由用户判定，AI 不动。**

---

## 1. 什么是 explored

- `mine/explored/`：我自己的方法论、深度产出、成体系的思考
- `others/explored/`：外部内容经过深度消化后的精华（不是摘抄，是「我理解的版本」）

### 与 compiled 的区别

| compiled | explored |
|---|---|
| 主题聚合，3-7 条核心观点 | 成体系的方法论 / 深度产出 |
| 可能还在变化 | 相对稳定（但不是永恒） |
| 不要求能反驳自己 | 必须能写出反驳 + 回应 |
| 不要求适用边界 | 必须能写出适用边界 |

---

## 2. 升级条件（从 compiled → explored）

满足以下**全部**条件才升级（🔒 由用户判定，AI 不判定）：

- ✅ 该 compiled 已经至少 **2 次回看 / 修订**
- ✅ 用户能写出「这套观点的反驳是什么 + 我的回应」
- ✅ 用户能写出「这套观点的适用边界」
- ✅ 用户愿意把这篇分享给一个具体的朋友

有一条做不到 → **留在 compiled 层**，不要硬升级。

> 🔒 AI 不能建议「这篇可以升级了」。只有用户主动说「升级到 explored」时才执行。

---

## 3. status 管理（防博物馆）

每个 `mine/explored/*.md` 在 frontmatter 中维护 `status` 字段：

| 状态 | 含义 | 处理 |
|---|---|---|
| `alive` | 这条仍然成立 | 继续保留，可被引用 |
| `aging` | 部分过时，待修订 | 在文末加一段「现在的我会怎么改」 |
| `outdated` | 已不成立 | 保留作为历史档案，前置警告：`> ⚠️ 此观点已过时，仅作历史记录` |

### 🔒 status 判定红线

- **AI 永远不判定 status**。只有用户说「这篇标 aging / outdated」时才改。
- AI 可以在季度回看时**提问**：「今天的你还同意这篇吗？」——但不能替用户回答。
- 不要因为一时兴起就把 alive 改 outdated——至少观察 2 周再标注。

---

## 4. 季度回看

每季度末（建议跟随月度复盘的最后一次），花 30 分钟：

```
1. 列出所有 mine/explored/*.md
2. 对每篇问自己：「今天的我，还会同意这篇吗？」
3. 根据答案更新 status 字段
4. 填写 last_updated 与「最近一次回看」结论
```

### 回看时的规则

- ❌ **不要删除** outdated 的文件——它们是「过去你」的化石
- ❌ **不要改写**原文——在文末加注，不要修历史
- ✅ 可以在文末加「现在的我会怎么改」段落
- ✅ 可以加双向链接到新的 compiled / explored

---

## 5. others/explored → mine 的「搬运」流程

`others/explored/` 里真正影响了你的部分，必须经过 3 步才算成为你的：

```
Step 1: 在 mine/raw/ 写一条：
        「来自 [[others/explored/X]]，我现在的想法是 ___」

Step 2: 进入下次编译时，把这条 raw 升级到 mine/compiled/

Step 3: 在原 others/explored/X.md 末尾加一行：
        「→ 已被搬运到 [[mine/compiled/X]]」
```

> ⚠️ 不能直接把 others/explored 的内容 copy 到 mine/explored。必须经过 raw → compiled 的消化过程。

---

## 6. 文件命名与路径

| 轨道 | 路径 | 文件名 |
|---|---|---|
| mine | `mine/explored/<主题>.md` | 主题命名 |
| others | `others/explored/<主题>.md` | 主题命名 |

> 不按日期命名。explored 是主题聚合，不是时间线。

---

## 7. 写入流程（standard procedure）

```
1. 用户主动说「升级到 explored」
   - AI 不能主动建议升级

2. AI 确认升级条件
   - 提问："这篇 compiled 是否满足以下条件？"
   - 列出 §2 的 4 个条件
   - 用户逐条确认

3. AI 起草 explored 文件
   - 基于 compiled 内容
   - frontmatter 加 status: alive
   - 核心结论 → 🔒 由用户填写
   - 加「反驳与回应」「适用边界」段落（由用户填写）

4. 通用确认协议
   - 列【内容 / 路径】给用户确认

5. 落盘
   - 写入对应路径
   - 原 compiled 文件加 `upgraded_to: mine/explored/<主题>.md`
   - 原 compiled 文件保留（不删）

6. commit
   git -C <SELFDEX-PATH> add -A && git -C <SELFDEX-PATH> commit -m "explored: <主题>"

7. 告知用户
   "✅ 已升级到 <路径>，本地已 commit。
    🔒 请手动填写「反驳与回应」「适用边界」字段。"
```

---

## 8. 自查清单

### 8.1 升级判定
- [ ] 是用户主动说「升级」的吗？（AI 没有主动建议？）
- [ ] 4 个升级条件是否逐条确认？
- [ ] 有没有硬升级（条件不满足就升了）？

### 8.2 status
- [ ] status 字段是否存在？
- [ ] status 是否由用户判定（AI 没有擅自改）？
- [ ] outdated 文件是否保留（没删）？
- [ ] outdated 文件是否有前置警告？

### 8.3 搬运
- [ ] others → mine 是否走了 3 步？
- [ ] 有没有直接 copy 而不经过 raw → compiled？

### 8.4 落盘前
- [ ] 通用确认协议是否执行？
- [ ] 原 compiled 是否保留 + 加了 `upgraded_to:`？
- [ ] 用户主笔的字段（反驳与回应 / 适用边界）是否留空 / 由用户填写？

---

## 9. 反模式

| 反模式 | 危害 | 正确做法 |
|---|---|---|
| AI 建议「这篇可以升级了」 | 越界，explored 是用户的判断 | 只有用户主动说才执行 |
| AI 判定 status | 违反红线 | AI 只能提问，不能回答 |
| 删除 outdated 文件 | 丢失历史 | 保留 + 前置警告 |
| 改写 explored 原文 | 篡改历史 | 在文末加注 |
| 直接 copy others → mine | 跳过消化过程 | 走 3 步搬运 |
| 条件不满足就升级 | explored 质量下降 | 留在 compiled，不硬升 |

---

## 10. 一句话兜底

> explored 不是「写完了」的终点，而是「想透了」的起点——它会 alive、会 aging、会 outdated，这才是活的知识。

---

*相关：[../flow.md](../flow.md) · [../redlines.md](../redlines.md) · [compiled.md](compiled.md) · [profile.md](profile.md)*
