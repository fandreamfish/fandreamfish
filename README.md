# DSH 插件总指南 · fandreamfish

这个账号下有哪些 DeepSeek Harness 插件、各干什么、怎么装；插件产生的数据存在哪、换电脑怎么恢复。

**新电脑只报这一个账号，就能从这里把插件和数据全部找回来。**

## 插件一览

| 仓库 | 名字 | 干什么 | 安装规格 | 状态 |
|---|---|---|---|---|
| [dsh-memory](https://github.com/fandreamfish/dsh-memory) | 记忆 | 会话总结 → 场景 → 全局的三层长期记忆 | `github:fandreamfish/dsh-memory` | 骨架 |
| [dsh-persona](https://github.com/fandreamfish/dsh-persona) | 人格 | 给 AI 伙伴一套可切换的人设 | `github:fandreamfish/dsh-persona` | 骨架 |
| [dsh-mood](https://github.com/fandreamfish/dsh-mood) | 心情 | 会自己判断、看得见的心情值 | `github:fandreamfish/dsh-mood` | 骨架 |
| [dsh-bili](https://github.com/fandreamfish/dsh-bili) | 刷B站 | 派子代理看视频、挑值得的汇报回来 | `github:fandreamfish/dsh-bili` | 骨架 |
| [dsh-speak-up](https://github.com/fandreamfish/dsh-speak-up) | 主动搭话 | 没人说话时自己掷骰子开口 | `github:fandreamfish/dsh-speak-up` | 骨架 |

> 状态「骨架」= 能安装、能被 DSH 加载，功能尚未实现。

## 安装（不需要 token，插件仓都是公开的）

```bash
# 装一个
dsh plugin --profile desktop add github:fandreamfish/dsh-memory

# 全装
dsh plugin --profile desktop add github:fandreamfish/dsh-memory
dsh plugin --profile desktop add github:fandreamfish/dsh-persona
dsh plugin --profile desktop add github:fandreamfish/dsh-mood
dsh plugin --profile desktop add github:fandreamfish/dsh-bili
dsh plugin --profile desktop add github:fandreamfish/dsh-speak-up
```

装完重启 DSH（或热加载）。

## 大脑：[dsh-brain](https://github.com/fandreamfish/dsh-brain)（私有）

插件代码在上面；**插件的数据和「这台电脑到底在用哪一版插件」在 `dsh-brain` 里**。

| 分支 | 内容 |
|---|---|
| `main` | 模板：结构、规范、插件锁格式、恢复说明、工具。不含任何人的数据 |
| `person/<名字>` | 某个人的大脑：`plugins/lock.json`（地址/分支/commit/版本）+ `data/<插件>/` 数据 |

仓库里有两样东西：

- `plugins/lock.json` —— 每个插件的仓库地址、分支、**commit sha**、版本、安装规格、配置补丁
- `data/<plugin-id>/` —— 各插件在这台电脑上产生的数据，**与机器无关**（不写绝对路径、不含凭据）

## 换机恢复

1. **装插件**（不需要 token）：按上面的命令装
2. **取数据**（需要 token：fine-grained PAT，只给 `dsh-brain` 的 Contents 读写）
   ```bash
   git clone https://github.com/fandreamfish/dsh-brain "$env:USERPROFILE\dsh-brain"
   cd "$env:USERPROFILE\dsh-brain"
   git checkout person/default
   node tools/verify.mjs      # 必须先 OK：结构、哈希、机器无关、无凭据
   node tools/import.mjs --apply
   ```
3. **启动并登录** DeepSeek 账号（凭据不入库），问它「你记得我吗」

详见 `dsh-brain` 里的 `RESTORE-FOR-AI.md`（写给新电脑上的 AI 看的）。

## 约定

- 一个插件一个仓，包名 = 仓名；`package.json` 的 `dsh.bundle.patch` 指向本仓 `cordis.patch.yml`
- 插件入口 `lib/index.js`：`name` / `inject` / `apply(ctx, config)`
- 每轮都变的内容用 `systemPrompt.context()`；`section()` 会废掉系统提示词缓存
- **凭据永不入库**（账号 token、B 站 cookie、PAT）；数据仓只收「和电脑无关」的记忆
- 大脑不懂数据内容：只搬运与校验，插件自己定义格式

## 机器可读清单

见 [`plugins.json`](./plugins.json)。

## 许可

MIT
