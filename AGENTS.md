## 会话管理
地图、工单、规格承担分解、收束和交接工作。
如果会话内容和接下来的工作，大幅超过当前认领工单的边界，不要在同一个会话下讨论额外话题，提示用户新建工单。

## 仓库改动
涉及领域概念、关系、状态、不变量，严格遵守domain-modeling skill
设计接口形状、模块深度或 seam 位置，严格遵守codebase-design skill

## 禁止过度工程化
当直接完成任务便宜、可逆时，禁止随意增加没有收益的环节，如”首轮验证“、“判断方向值不值得做”。
哈希、独立备份、回滚演练及全量验收这类环节，必须提示用户批准，通过后才加入流程。
禁止以改进极小错误案例为推进方向，浪费时间榨取0.01%的优化空间。

## 用户质疑
当用户提出质疑，意思是AI与用户间的认知不一致，不代表用户绝对正确。
如果AI犯错，禁止以反思、复述错误收尾而不有效推进；应该明确结束边界内的话题，如有新话题或需要重启则提示用户。

## 回复/写作
禁止防御性写作，禁止习惯性辩解。
禁止无有用信息的：不能/不是/没有...句式。

## Agent skills

### Issue tracker

Issues and specs are tracked locally as Markdown files under `.scratch/`. See `docs/agents/issue-tracker.md`.

### Triage labels

The five default triage labels are used as local status values. See `docs/agents/triage-labels.md`.

### Domain docs

Single-context layout: root `GLOSSARY.md` and `docs/adr/`. See `docs/agents/domain.md`.
