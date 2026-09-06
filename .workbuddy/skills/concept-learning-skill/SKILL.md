---
name: concept-learning
description: This skill should be used when the user provides a new concept (term, methodology, theory, algorithm, framework, etc.) and needs structured learning material. Produces a standardized Markdown study document with five mandatory sections — personal explanation (个人解释), core mechanism (核心机制), application scenarios (应用场景), boundary discrimination (边界辨析), and source links (来源链接). Use when the user asks things like "解释一下XX"、"XX 是什么"、"帮我整理 XX 的学习笔记"、"对比 XX 与 YY"、"什么是 XX, 想深入了解一下".
agent_created: true
---

# 概念学习资料生成

A reusable workflow that turns **any new concept** into a structured learning
document. Input is a single concept name (with optional context); output is a
five-section Markdown study note that is both human-readable and machine-tractable.

## When to Use This Skill

Trigger this skill in any of the following situations:

- The user encounters an unfamiliar term while reading papers, documentation, or source code and wants a quick orientation
- The user explicitly asks to "explain / organize / learn" a concept in a note-taking style
- The user wants to compare one concept with adjacent/look-alike concepts and clarify their boundaries
- The user wants to convert scattered notes into a reusable, well-structured knowledge card
- The user asks for "概念学习资料 / 学习笔记 / 知识卡片 / study notes for XX"

Do **not** trigger this skill when:

- The user only wants a one-sentence definition (just answer directly)
- The user is asking a factual lookup question without intent to learn the concept
- The user already provided a complete concept document and only wants reformatting

## Input Contract

Collect the following inputs before generation. Anything not provided should be
filled in with sensible defaults; **only the concept itself is strictly required**.

| Field | Required | Description | Default |
|-------|----------|-------------|---------|
| `concept` | ✅ | Standard name of the concept (Chinese and/or English). Aliases welcome. | — |
| `depth` | optional | One of `brief` / `standard` / `deep`. Controls verbosity of mechanisms and depth of math. | `standard` |
| `audience` | optional | One of `beginner` / `engineer` / `researcher` / `general`. Tunes vocabulary level. | `general` |
| `focus` | optional | Aspect to emphasize, e.g. `historical origin`, `math form`, `engineering impl`, `vs {peer}`. | auto |
| `comparison_peers` | optional | List of adjacent concepts to compare against in boundary section. | auto-pick 2–4 from sources |

If only `concept` is given, treat it as the standard query and proceed with defaults.
If `concept` is missing or ambiguous, ask one clarifying question — never guess.

## Generation Steps

Follow these steps in order. Skip a step only when inputs are missing AND a
sensible default cannot be inferred.

1. **Concept scoping.** Identify the concept's domain (CS / economics / biology / humanities / math / engineering / etc.) and where it sits in the larger knowledge map. State this briefly before proceeding.
2. **Source retrieval.** Search authoritative sources for the concept — academic papers, official documentation, textbooks, well-known technical blogs, encyclopedias (Wikipedia, Stanford Encyclopedia of Philosophy, Britannica). Use at least **two distinct source types** and prefer **2020+** material for tech concepts (classical theories may cite the original paper).
3. **Personal explanation (个人解释).** Produce a 150–300 character, analogy-driven, jargon-light explanation aimed at the target audience. The reader should be able to retell the concept in their own words after reading.
4. **Core mechanism (核心机制).** Extract the underlying "why it works / what problem it solves" — key terms, the causal chain, and, when relevant, the 1–3 most critical formulas or algorithm steps. Depth scales with `depth` input.
5. **Application scenarios (应用场景).** List at least **three** distinct scenarios. For each, give: setting → the role of the concept → a concrete micro-example. Avoid repeating the same scenario with different words.
6. **Boundary discrimination (边界辨析).** For each `comparison_peer` (or auto-picked peer), articulate the **single sharpest distinction** in one sentence, **plus one verifiable counter-example** that exposes the difference.
7. **Source links (来源链接).** Consolidate 3–8 sources ranked by authority. Each entry must include: link, source type (paper / official doc / textbook / encyclopedia / blog / community), and one-line description of what it contributes.
8. **Self-check.** Run the checklist in the next section before delivering.

## Output Structure

Output **must** be a single Markdown document with the following sections, in
the listed order. Section names are part of the contract — do not rename them.

