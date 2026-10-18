# Apple

Apple 账号的命令行认证，以及用 icloudpd 把 iCloud 图库同步到本地。适用于需要把照片和视频素材拉到工作站、又不想逐台手动导出的场景。

## 安装

icloudpd 是第三方 Python 工具，用 uv 安装：

```bash
uv tool install icloudpd
```

## 账号区域

中国区账号登录后，Apple 返回的账号国家为 `CHN`，所有请求必须带 `--domain cn`。缺少该参数会报：

```text
Apple insists on using iCloud.com.cn for your request. Please use --domain parameter
```

不确定账号区域时，先不加参数试一次，按报错补上即可。

## 认证

首次认证需要交互输入密码和双重认证码：

```bash
icloudpd --domain cn --username <Apple ID> --auth-only
```

会话保存在 `~/.pyicloud/`，之后命令会自动续期，直到 2FA 过期；届时重新执行认证命令。双重认证码是一次性的，不要写入脚本或文档。

## 查看相册

```bash
icloudpd --domain cn --username <Apple ID> --list-albums
```

不加 `--album` 时操作整个图库；只同步指定相册时加 `--album "相册名"`。

## 同步

照片和视频分开执行，用 `--skip-videos` 与 `--skip-photos` 分流。以下示例把内容放到 `~/Pictures/iCloud/`，按实际素材目录替换。

照片：

```bash
icloudpd --domain cn --username <Apple ID> \
  --directory ~/Pictures/iCloud/images \
  --skip-videos \
  --size original \
  --folder-structure '{:%Y/%m}' \
  --set-exif-datetime \
  --xmp-sidecar
```

视频：

```bash
icloudpd --domain cn --username <Apple ID> \
  --directory ~/Pictures/iCloud/videos \
  --skip-photos \
  --folder-structure '{:%Y/%m}' \
  --set-exif-datetime
```

正式执行前先用 `--recent 5 --dry-run` 试跑，确认选中的文件和落盘路径。

主要参数：

- `--size original`：拉取原图，避免拿到 iCloud 优化存储的缩略图；
- `--folder-structure '{:%Y/%m}'`：按拍摄年月分目录，降低同名冲突；
- `--set-exif-datetime`：文件缺少 EXIF 拍摄时间时用创建时间补齐；
- `--xmp-sidecar`：把相册、地点等附加信息导出为 XMP；
- `--until-found N`：增量同步时，遇到 N 个已下载项即停止。

Live Photo 会连同配对的 MOV 一起下载，MOV 可能落在照片目录下；只需要静态图时加 `--skip-live-photos`。

重复执行同一命令即可增量同步，工具按文件名和大小识别已下载文件。

## 排查

退出码为 0 不代表成功。`--list-albums` 不报错但列不出相册、日志里出现 `Apple iCloud setup is not complete` 时，通常是账号还没在网页端完成初始化：登录 [iCloud 网页版](https://www.icloud.com.cn/) 接受条款，确认图库能打开后再试。

其他常见现象：

- 首次请求返回 421 属于会话续期流程，随后会用保存的 session token 自动重新认证；
- 登录响应的 `webservices` 为空时，同样先检查网页端条款，仍为空则删除 `~/.pyicloud/` 重新认证；
- 2FA 过期后需要重新交互认证，无法通过参数绕过。

## 安全与自动化

密码不要写进命令或仓库，认证交互由本人完成。会话文件位于 `~/.pyicloud/`，权限为 `600`。

长期同步可用 cron 或 systemd timer 定时执行同一命令。`--watch-with-interval 3600` 可以常驻轮询，但进程退出后不会自行恢复。
