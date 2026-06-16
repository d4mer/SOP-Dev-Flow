# Routing Rules

| Task Type | Model | Notes |
| --- | --- | --- |
| Planning / architecture / high risk | openai/gpt-5.4 | planner model; fallback to `minimax-coding-plan/MiniMax-M2.7` |
| Implementation / refactor / fixes | omlx/qwen3.6-35b | worker model |
| Planner fallback | minimax-coding-plan/MiniMax-M2.7 | escalate when the planner is blocked |
| Review | openai/gpt-5.4 | reviewer model |
| Security | omlx/qwen3.6-35b | security model |
| QA verification | opencode/minimax-m2.5-free | validation |
| Ops / heartbeat | omlxmini/qwen3.5-9b | summaries |
