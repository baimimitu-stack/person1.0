# person1.0

> 通用的"活人感 AI 陪伴"提示词砖块。哪里要用往哪搬。

一个零依赖、单文件的 TypeScript 工具包，把 AI 从"客服助手"改造成"一个具体的人在过 TA 的日子，顺便跟你聊天"。

## 是什么

输入：陪伴对象的 persona + 此刻状态 + 关系 + 上一段记忆 + 用户这句话
输出：一段可以直接扔给任何 OpenAI 兼容 / Gemini / DeepSeek 的 system+user prompt

它不是：
- 不是 AI 调用封装（自己选 fetch）
- 不是记忆库（可以跟 memory-core 拼）
- 不是 UI 库（自己写气泡）
- 不是角色卡格式（可以跟 Tavern V3 / companion-v1.2 兼容）

## 安装

```bash
# 手动复制这 3 个文件到你的项目：
companion-preset.ts
example-persona.json
README.md
```

没有 npm install，就是搬砖。

## 30 秒上手

```ts
import { buildCompanionPrompt, sanitizeCompanionReply } from "./companion-preset";
import myPersona from "./example-persona.json";

const prompt = buildCompanionPrompt({
  persona: myPersona,
  snapshot: { now: "周日 22:14", doing: "刷手机", mood: "普通", energy: 6 },
  relation: "认识 3 年的网友，最近有点疏远",
  memory: "",                   // 可选：从你的记忆库 build 出来的一段
  userName: "小明",
  userText: "在干嘛",
});

const raw = await yourAiCall(prompt);
const cleaned = sanitizeCompanionReply(raw);
displayInBubble(cleaned);
```

## 核心设计约定

1. **人设是一次性的**：`persona` 描述"这个人是谁"，不描述"这个人此刻怎样"
2. **状态是每次拼的**：`snapshot` 每次调用重新填，模拟"活人的当下状态"
3. **记忆是可选的**：不接记忆库也能跑（AI 每轮当作初次对话）
4. **输出是纯文本**：不返 JSON，不打 Markdown，不夹 HTML —— 这是硬约束
5. **抗 AI 腔**：内置 8 条禁令 + sanitize 时会剥掉一些兜底特征

## 跟 memory-core 的关系

- **兼容**：`persona.identity` 和 `persona.three_faces` 直接映射 memory-core v1.2 的 `character.basic_info` 和 `character.three_faces`
- **不冲突**：memory-core 管"关系归档 + 记忆"，本包管"当次对话怎么说话"
- **推荐搭配**：用 memory-core 存记忆，用本包生成提示词，用你的 AI 客户端调用

## 兼容 Tavern V3 角色卡

你可以从任何 Tavern V3 卡 `.data.description` + `.data.personality` 快速填 `persona.identity` 和 `persona.trait_lines`，7 分兼容够用。

## 文件清单

| 文件 | 作用 |
| --- | --- |
| `companion-preset.ts` | 全部代码（类型 + rules + buildPrompt + sanitize + tinyBuildMemoryContext） |
| `example-persona.json` | 一份示例 persona（阿岚 · 25 岁咖啡馆店主） |
| `README.md` | 你正在看的这份 |

## License

MIT
