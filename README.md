# ShortcutStudio

Surge / Loon / Stash / Quantumult X 通用。

---

## 订阅地址

**Surge / Egern**

https://raw.githubusercontent.com/Yu9191/shortcutstudio-rewrite/refs/heads/baby/modules/ShortcutStudio.sgmodule

**Quantumult X**

https://raw.githubusercontent.com/Yu9191/shortcutstudio-rewrite/refs/heads/baby/modules/ShortcutStudio.qx.conf

**Loon**

https://raw.githubusercontent.com/Yu9191/shortcutstudio-rewrite/refs/heads/baby/modules/ShortcutStudio.lpx

**BoxJS**

https://raw.githubusercontent.com/Yu9191/shortcutstudio-rewrite/refs/heads/baby/modules/ShortcutStudio.boxjs.json

> Stash / Shadowrocket 等：用 [Script-Hub](https://github.com/Script-Hub-Org/Script-Hub) 把 `.sgmodule` 转换后订阅。

---

<details>
<summary><b>功能</b></summary>

- 思考模式：改写 `generate` 请求的 `thinkingModeSelection` / `thinkingMode` / `enableReasoning` / `reasoningEffort`（`auto` / `on` / `off`）
- 搜索模式：改写 `generate` 请求的 `searchWebMode`（默认 `on`）
- 设备安装 ID：为请求注入 `X-Device-Installation-ID`，按「复用次数」轮换（默认 2 次后切换新 ID）
- sign 接口替换（可选）：`off` 时不改 `/sign` 的设备 ID，减少对签名/隐私敏感请求的影响；`on` 时对 `/sign` 也替换
- limits 响应 mock（默认 `off`）：`on` 时仅修改本机 App 看到的 UI 显示额度（消息 / 下载 / 搜索 / 上传 / 市场下载 / 输入字数），**不改变服务端真实额度**

</details>

<details>
<summary><b>BoxJS 配置项</b></summary>

BoxJS 订阅链接：

https://raw.githubusercontent.com/Yu9191/shortcutstudio-rewrite/refs/heads/baby/modules/ShortcutStudio.boxjs.json

| Key | 含义 |
| --- | --- |
| `ShortcutStudio.thinkingMode` | 思考模式：`auto` / `on` / `off` |
| `ShortcutStudio.searchWebMode` | 搜索模式：`on`（默认）/ `auto` / `off` |
| `ShortcutStudio.deviceIdReuseLimit` | 设备 ID 复用次数（默认 2） |
| `ShortcutStudio.signRewrite` | sign 接口替换：`off` / `on` |
| `ShortcutStudio.limitsMock` | limits 响应 mock：`off`（默认）/ `on` |
| `ShortcutStudio.messagesLimit` | 消息额度（mock=on 生效） |
| `ShortcutStudio.downloadsLimit` | 下载额度（mock=on 生效） |
| `ShortcutStudio.searchesLimit` | 搜索额度（mock=on 生效） |
| `ShortcutStudio.uploadsLimit` | 上传额度（mock=on 生效） |
| `ShortcutStudio.marketplaceDownloadsLimit` | 市场下载额度（mock=on 生效） |
| `ShortcutStudio.chatInputCharsLimit` | 输入字数（mock=on 生效） |
| `ShortcutStudio.logLevel` | 日志级别：`off` / `error` / `warn` / `info` / `debug` / `all` |

> 生效顺序（对齐 [@nsnanocat/util](https://github.com/NSNanoCat/util) 约定）：默认值 → 模块 `$argument` → BoxJS 持久化，**BoxJS 优先级最高**。未在 BoxJS 设置的项保持模块参数/默认值。
>
> Quantumult X 用户的设置 **只能走 BoxJS**（rewrite 段不支持 argument）。Surge / Loon 既可在模块 `arguments` / `[Argument]` 里改，也可走 BoxJS（BoxJS 会覆盖模块里的同名项）。

</details>

<details>
<summary><b>项目结构</b></summary>

```text
shortcutstudio-rewrite/
├── package.json
├── rollup.config.js
├── src/
│   ├── request.js         请求入口（rollup 打包到 dist/request.js）
│   ├── response.js        响应入口（rollup 打包到 dist/response.js）
│   └── process/           请求 / 响应处理逻辑（Request.mjs / Response.mjs）
├── dist/                  打包产物，被 script-path 直接引用
├── modules/              各平台模块文件（sgmodule / lpx / qx.conf / boxjs.json）
└── resources/icon.png    模块与 BoxJS 图标
```

</details>

<details>
<summary><b>本地构建</b></summary>

```bash
npm install
npm run build      # 生成 dist/request.js、dist/response.js
npm run build:dev  # 生成 dev 版本
npm test           # 运行测试
```

产物 `dist/request.js` 与 `dist/response.js` 直接被 `script-path` 引用：

```text
https://raw.githubusercontent.com/Yu9191/shortcutstudio-rewrite/refs/heads/baby/dist/request.js
https://raw.githubusercontent.com/Yu9191/shortcutstudio-rewrite/refs/heads/baby/dist/response.js
```

</details>

<details>
<summary><b>二次开发</b></summary>

- 改请求改写逻辑：`src/process/Request.mjs`（思考 / 搜索模式、设备 ID 轮换、sign 替换）
- 改额度 mock 逻辑：`src/process/Response.mjs`（`applyQuota` 各额度字段）
- 新增/调整参数：更新 `DEFAULT_ARGUMENT` 与 `BOXJS_ARGUMENT_KEYS`，并同步各模块的 `arguments` / `[Argument]` 与 `modules/ShortcutStudio.boxjs.json`
- 改完 `src/` 后需 `npm run build` / `npm run build:dev` 重新生成 `dist/`

</details>
