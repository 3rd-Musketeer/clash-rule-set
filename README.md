# clash-rule-set

个人 Mihomo / Clash 私有 rule-set 补丁。专门覆盖**公共 ruleset 漏掉**的高频
域名 —— 公共底盘走 MetaCubeX / DustinWin，这里只放补丁。

## 文件分类

| 文件 | behavior | 命中后去哪 | 用途 |
|---|---|---|---|
| `ai-cn.list` | domain | DIRECT | 国产 AI（Kimi/DeepSeek/豆包等），必须直连，避免被误判走代理 |
| `ai-overseas.list` | domain | 🤖 AI 服务 | 海外 AI 服务（公共 geosite 漏掉的） |
| `dev-saas.list` | domain | 🛠 工具 | Figma/Linear/Notion/Vercel/Supabase 等 |
| `cn-extra.list` | domain | DIRECT | 国内服务补丁（WPS/QCC/学校等） |
| `personal.list` | domain | 🚀 节点选择 | 自己 / 朋友的项目 |

> 机场后台域名、自有服务器 IP 等不放进本仓库（避免 fingerprinting），
> 直接 inline 到本地 Verge 的 `Merge.yaml` 里。

## 在 Mihomo 配置里引用

```yaml
rule-providers:
  my-ai-cn:
    type: http
    behavior: domain
    format: text
    url: https://raw.githubusercontent.com/3rd-Musketeer/clash-rule-set/main/ai-cn.list
    path: ./rule-providers/my-ai-cn.txt
    interval: 21600   # 6h

  my-ai-overseas:
    type: http
    behavior: domain
    format: text
    url: https://raw.githubusercontent.com/3rd-Musketeer/clash-rule-set/main/ai-overseas.list
    path: ./rule-providers/my-ai-overseas.txt
    interval: 21600

  my-dev-saas:
    type: http
    behavior: domain
    format: text
    url: https://raw.githubusercontent.com/3rd-Musketeer/clash-rule-set/main/dev-saas.list
    path: ./rule-providers/my-dev-saas.txt
    interval: 21600

  my-cn-extra:
    type: http
    behavior: domain
    format: text
    url: https://raw.githubusercontent.com/3rd-Musketeer/clash-rule-set/main/cn-extra.list
    path: ./rule-providers/my-cn-extra.txt
    interval: 21600

  my-personal:
    type: http
    behavior: domain
    format: text
    url: https://raw.githubusercontent.com/3rd-Musketeer/clash-rule-set/main/personal.list
    path: ./rule-providers/my-personal.txt
    interval: 21600

rules:
  # 第一层 — 私有补丁（强覆盖）
  # （机场后台 / ECS 等敏感 inline 规则在本地 Merge.yaml 中追加）
  - RULE-SET,my-ai-cn,DIRECT
  - RULE-SET,my-ai-overseas,🤖 AI 服务
  - RULE-SET,my-dev-saas,🛠 工具
  - RULE-SET,my-cn-extra,DIRECT
  - RULE-SET,my-personal,🚀 节点选择

  # 第二、三层 — 公共 ruleset + 大盘兜底（见主 config.yaml）
```

## 维护节奏

- 每次发现"某个常用域名走错组了"，加一条到对应 list，git commit + push
- Mihomo 自动每 6h 拉一次更新，重启代理立刻生效

## 转 mrs（可选优化）

text 格式 Mihomo 加载稍慢但完全可用。如果想转 mrs 提速：

```bash
mihomo convert-ruleset domain text ai-cn.list ai-cn.mrs
```

之后配置改成 `format: mrs`、url 改成 `.mrs` 文件即可。可以用 GitHub Actions
自动转换，见 `.github/workflows/build-mrs.yml`（暂未提供，按需添加）。
