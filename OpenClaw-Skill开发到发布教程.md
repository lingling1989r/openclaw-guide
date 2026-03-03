# OpenClaw Skill 开发到发布全流程教程

> 从零开始开发一个 Skill 并发布到 ClawHub

---

## 什么是 Skill？

**Skill** 是 OpenClaw 的扩展包，就像手机的"应用"一样。一个 Skill 包含：

- `SKILL.md` — 配置文件，告诉 AI 什么时候用什么工具
- `scripts/` — 可选的脚本文件
- `references/` — 可选的参考资料
- `assets/` — 可选的静态资源

官方文档：https://docs.openclaw.ai/tools/skills

---

## 一、开发 Skill

### 1.1 创建 Skill 目录

在你的工作区创建一个新文件夹：

```bash
# 方式一：直接在 ~/.openclaw/workspace/skills/ 下创建
mkdir -p ~/.openclaw/workspace/skills/my-first-skill

# 方式二：在当前项目创建
mkdir -p ./skills/my-first-skill
```

### 1.2 编写 SKILL.md

这是核心文件，必须包含 **YAML frontmatter** 和 **说明文档**。

```markdown
---
name: my_first_skill
description: 一个简单的示例 Skill，用于演示开发流程。当你需要创建一个新的 Hello World Skill 时使用它。
metadata:
  {
    "openclaw": {
      "emoji": "👋",
      "requires": { "bins": ["echo"] },
    },
  }
---

# My First Skill

## 触发条件

当用户说"你好"或"打招呼"时，使用本 Skill。

## 使用方法

1. 使用 `echo` 工具输出问候语
2. 可以添加更多自定义逻辑

## 示例

- 用户说"你好" → 回复"Hello! 欢迎使用我的 Skill！"
```

### 1.3 SKILL.md 详解

#### Frontmatter 必填字段

| 字段 | 说明 | 示例 |
|------|------|------|
| `name` | Skill 名称（唯一标识） | `hello-world` |
| `description` | 触发描述（非常重要） | "用于处理..." |

#### metadata.openclaw 可选字段

```yaml
metadata:
  {
    "openclaw": {
      "emoji": "🎯",                          # 图标
      "os": ["darwin", "linux"],            # 支持的系统
      "requires": {
        "bins": ["python3"],                 # 必须存在的命令
        "env": ["API_KEY"],                  # 必须存在的环境变量
        "config": ["browser.enabled"],       # 必须开启的配置
      },
      "primaryEnv": "API_KEY",               # 主要的环境变量名
      "install": [                           # 自动安装器
        {
          "id": "brew",
          "kind": "brew",
          "formula": "python3",
          "bins": ["python3"],
        },
      ],
    },
  }
```

### 1.4 添加脚本（可选）

如果你需要运行特定脚本，可以在 Skill 目录下创建 `scripts/` 文件夹：

```
my-first-skill/
├── SKILL.md
└── scripts/
    └── hello.sh
```

```bash
#!/bin/bash
echo "Hello from Skill!"
```

### 1.5 添加参考资料（可选）

```
my-first-skill/
├── SKILL.md
├── scripts/
└── references/
    └── guide.md
```

在 SKILL.md 中引用：

```markdown
详细使用说明见 [指南](references/guide.md)
```

---

## 二、本地测试

### 2.1 刷新 Skill

创建或修改 Skill 后，需要刷新 OpenClaw：

```bash
# 重启 Gateway
openclaw gateway restart

# 或在对话中让 Agent 刷新
```

### 2.2 测试 Skill

```bash
# 通过 CLI 测试
openclaw agent --message "使用我的 my-first-skill"

# 或在飞书/微信中直接对话
```

---

## 三、发布到 ClawHub

**ClawHub** 是 OpenClaw 的公共 Skills 商店，网址：https://clawhub.com

### 3.1 安装 ClawHub CLI

```bash
# npm
npm i -g clawhub

# 或 pnpm
pnpm add -g clawhub
```

### 3.2 登录 ClawHub

```bash
# 浏览器登录（推荐）
clawhub login

# 或使用 Token 登录
clawhub login --token 你的TOKEN
```

### 3.3 发布 Skill

```bash
# 进入 Skill 目录
cd ./skills/my-first-skill

# 发布（需要先在网页注册 slug）
clawhub publish ./my-first-skill \
  --slug my-first-skill \
  --name "我的第一个 Skill" \
  --version 1.0.0 \
  --changelog "初始版本" \
  --tags latest
```

