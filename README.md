# 🛠️ Codex 客户端登录 403 Forbidden 解决方案

> 💡 解决使用 VPN / Clash 等代理工具登录 Codex 桌面客户端时，出现 `403 Forbidden`、`Region Unavailable` 等错误的问题。

## 📌 问题描述

在使用 VPN 连接网络，并通过 ChatGPT 账号登录 **Codex Desktop** 时，可能遇到以下情况：

- ✅ 浏览器可以正常访问 ChatGPT
- ❌ Codex 客户端无法完成 OAuth 登录验证
- ⚠️ 登录页面提示 `403 Forbidden` 或 `Region Unavailable`

即使代理工具已经开启，Codex 仍可能无法正常完成身份验证。

## 🔍 可能原因

Codex 使用 **OAuth 授权流程**连接 OpenAI 服务器，完成 ChatGPT 账号的身份验证。

问题可能出在浏览器与桌面客户端的**代理配置不一致**：

- 🌐 **浏览器流量**：通常可以通过浏览器或系统配置的代理访问 OpenAI。
- 💻 **Codex 客户端流量**：部分网络请求可能没有正确使用系统代理，而是尝试直接连接目标服务器。

因此，即使浏览器能够正常登录 ChatGPT，Codex 客户端仍可能因网络路由、IP 地址限制或服务器安全验证而返回 `403 Forbidden`。

## ✅ 解决方法

### 1️⃣ 找到 Codex 配置文件

Codex 的默认配置文件位于：

```text
~/.codex/config.toml
```

不同操作系统的路径如下：

**🪟 Windows**

```text
C:\Users\<用户名>\.codex\config.toml
```

**🍎 macOS / 🐧 Linux**

```text
~/.codex/config.toml
```

如果文件不存在，可以手动创建。

**Windows（PowerShell）**

```powershell
New-Item -ItemType Directory -Force "$HOME\.codex"
notepad "$HOME\.codex\config.toml"
```

如果记事本提示文件不存在，选择创建新文件即可。

**macOS / Linux（Terminal）**

```bash
mkdir -p ~/.codex
touch ~/.codex/config.toml
```

### 2️⃣ 修改配置文件

使用文本编辑器打开 `config.toml`，在文件末尾添加以下内容：

```toml
[features]
respect_system_proxy = true
```

**🔧 配置说明**

- `[features]`：Codex 功能配置区域。
- `respect_system_proxy = true`：启用系统代理支持，使 Codex 尝试按照操作系统的代理设置路由网络请求。

> ⚠️ **注意事项**
>
> - `true` 必须小写，无须添加引号。
> - 如果文件中已经存在 `[features]`，只需在该区域下添加 `respect_system_proxy = true`。
> - 不要重复创建 `[features]`，否则可能导致 TOML 配置解析失败。
> - 参数名是 `respect_system_proxy`，不是 `respect_system_proxies`。

### 3️⃣ 重启 Codex 客户端

保存配置文件后：

1. 完全退出 Codex 客户端。
2. 确认 VPN / 代理工具正在运行。
3. 重新启动 Codex。
4. 再次尝试通过 ChatGPT 账号登录。

如果问题由 Codex 未正确使用系统代理引起，启用上述配置可能解决登录失败的问题。

## ⚠️ 其他注意事项

如果修改配置后仍然出现 `403 Forbidden`，建议进一步检查：

- **系统代理状态**：确认操作系统已正确配置代理。仅启用 VPN 的 TUN 模式，不代表系统 HTTP 代理已开启。
- **代理节点**：确认当前出口 IP 所在地区支持相应 OpenAI 服务。
- **网络验证**：部分 `403 Forbidden` 由 Cloudflare 安全验证或服务器访问策略触发，并非系统代理配置导致。
- **客户端版本**：不同 Codex 版本和网络传输方式对系统代理的支持可能存在差异。

**注意：** `respect_system_proxy = true` 并不是所有 403 错误的通用解决方案，它主要用于排查和改善 Codex 对系统代理的使用。

---

## 🎉 总结

当 ChatGPT 网页版可以正常访问，但 Codex 桌面客户端无法完成登录验证时，可以优先检查客户端是否正确使用了系统代理。

核心配置：

```toml
[features]
respect_system_proxy = true
```

📚 **参考资料**

- [OpenAI Codex GitHub Repository](https://github.com/openai/codex)
- [Codex 系统代理配置相关讨论](https://github.com/openai/codex/issues/39237)
---

⭐ 如果这个方法帮你解决了问题，欢迎收藏或分享给遇到类似情况的开发者！
