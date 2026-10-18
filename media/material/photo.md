# 照片素材

用 icloudpd 把 iCloud 相册中的照片同步到本地素材目录。适用于需要把拍摄素材集中到工作站、又不想逐台手动导出的场景。视频见 [视频素材](./video.md)。

前置：已完成 [iCloud 账号认证](../../auth/icloud.md)，包括 icloudpd 安装与首次登录。

素材目录固定为两个：`<素材目录>/images` 放 JPG，`<素材目录>/videos` 放 MP4。本篇只处理图片，同步后直接转码，不保留 HEIC。

## 查看相册

```bash
icloudpd --domain cn --username <Apple ID> --list-albums
```

不加 `--album` 时操作整个图库；只同步指定相册时加 `--album "相册名"`。

## 同步

照片单独执行，用 `--skip-videos` 跳过视频：

```bash
icloudpd --domain cn --username <Apple ID> \
  --directory <素材目录>/images \
  --skip-videos \
  --size original \
  --folder-structure none \
  --set-exif-datetime
```

正式执行前先用 `--recent 5 --dry-run` 试跑，确认选中的文件和落盘路径。

主要参数：

- `--size original`：拉取原图，避免拿到 iCloud 优化存储的缩略图；
- `--folder-structure none`：所有文件直接放在目标目录，路径固定，便于引用；
- `--set-exif-datetime`：文件缺少 EXIF 拍摄时间时用创建时间补齐；
- `--until-found N`：增量同步时，遇到 N 个已下载项即停止。

Live Photo 会连同配对的 MOV 一起下载，MOV 可能落在 `images/` 下；转码时按 [视频素材](./video.md) 转成 MP4 并移到 `videos/`。只需要静态图时加 `--skip-live-photos`。

重复执行同一命令即可增量同步，工具按文件名和大小识别已下载文件。

## 转码

同步下来的原格式是 HEIC，素材目录只保留 JPG。转换成功后删除旧格式文件；需要保留原图时先另存。命令中的 `<素材目录>` 与同步时一致。

### 图片转 JPG

ImageMagick 缺少 HEIC 解码插件、ffmpeg 只能读到 HEIC 分块流时，用 pillow-heif：

```bash
uv run --no-project --with pillow-heif python - <<'PY'
from pathlib import Path
import io

from PIL import Image, ImageOps, ImageCms
import pillow_heif

pillow_heif.register_heif_opener()
for src in Path("<素材目录>/images").glob("*.HEIC"):
    im = Image.open(src)
    exif = im.getexif()
    im = ImageOps.exif_transpose(im)
    icc = im.info.get("icc_profile")
    if icc:
        im = ImageCms.profileToProfile(
            im,
            ImageCms.ImageCmsProfile(io.BytesIO(icc)),
            ImageCms.createProfile("sRGB"),
            outputMode="RGB",
        )
    exif[274] = 1
    im.convert("RGB").save(
        src.with_suffix(".jpg"),
        "JPEG",
        quality=92,
        optimize=True,
        exif=exif.tobytes(),
    )
    src.unlink()
PY
```

脚本把 Display P3 等内嵌配置转到 sRGB、保留 EXIF，并在成功后删除 HEIC。PNG 截图同样转为 JPG，扩展名统一用小写 `.jpg`。

## 自动化

长期同步可用 cron 或 systemd timer 定时执行同一命令。`--watch-with-interval 3600` 可以常驻轮询，但进程退出后不会自行恢复。
