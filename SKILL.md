---
name: codex-glm
description: 使用个人 AO 配置让 Codex 拆解和审查、OpenCode GLM 实现任务。用于用户要求 Codex 当大脑、GLM 当手脚或用 AO 执行新任务。
---

# Codex + GLM

个人工具 AO 0.13.0 已安装，命令 `~/.local/bin/ao`。配置和状态在 `~/.ao`，不向业务仓库写调度配置。本机当前项目注册名为 `inspiralclimb-personal`，路径 `/Users/lazy/Code/crack/InspiralClimb`，基线 `develop_mvp`。其他项目先 `ao project ls`，按实际路径注册个人配置，不能沿用该项目名或基线。

1. 读取适用项目规则并检查 Git 状态；保留现有修改。先 `ao status` 确認 daemon 就绪。未运行时打开个人 Applications 中的 Agent Orchestrator；若桌面启动不可用，可使用该版本已确认的 `ao daemon` 启动后台服务，日志留在 `~/.ao`。不要重复启动。
2. 当前 Codex 会话可直接充当大脑：给一个 GLM worker 精确任务，包含目标、允许修改路径、验收条件与报告格式。用 `ao spawn --help` 确认参数，使用 `--project <id> --agent opencode --mode chat --model litellm/glm-5.3 --name <简短名> --prompt <任务>`。一次一个 worker。业务执行在 AO 的独立 worktree 中；不要把未提交代码默默复制过去。
3. 用 `ao session --help`、`ao send --help` 查当前版本的观察和消息命令。检查真实 diff 和证据，Codex 自己审查；最多一次定向返工。额度不足或调用错误就报告并保留现场，当前不自动换其他模型。
4. 用户若希望在 AO 独立继续，可启动 `ao spawn --project <id> --kind orchestrator --agent codex --mode chat --name <简短名> --prompt <任务>`；不要同时让当前会话和 AO 总控重复派发同一任务。

用户已明确让 Codex 审查，勿按旧分工另增 Kimi。公共规则、依赖/锁文件、构建/CI、共享接口与数据库契约需要修改时先解释必要性及同事影响。个人文件不进业务仓库。提交、推送、PR和部署需要本次单独授权。`review trigger` 是 PR 审查，不因使用本 skill 自动创建或发布 PR。

配置完成不等于任务执行成功；首次新任务仍要确认 worker 真正启动及最终产物。

用户最新偏好：默认固定 Codex 总控/审查 + OpenCode litellm/glm-5.3 实现，停止使用 Grok。接续任务先检查已有会话，勿重复派发。同一任务只一个worker。

运行经验（AO 0.13.0）：`ao send` 在 worker 忙时可能仅排队；发送成功不等于 worker 已收到纠偏。审查意见集中成一次反馈，先确认回合 ready 再发送。必须立即纠偏时，当前版本官方客户端使用 `POST /api/v1/sessions/{id}/conversation/interrupt`（本机 daemon）；中断前保存排队消息，中断后确认 ready，再把所有尚未处理意见合并重发。中断可能清掉排队消息。不要重复轮询完整对话或输出模型推理；只查看状态、工具结果和实际 diff。验收使用指定文件，命令不得用 `tail` 等管道遮蔽真实退出码。
