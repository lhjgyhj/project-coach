# project-engineer

本仓库正在分阶段制作 `project-engineer`：人工负责决策与验收，AI 实施已批准的小功能，维护项目地图、测试证据、交接记录和跨项目共享知识。

当前版本为 `0.1.0`，仅交付 M01「入口及旧版停用」，已获用户归档合并与推送授权。详细规范、项目模板和完整行为自检尚未交付，不能将当前版本视为最终完整 Skill。

## 当前入口

- [SKILL.md](SKILL.md) 是唯一活动入口。
- 原带教 Skill 与原使用说明逐字节保存在 `legacy/project-coach.skill.txt`、`legacy/project-coach.readme.txt`，可从 Git 历史和归档恢复。它们是历史资料，不作为当前 AI 指令加载。
- 共享知识库继续使用根目录现有的 [knowledge/](knowledge/)；不会创建第二套知识库。

## 安装与入口

采用 [Agent Skills 规范](https://agentskills.io/specification)的 YAML frontmatter 和 `SKILL.md` 入口，不绑定语言、框架、操作系统或绝对路径。仓库名仍为 `project-coach`；安装目录名应为 `project-engineer`，与 frontmatter 的 `name` 一致。

可先在普通目录中检查内容。当前版本尚在制作，不建议替换用于正式工作的完整 Skill。最终安装时，将**整个仓库**放入目标平台实际支持的 skills 目录，目录命名为 `project-engineer`。例如按照 [Codex 官方说明](https://developers.openai.com/codex/skills/)配置用户级入口：

```powershell
git clone https://github.com/lhjgyhj/project-coach.git "$HOME/.agents/skills/project-engineer"
```

仅在目标目录不存在时执行。其他平台先核实其 Skill 发现机制、入口及路径，不直接套用 Codex 路径。本工作区现有配置使用 `.codex/skills`，应以实际宿主说明及当前设置为准。

显式调用示例：`使用 project-engineer，调查这个项目并提交当前小功能地图，先不要编码。` 支持自动匹配的宿主也可根据入口描述选择本 Skill；启用本身不授予编码、删除、重构或推送权限。

## 共享知识库与更新

唯一知识库是本仓库根目录 `knowledge/`。本次保留其协议、模板、索引及全部条目，不创建副本、不迁移分类、不重新宣称旧条目已经核实。后续恢复旧带教模式也使用该目录。

跨项目使用同一可写 Skill 检出目录，不把 `knowledge/` 复制进业务项目；多台设备通过 Skill 仓库单独同步。更新前检查本地修改，知识条目冲突必须合并处理，不能用强制重置覆盖。远程推送仍需明确授权。

现有知识维护协议含带教流程措辞，统一调整属于后续知识库模块；当前入口明确排除其中的带教提示、手动触发限制和 Git 暂存建议。当前仅执行实际有候选内容的核对和入库，不为凑数量增加条目。

## 旧 Skill 停用与恢复

根入口包含停用注释，归档使用 `.txt` 而非 `SKILL.md`，避免标准 Skill 扫描器将旧版识别为独立 Skill。注释和扩展名不是文件访问控制：AI 若被明确要求打开原始归档仍可读取，因此当前入口还明确禁止将历史资料当作规则加载。

需要恢复时先备份当前入口并提出恢复方案；用户批准后，将归档原文恢复为旧 Skill 的入口，同时保持同一 `knowledge/`。不要在未确认的情况下同时启用工程与带教两套冲突规则。

## 当前文件与后续模块

当前文件职责：`SKILL.md` 为主入口；本文件为使用与迁移说明；`legacy/` 为只读历史资料；`knowledge/` 为现有共享资产；`ai痕迹/` 保存本次制作的地图、授权和检查记录。

按已批准地图，后续逐模块制作审批规则、项目地图与恢复、编码与数据安全、测试体系、知识库协议、自检及完整说明。`references/`、`templates/` 和 `checks/` 尚未创建，不把计划文件作为已可用资源引用。

本模块地图、变更和真实自检结果见 [M01 记录](ai痕迹/M01-入口与旧版停用.md)。