**参数说明：**

| 参数 | 说明 | 示例 |
|------|------|------|
| `--slug` | 唯一标识（URL 用） | `hello-world` |
| `--name` | 显示名称 | "Hello World" |
| `--version` | 语义化版本 | `1.0.0` |
| `--changelog` | 更新日志 | "修复了 xxx" |
| `--tags` | 标签 | `latest` |

### 3.4 同步发布（推荐）

如果你有多个 Skill，可以一键同步：

```bash
# 扫描并发布所有 Skills
clawhub sync --all

# 指定版本号类型
clawhub sync --all --bump minor
```

### 3.5 更新 Skill

```bash
# 修改代码后，重新发布
clawhub publish ./my-first-skill --version 1.0.1 --changelog "修复 bug"

# 或使用 sync 自动检测更新
clawhub sync --all --bump patch
```

---

## 四、常用命令汇总

| 命令 | 说明 |
|------|------|
| `clawhub search <关键词>` | 搜索 Skills |
| `clawhub install <slug>` | 安装 Skill |
| `clawhub update <slug>` | 更新 Skill |
| `clawhub update --all` | 更新所有 |
| `clawhub list` | 列出已安装 |
| `clawhub publish` | 发布 Skill |
| `clawhub sync` | 同步发布 |
| `clawhub whoami` | 查看当前用户 |

---

## 五、最佳实践

### 5.1 命名规范

- 使用小写字母、数字、短横线
- 例如：`hello-world`、`feishu-notify`、`pdf-generator`

### 5.2 Description 写法

Description 是 **最重要的触发机制**，要包含：

1. **功能描述** — Skill 做什么
2. **触发场景** — 什么时候使用
3. **使用示例** — 具体例子

```markdown
description: |
  用于处理飞书文档的创建、读取和更新。
  当用户说"创建飞书文档"、"读取文档"或"更新文档"时使用。
  示例：帮我写一篇公众号文章
```

### 5.3 版本管理

遵循语义化版本（SemVer）：

- `1.0.0` — 初始版本
- `1.0.1` — 修复 bug
- `1.1.0` — 新功能
- `2.0.0` — 破坏性更新

### 5.4 安全注意事项

- 不要在 Skill 中硬编码敏感 API Key
- 使用环境变量：`$MY_API_KEY`
- 优先在沙箱中运行不受信任的 Skill

---

## 六、完整示例：天气查询 Skill

### 目录结构

```
weather-skill/
├── SKILL.md
└── scripts/
    └── weather.sh
```

### SKILL.md

```markdown
---
name: weather
description: 查询指定城市的天气信息。当用户问"天气"、"多少度"、"下雨吗"时使用。
metadata:
  {
    "openclaw": {
      "emoji": "🌤️",
      "requires": { "env": ["OPENWEATHER_API_KEY"] },
      "primaryEnv": "OPENWEATHER_API_KEY",
    },
  }
---

# Weather Skill

## 功能

查询任意城市的当前天气。

## 使用方法

1. 从用户输入中提取城市名称
2. 调用 OpenWeather API 获取天气
3. 返回格式化后的天气信息

## 配置

需要在 openclaw.json 中配置 API Key：

```json5
{
  skills: {
    entries: {
      weather: {
        apiKey: "你的 OpenWeather API Key"
      }
    }
  }
}
```

## 示例对话

- 用户："北京天气怎么样？"
- Agent：使用 weather skill 查询并回复
```

### weather.sh

```bash
#!/bin/bash
CITY="$1"
API_KEY="$OPENWEATHER_API_KEY"

curl -s "https://api.openweathermap.org/data/2.5/weather?q=${CITY}&appid=${API_KEY}&units=metric"
```

---

## 七、常见问题

### Q: Skill 不生效？

1. 检查 `SKILL.md` 格式是否正确
2. 重启 Gateway：`openclaw gateway restart`
3. 查看日志：`openclaw doctor`

### Q: 发布失败？

1. 确保已登录：`clawhub whoami`
2. 检查 slug 是否已被占用
3. 查看错误信息

### Q: 如何更新已发布的 Skill？

```bash
# 修改代码后
clawhub publish ./my-skill --version 1.0.1 --changelog "更新内容"
```

---

## 参考资料

- 官方文档：https://docs.openclaw.ai/tools/skills
- ClawHub：https://clawhub.com
- AgentSkills 规范：https://agentskills.io

---

*更新于：2026-03-03*