````markdown
# {概念名} 学习资料

> 一句话定义：(≤ 30 字的精炼定义)

## 1. 个人解释
(类比、白话、面向 {audience} 的非专业讲解，150–300 字)

## 2. 核心机制
(关键术语 + 因果链条 + 必要时 1–3 个核心公式或算法步骤)

## 3. 应用场景
(≥ 3 条；每条: 场景设定 → 概念扮演角色 → 简明示例)

| 场景 | 概念的作用 | 示例 |
|------|------------|------|
| ... | ... | ... |

## 4. 边界辨析
(与 {peer_1}/{peer_2}/... 的核心区分 + 反例验证)

| 区分点 | {本概念} | {相邻概念} |
|--------|-----------|------------|
| ... | ... | ... |

## 5. 来源链接
(按权威度排序，每条标注: 链接 / 类型 / 关键贡献)

1. [标题](#url) — {类型} — {一句话贡献}
2. ...
````

Use Markdown tables when ≥ 3 items exist in a section. Do not introduce extra
top-level sections; sub-sections inside a section are allowed.

## Source Quality Requirements

Sources must satisfy **all** of the following:

1. **Authority first.** Prefer original papers, official documentation, textbooks, and authoritative encyclopedias. Personal blogs are acceptable only as supplementary support.
2. **Type diversity.** Single-type sources (e.g., all blogs) are not allowed. Include at least two different source types.
3. **Recency.** For technical concepts, prefer 2020+ sources. For classical theory, original-paper citation is acceptable and encouraged.
4. **Accessibility.** Every URL must be verified to open. Strip 404 / redirected-to-irrelevant / pay-walled-with-no-abstract links.
5. **Quantity.** Keep between 3–8 sources. More dilutes focus; fewer lacks cross-validation.
6. **Neutrality.** For contested concepts, include at least one source representing the opposing view.

If no authoritative source can be found for an obscure concept, state that
honestly and offer the best available evidence with caveats — do not fabricate.

## Self-Check Requirements

Before delivering the output, verify **every** item below. If any item fails,
fix the output before sending it to the user.

1. The document contains all five mandatory sections, named exactly: 个人解释 / 核心机制 / 应用场景 / 边界辨析 / 来源链接
2. The 一句话定义 is present and ≤ 30 characters
3. 个人解释 can be understood by a reader in the target `audience` without external lookup; uses an analogy or concrete scenario
4. 核心机制 includes at least: key terminology + causal chain; if math/algorithm is central, includes 1–3 critical formulas/steps
5. 应用场景 contains ≥ 3 entries, mutually distinct, each with scenario + role + example
6. 边界辨析 covers ≥ 2 adjacent concepts; each distinction has a verifiable counter-example
7. 来源链接 contains 3–8 entries, all links open successfully, ≥ 2 distinct source types represented
8. No placeholder text remains (`TBD`, `待补充`, `示例`, `…`); unfinished parts must be **explicitly stated**, not silently omitted
9. Document length is proportional to `depth`: brief ~1 page, standard ~2–3 pages, deep ~4+ pages
10. Output language matches the user's input language (Chinese input → Chinese output, unless requested otherwise)

## Edge Cases & Rules

- **Generic concept (e.g. "人工智能", "经济学").** Treat as a top-level umbrella, briefly acknowledge breadth, then pick 2–3 representative sub-aspects for the mechanism section; otherwise follow the standard flow.
- **Multiple concepts in one query.** Ask once: "generate one document per concept, or one comparison document?" Default to comparison document for ≥ 2 concepts.
- **Concept is a proper noun / product / paper title.** Then organize around: what it is, what problem it solves, key innovations, how it differs from prior work, sources.
- **Concept is in a language other than the user's.** Default to bilingual key terms (preserve original term, add Chinese gloss); explanations in the user's language.
- **Cross-domain concept.** Note the home domain, then explain the cross-domain meaning explicitly under 边界辨析.
- **No comparable peer is meaningful.** State "本概念暂无主流易混相邻概念" and provide a contrast with the concept's *predecessor* or *parent* concept instead.
- **User requests an additional section** (e.g. "加一个例子"). Append it as a sub-section under the most fitting existing section, do not create new top-level sections.

## Bundled Resources

None required for v1. If a future version needs templating, place reusable
Markdown skeletons under `assets/`.
