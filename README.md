# big-data-ai-coursework

大数据与人工智能课程作业仓库 / Big Data and AI Coursework —— 同时维护一套"概念学习"工作流：把任意新概念标准化为五段式学习资料，再用关系图把若干概念编织成知识网。

---

## 🎯 仓库用途

本仓库承担三件事：

1. **概念学习 Skill** —— 把任意新概念转为统一格式的结构化学习笔记（`SKILL.md` 已落地）
2. **学习资料库** —— 把已经整理过的概念沉淀为 HTML 知识卡（`learning-materials/`）
3. **关系图谱** —— 把多个相邻概念的关系可视化，发现"概念间"的连接（`concept-relationship.md`）

---

## 📁 目录结构

```
big-data-ai-coursework/
├── README.md                       ← 本文件
├── concept-relationship.md         ← Agent / 上下文 / Skill 三角关系图解
├── iris_dataset.csv                ← 课程作业示例数据集 (150 条鸢尾花样本)
├── learning-materials/             ← 已沉淀的 HTML 学习资料
│   ├── agent.html
│   ├── llm-context-window.html
│   └── skill.html
└── .workbuddy/
    ├── skills/
    │   ├── concept-learning/         ← Skill 副本 (首次生成位置)
    │   │   └── SKILL.md
    │   └── concept-learning-skill/   ← Skill 副本 (主用位置)
    │       └── SKILL.md
    └── memory/
        └── 2026-09-06.md             ← 当日工作日志
```

---

## 🧩 Skill 存放路径

本仓库使用 [WorkBuddy](https://www.workbuddy.cn) 兼容的 Skill 格式。Skill 文件夹结构如下：

```
<skill-name>/
└── SKILL.md    ← 必备：YAML metadata + 正文指令
```

当前仓库内已落地的 Skill：

| 路径 | 范围 | 适用场景 |
|------|------|---------|
| `.workbuddy/skills/concept-learning-skill/SKILL.md` | **项目级** | 当前仓库协作者共享 |
| `.workbuddy/skills/concept-learning/SKILL.md` | 项目级（早期副本，内容与上一致） | 保留以兼容早期引用 |
| `~/.workbuddy/skills/concept-learning-skill/SKILL.md` | **用户级** | 跟随个人，所有项目可用 |

> 💡 推荐做法：项目级 Skill 走 `.workbuddy/skills/`；个人跨项目复用 Skill 拷到 `~/.workbuddy/skills/`。

### Skill 的加载机制

模型在每次推理时会读到该 Skill 的 YAML 元数据（`name` + `description`），共三级：

```
L1 元数据  ←  常驻在 system prompt，用于触发判断 (~100 词)
L2 正文    ←  命中时按需加载 (<5K 词)
L3 资源    ←  scripts/ references/ assets/ (按需取用)
```

---

## 📞 调用方法

### 1. 在当前会话里直接调用

只要 Skill 已经存在于本仓库的 `.workbuddy/skills/` 下，且 YAML 元数据被工作环境读到，直接用自然语言表达任务即可触发。例如：

- "用 concept-learning-skill 帮我整理 XGBoost 的学习资料"
- "概念学习 / 学习笔记 / 知识卡片"
- "解释一下 XX" / "对比 XX 和 YY"

### 2. 在新会话里调用

如果 Skill 没有自动被加载，**显式点名**即可：

```
请使用 .workbuddy/skills/concept-learning-skill/SKILL.md 处理：
"生成 Transformer 的学习笔记"
```

### 3. 用户级安装

想让该 Skill 跨项目可用：

```bash
# 把项目级 Skill 拷到用户级目录
mkdir -p ~/.workbuddy/skills/concept-learning-skill
cp .workbuddy/skills/concept-learning-skill/SKILL.md \
   ~/.workbuddy/skills/concept-learning-skill/

# 重启任何 WorkBuddy 会话，都会看到这个 Skill
```

### 4. 打包分发

用 Skill 配套脚本打包，便于发给团队：

```bash
scripts/package_skill.py .workbuddy/skills/concept-learning-skill ./dist
```

打包后会同时做格式校验，输出 `concept-learning-skill.zip`。

---

## ✅ 人工核查声明（Human-in-the-Loop）

本仓库中**所有由 AI 生成的内容**均经过人工核查与校对：

| 产出 | 核查人动作 | 核查重点 |
|------|-----------|---------|
| `.workbuddy/skills/concept-learning-skill/SKILL.md` | 人工对照 skill-creator 规范 | YAML schema / 触发词 / 5 段必备章节名 / 10 条自检清单 |
| `learning-materials/agent.html` | 人工对照 ReAct 论文、Anthropic Agents 文档 | ReAct 循环伪代码 / Plan-Execute-Reflect / 边界辨析反例 |
| `learning-materials/llm-context-window.html` | 人工对照 "Lost in the Middle" 论文、Transformer 论文 | KV Cache 复杂度公式 / "理论 ≠ 有效" 结论 |
| `learning-materials/skill.html` | 人工对照 Anthropic Skills 官方文档 | SKILL.md 文件结构 / 三级加载 / 与 Prompt/Tool/MCP/Fine-tuning 的区别 |
| `concept-relationship.md` | 人工在 Mermaid Live Editor 中渲染测试 | graph TD/LR / sequenceDiagram / 语法正确 |

> 🛑 **AI 可生成、可建议，但不做最终结论。** 任何对概念边界的判断（例如"Agent 与 Workflow 的本质区别"）都必须由人工基于一手资料确认。

### 后续 PR / 修改清单

每次对仓库做实质性修改前，请过一遍自检：

1. 不跳过 Skill 自身的 10 条 Self-Check
2. 改 Skill 时保留三件套：**5 段章节名 / 10 条自检 / 边界处理**
3. 添加新概念学习资料时，**同步**更新 `learning-materials/` 与 `concept-relationship.md`
4. 涉及技术细节（公式、API、命名）的修改，附上**人工校验过的来源链接**

---

## 🚀 当前进度 / 路线图

- [x] 落地 concept-learning Skill（项目级）
- [x] 沉淀第一批概念：Agent / 上下文 / Skill（HTML 三份）
- [x] 完成三概念关系图谱（`concept-relationship.md`）
- [ ] 概念库扩到 ≥ 10 个（RAG / Transformer / RLHF / Fine-tuning / Embedding / …）
- [ ] Skill 上架工作流：init_skill.py + package_skill.py 走通
- [ ] 用户级 Skill 安装与跨项目复用验证

---

## 📦 数据集说明 / Dataset

`iris_dataset.csv` 是一个经典的机器学习入门数据集（鸢尾花数据集），
共 150 条样本、4 个特征 + 1 个目标列，常用于分类任务演示。

### 字段

| 字段 | 类型 | 说明 |
| --- | --- | --- |
| sepal_length | float | 花萼长度（cm） |
| sepal_width  | float | 花萼宽度（cm） |
| petal_length | float | 花瓣长度（cm） |
| petal_width  | float | 花瓣宽度（cm） |
| species      | str   | 类别：setosa / versicolor / virginica |

> 后续课程作业可以直接替换为自己的数据集；保留此文件是为了让仓库首次 clone 后立即有可运行的数据。
