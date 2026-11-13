# project-coach

基于项目开发报告的编程实战带教 skill。读取 `doc/` 下的项目文档，拆成里程碑和任务卡，引导用户亲手写出项目代码，逐步撤掉提示，直到能脱离 AI 独立开发。适用于任何语言和框架。

## 安装

把本仓库放到 ZCode 的用户 skills 目录：

```bash
git clone git@github.com:lhjgyhj/project-coach.git ~/.zcode/skills/project-coach
```

Windows 路径：`C:\Users\<你的用户名>\.zcode\skills\project-coach`

## 使用

1. 把项目开发报告放进项目根目录的 `doc/`。
2. 对 AI 说：「使用 project-coach，开始带教」，并说明这次用的语言/框架。
3. AI 会自动创建 `doc/`、`AI痕迹/`，搭好项目骨架，然后从第一张任务卡开始。
4. 换电脑后：先 `git pull`，再说「使用 project-coach，继续带教」。

## 个人代码库

除了带教，本 skill 还维护一份跨项目复用的代码知识库，放在本仓库的 `knowledge/` 下，
与带教项目的 `doc/`、`AI痕迹/` 无关。

怎么入库：

1. 把常用 API、函数或代码片段发给 AI，说「入库」「收进代码库」或「维护代码库」。
2. AI 先核对官方文档，再按 `knowledge/INDEX.md` 去重，最后按 `knowledge/_TEMPLATE.md` 写入条目并更新索引。
3. 用户给的默认值有错时，AI 会先纠正再入库，并列出「你写的是什么 → 实际是什么 → 依据」。

目录结构：

```
knowledge/
├── PROTOCOL.md              # 维护协议（三步流水线）
├── INDEX.md                 # 总索引
├── _TEMPLATE.md             # 新条目模板
└── {语言}/{库或主题}/{符号}.md
```

带教时做 L2「该用哪个函数」提示，AI 会先查 `knowledge/INDEX.md`，命中就直接指向条目。
完整规则见 [knowledge/PROTOCOL.md](knowledge/PROTOCOL.md)。

完整规则见 [SKILL.md](SKILL.md)。
