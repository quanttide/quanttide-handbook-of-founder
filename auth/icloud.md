# iCloud

Apple 账号的命令行认证。一次交互式登录后，会话会自动续期，供后续从 iCloud 相册同步素材使用。取素材的操作见案例集 `work/export-icloud-photo.md` 中的导出工作流（认证完成后即可执行）。

## 安装

icloudpd 是第三方 Python 工具，用 uv 安装：

```bash
uv tool install icloudpd
```

## 认证

### 账号区域

中国区账号登录后，Apple 返回的账号国家为 `CHN`，所有请求必须带 `--domain cn`。缺少该参数会报：

```text
Apple insists on using iCloud.com.cn for your request. Please use --domain parameter
```

不确定账号区域时，先不加参数试一次，按报错补上即可。

### 首次认证

首次认证需要交互输入密码和双重认证码：

```bash
icloudpd --domain cn --username <Apple ID> --auth-only
```

会话保存在 `~/.pyicloud/`，之后命令会自动续期，直到 2FA 过期；届时重新执行认证命令。双重认证码是一次性的，不要写入脚本或文档。密码不要写进命令或仓库，会话文件权限为 `600`。

## 排查

退出码为 0 不代表成功。用 `--list-albums` 检查时，不报错但列不出相册、日志里出现 `Apple iCloud setup is not complete`，通常是账号还没在网页端完成初始化：登录 [iCloud 网页版](https://www.icloud.com.cn/) 接受条款，确认图库能打开后再试。

其他常见现象：

- 首次请求返回 421 属于会话续期流程，随后会用保存的 session token 自动重新认证；
- 登录响应的 `webservices` 为空时，同样先检查网页端条款，仍为空则删除 `~/.pyicloud/` 重新认证；
- 2FA 过期后需要重新交互认证，无法通过参数绕过。
