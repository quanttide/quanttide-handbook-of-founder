# 视频素材

用 icloudpd 把 iCloud 相册中的视频同步到本地素材目录。适用于需要把拍摄素材集中到工作站、又不想逐台手动导出的场景。图片见 [照片素材](./image.md)。

前置：已完成 [iCloud 账号认证](../../auth/icloud.md)，包括 icloudpd 安装与首次登录。

素材目录固定为两个：`<素材目录>/images` 放 JPG，`<素材目录>/videos` 放 MP4。本篇只处理视频，同步后直接转码，不保留 MOV。

## 查看相册

```bash
icloudpd --domain cn --username <Apple ID> --list-albums
```

不加 `--album` 时操作整个图库；只同步指定相册时加 `--album "相册名"`。

## 同步

视频单独执行，用 `--skip-photos` 跳过图片：

```bash
icloudpd --domain cn --username <Apple ID> \
  --directory <素材目录>/videos \
  --skip-photos \
  --folder-structure none \
  --set-exif-datetime
```

正式执行前先用 `--recent 5 --dry-run` 试跑，确认选中的文件和落盘路径。

主要参数：

- `--folder-structure none`：所有文件直接放在目标目录，路径固定，便于引用；
- `--set-exif-datetime`：文件缺少 EXIF 拍摄时间时用创建时间补齐；
- `--until-found N`：增量同步时，遇到 N 个已下载项即停止。

Live Photo 的配对 MOV 可能落在 `images/` 下，转码时按本篇一并处理；只需要静态图时，在同步照片时加 `--skip-live-photos`。

重复执行同一命令即可增量同步，工具按文件名和大小识别已下载文件。

## 转码

同步下来的原格式是 MOV，素材目录只保留 MP4。转换成功后删除旧格式文件；需要保留原片时先另存。命令中的 `<素材目录>` 与同步时一致。

### 视频转 MP4

iPhone 视频多为 HEVC 编码，转成 H.264 以便剪辑软件和平台直接使用：

```bash
ffmpeg -hide_banner -loglevel error -i input.MOV \
  -map 0:v:0 -map 0:a? \
  -c:v libx264 -preset medium -crf 20 -pix_fmt yuv420p \
  -c:a aac -b:a 192k -movflags +faststart \
  output.mp4
```

`videos/` 下的 MOV 原地转成 MP4；`images/` 下 Live Photo 的 `_HEVC.MOV` 转成 MP4 后移到 `videos/`。转换成功后删除 MOV。

转码前用 `ffprobe` 查看 `color_transfer`。SDR（`bt709`）直接转即可；HDR（`arib-std-b67` 或 `smpte2084`）需要先做 tonemap，否则颜色会发灰。

## 自动化

长期同步可用 cron 或 systemd timer 定时执行同一命令。`--watch-with-interval 3600` 可以常驻轮询，但进程退出后不会自行恢复。
