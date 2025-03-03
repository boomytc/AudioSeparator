# 音频分离工具 (AudioSeparator)

## 简介

本项目是一个基于 [Demucs](https://github.com/facebookresearch/demucs) 的音频分离工具，可以将音频文件分离为人声、鼓、贝斯和其他乐器等不同声部。

## 功能特点

- 支持多种预训练模型
- 支持多种音频格式（WAV、MP3、FLAC、OGG等）
- 可以选择输出为WAV、MP3或FLAC格式
- 提供多种参数调整分离质量和性能
- 支持批量处理多个音频文件
- 支持使用GPU加速（如果可用）

## 环境安装

```bash
# 安装依赖
pip install -r requirements.txt

# 或者直接安装demucs
pip install demucs
```

## 使用方法

### 命令行使用

```bash
python demo.py 音频文件路径 [选项]
```

### 基本示例

```bash
# 使用默认设置分离音频
python demo.py path/to/audio.mp3

# 指定输出目录
python demo.py path/to/audio.mp3 -o output_folder

# 使用CPU处理
python demo.py path/to/audio.mp3 -d cpu

# 转换为MP3格式输出
python demo.py path/to/audio.mp3 --mp3

# 只分离人声和伴奏
python demo.py path/to/audio.mp3 --two-stems vocals
```

### 高级选项

```bash
# 使用更多随机移位提高质量
python demo.py path/to/audio.mp3 --shifts 10

# 使用多核处理加速
python demo.py path/to/audio.mp3 -j 4

# 设置分段大小以节省显存
python demo.py path/to/audio.mp3 --segment 10

# 输出为高质量FLAC格式
python demo.py path/to/audio.mp3 --flac
```

## 参数说明

以下是可用的命令行参数：

| 参数 | 说明 |
|------|------|
| `track` | 要分离的音频文件路径 |
| `-o, --output` | 输出目录，默认为"separated" |
| `-n, --model` | 预训练模型名称，默认为"htdemucs" |
| `-s, --signature` | 本地训练的XP签名 |
| `--repo` | 包含所有预训练模型的文件夹 |
| `-d, --device` | 使用的设备，可选"cuda"或"cpu"，默认为"cuda" |
| `--shifts` | 随机移位数量，增加质量但需要更多时间，默认为5 |
| `--overlap` | 分割之间的重叠，默认为0.25 |
| `--no-split` | 不将音频分成块处理，可能会占用大量内存 |
| `--segment` | 设置每个块的分割大小，可以帮助节省显卡内存 |
| `--two-stems` | 仅分离为两个声部，如"vocals"将分离为vocals和no_vocals |
| `--int24` | 将WAV输出保存为24位 |
| `--float32` | 将WAV输出保存为float32格式（文件大小增加2倍） |
| `--clip-mode` | 避免削波的策略：rescale（必要时缩放整个信号）或clamp（硬削波），默认为"rescale" |
| `--flac` | 将输出WAV转换为FLAC格式 |
| `--mp3` | 将输出转换为MP3格式 |
| `--mp3-bitrate` | MP3转换的比特率，默认为320 |
| `--mp3-preset` | MP3编码器预设，2为最高质量，7为最快速度，默认为2 |
| `--filename` | 设置输出文件名，支持变量{track}、{trackext}、{stem}、{ext} |
| `-j, --jobs` | 并行作业数，增加内存使用但多核处理更快，默认为1 |
| `-v, --verbose` | 显示详细输出 |

## 预训练模型

默认使用的是`htdemucs`模型，这是一个混合变换器模型，在大多数情况下效果最好。其他可用的模型包括：

- `mdx`: 使用MDX-Net架构的模型
- `mdx_extra`: MDX-Net的扩展版本
- `mdx_q`: MDX-Net的量化版本
- `htdemucs_ft`: 在更多数据上微调的htdemucs

## 输出格式

分离后的音频将保存在指定的输出目录中，按照以下结构组织：

```
输出目录/
  └── 模型名称/
      └── 音频文件名/
          ├── vocals.wav      # 人声
          ├── drums.wav       # 鼓
          ├── bass.wav        # 贝斯
          └── other.wav       # 其他乐器
```

如果使用了`--two-stems`选项，则只会生成两个文件，例如`vocals.wav`和`no_vocals.wav`。

## 注意事项

- 使用GPU可以显著加快处理速度
- 增加`shifts`参数可以提高分离质量，但会增加处理时间
- 对于大文件，可能需要使用`--segment`参数来避免显存不足
- 使用`-j`参数可以利用多核CPU加速处理

## 许可证

本项目基于MIT许可证开源。

## 致谢

本项目基于Facebook Research的[Demucs](https://github.com/facebookresearch/demucs)模型。
