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

完整规则见 [SKILL.md](SKILL.md)。
