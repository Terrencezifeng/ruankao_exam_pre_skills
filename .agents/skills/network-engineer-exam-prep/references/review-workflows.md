# 入口检查与每周复盘

## 入口检查触发条件

每个新项目会话首次使用本 Skill 时读取 `study/review-state.md`、`study/mistakes.md` 和当前周计划。满足任一条件就执行入口复盘：

1. 当前 ISO 周不同于 `last_entry_week`；
2. 存在复习日期不晚于今天、状态不是“已完成”或“稳定”的错题；
3. `last_entry_check` 之后新增了高风险错题；
4. 当前周任务已过计划日期但仍未开始，或剩余任务按现有预算无法在周内完成。

同一周没有新证据、没有新到期项时，只更新必要状态，不重复输出旧建议。用户正在询问一道具体题时，先完成题目解析，再追加入口复盘，避免打断当前任务。

## “当前最优先的 3 个问题”

每次恰好给 3 项，按以下顺序取材：

1. 已逾期或今天到期的错题；
2. 重复出错、掌握度低于 2、影响应用技术作答或导致某科估分低于 50 的知识域；
3. 本周尚未完成的高收益任务；
4. 证据不足时，用当前阶段必需的诊断或模拟任务补位，明确写“尚无薄弱点证据”，不得虚构错误。

每项包含：

- **问题**：知识点、题型或计划风险；
- **证据**：错题 ID、掌握度、估分、未完成任务或“尚无诊断证据”；
- **为什么现在调整**：与到期日期、45 分底线或本周剩余时间的关系；
- **建议动作**：一个可以直接开始的练习；
- **复习入口**：存在对应项目笔记时，链接到最小相关章节；没有对应笔记时省略，不虚构路径；
- **时间**：建议分钟数。

三项建议时间之和必须能纳入当周 240 分钟预算。若加入新任务，指出替换或缩短的原任务。

## 每周自动复盘

每周自动任务读取：

- `study/profile.md`
- `study/weekly-plan.md`
- `study/mistakes.md`
- `study/time-log.md`
- 最近 7 天的 `study/sessions/*.md`
- `study/review-state.md`

按以下结构输出并写回项目：

1. **本周完成情况**：从 `time-log.md` 汇总实际投入、完成任务、已证明掌握的内容；没有时长证据就写“未记录”，不能猜。用户只提供时长下限时保留下限表达，不换算为精确值。
2. **薄弱点**：最多 3 项，必须附错题、掌握度、模拟估分或无法作答的证据。
3. **到期错题**：列出逾期和未来 7 天到期项；若为空则明确写“无”。
4. **下周方向**：恰好 3 个优先问题，使用入口检查的字段格式。
5. **下周 4 小时安排**：总计不超过 240 分钟，包含学习/训练和复盘；新增任务必须替换低收益项。

写入规则：

- 合并更新 `study/weekly-plan.md`，保留历史完成记录和用户手工备注。
- 需要调整掌握度或复习状态时，按实际证据更新 `profile.md` 与 `mistakes.md`。
- 在当天 `sessions` 文件中记录“每周复盘”，但不把自动复盘本身计为用户学习时长。
- 更新 `review-state.md` 的 `last_weekly_review`、`last_entry_check`、`last_entry_week`、触发原因和三个推荐键。
- 若一周没有任何学习证据，不批评或虚构进度；指出缺失证据，并把下一步压缩为最小可执行诊断。

## `review-state.md` 格式

保留以下字段，日期时间使用 Asia/Shanghai：

```text
last_entry_check: YYYY-MM-DD
last_entry_week: YYYY-Www
last_weekly_review: YYYY-MM-DD 或 never
next_scheduled_review: YYYY-MM-DD HH:mm
last_trigger: weekly-boundary | due-mistake | new-high-risk | schedule-risk | automation | initialization
last_priority_keys: [key-1, key-2, key-3]
```

`last_priority_keys` 使用稳定、可读的标识，例如错题 ID、知识域加原因、或周计划任务名，用于判断是否出现了新证据。
