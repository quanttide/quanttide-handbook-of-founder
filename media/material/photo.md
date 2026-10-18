# 照片素材

用 icloudpd 把 iCloud 相册中的照片和视频同步到本地素材目录。适用于需要把拍摄素材集中到工作站、又不想逐台手动导出的场景。

前置：已完成 [iCloud 账号认证](../../auth/icloud.md)，包括 icloudpd 安装与首次登录。

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

## 自动化

长期同步可用 cron 或 systemd timer 定时执行同一命令。`--watch-with-interval 3600` 可以常驻轮询，但进程退出后不会自行恢复。

认证、会话与账号异常见 [iCloud 账号认证](../../auth/icloud.md)。
