# ChatGPT Session to CPA / sub2api / Cockpit / 9router / Codex / AxonHub / Codex-Manager

纯前端单页面工具，用来把 ChatGPT Web 登录 session JSON 转换成 CPA、sub2api、Cockpit Tools、9router、Codex auth.json、AxonHub 或 Codex-Manager 可导入 JSON。

## 在线使用

如果你开启了 GitHub Pages，可以直接访问：
`https://agonie0v0.github.io/GPTSession2CPAandSub2API/`

也可以直接在本地浏览器打开 `docs/index.html` 使用。

## 使用提示

本项目主要用于不同工具之间的 JSON 格式互转。

- **关于 Codex 手机验证**：OpenAI 已限制直接使用 ChatGPT Web 登录 session 转换后导入来跳过 Codex OAuth `add phone` / 手机绑定验证的方式。也就是说，本项目不能用来绕过 Codex 登录时的手机绑定要求。
- **关于使用有效性**：转换结果是否能被目标工具成功使用，取决于 OpenAI 和目标工具当前的认证限制、账号权限以及 token 是否仍然有效。
- **关于自动刷新**：ChatGPT Web session 通常不包含 OAuth 流程中的 `refresh_token`，因此 access token 过期后不能自动刷新。Free 账号即使转换了，也可能没有权限调用需要付费账号权限的模型或功能。

## 支持输入

支持粘贴或拖入以下来源的 JSON 格式数据：

- **ChatGPT Web session JSON**：包含 `user.email`、`accessToken`、`sessionToken`、`expires`、`account.id`、`account.planType` 等。
- **9router Codex OAuth JSON**：包含 `accessToken`、`refreshToken`、`expiresAt`、`providerSpecificData.chatgptAccountId` 和 `providerSpecificData.chatgptPlanType` 等。
- **Codex 原生 auth.json**：包含 `auth_mode`、`OPENAI_API_KEY`、`tokens.access_token`、`tokens.refresh_token`、`tokens.id_token`、`tokens.account_id` 和 `last_refresh` 等。
- **AxonHub Codex auth.json**：包含 `tokens.access_token`、`tokens.refresh_token`、`tokens.id_token` 和 `last_refresh` 等。
- **Codex-Manager 批量导入 JSON**：包含 `tokens.access_token`、`tokens.refresh_token`、`tokens.id_token` 和 `meta.label` 等。

> 页面会自动尝试从 `accessToken` 的 JWT payload 中解析并补充邮箱、账号 ID、用户 ID、套餐类型和过期时间。

## 支持输出格式

- **CPA**：生成 Codex CPA auth JSON，包含 `type: "codex"`、`access_token`、`session_token`、`id_token`、`email`、`account_id`、套餐和过期时间等字段；缺少真实 `id_token` 时会根据 session 与 access token claims 构造 Codex 可解析的占位 JWT claims。
- **sub2api**：生成参考 `CPA2sub2API` 项目的 `exported_at/proxies/accounts` 结构，账号平台为 `openai`，类型为 `oauth`；每个账号对象包含 `expires_at` 和 `auto_pause_on_expired`（`expires_at` 来自 access token 的 JWT `exp` 秒级时间戳）。
- **Cockpit**：生成 Cockpit Tools Codex JSON 导入可识别的扁平 token格式，包含 `id_token`、`access_token`、`refresh_token`、`account_id`、`email`、`expired` 等字段。
- **9router**：生成 9router Codex OAuth JSON，包含 `accessToken`、`refreshToken`、`expiresAt`、`providerSpecificData`、`provider`、`authType`、`priority`、`isActive`、`createdAt` 和 `updatedAt` 等字段。
- **Codex**：生成原生 Codex `auth.json`，包含 `auth_mode: "chatgpt"`、`OPENAI_API_KEY: null`、`tokens.id_token/access_token/refresh_token/account_id` 和 `last_refresh`（缺少真实 `refresh_token` 时保留空字符串，过期后无法自动续期）。
- **AxonHub**：生成 AxonHub Codex auth.json，包含 `auth_mode: "chatgpt"`、`last_refresh` 和 `tokens.access_token/refresh_token/id_token`（缺少真实 `refresh_token` 时写入 `__missing_refresh_token__` 占位值）。
- **Codex-Manager**：生成 Codex-Manager 批量导入 JSON，包含 `tokens.access_token/refresh_token/id_token` 和 `meta.label/workspace_id/chatgpt_account_id/note`。

## 本地使用

直接在浏览器中打开：

```text
docs/index.html
```

> **安全说明**：所有解析和转换完全在浏览器本地前端完成，不上传任何 token，不写入任何远端或本地存储。
