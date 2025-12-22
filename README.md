# 音频分离工具 (AudioSeparator)

## 简介

本项目是一个基于 [Demucs](https://github.com/adefossez/demucs) 的音频分离工具，可以将音频文件分离为人声、鼓、贝斯和其他乐器等不同声部。

## 功能特点

- 支持多种预训练模型
- 支持多种音频格式（WAV、MP3、FLAC、OGG等）
- 可以选择输出为 WAV 或 MP3 格式
- 提供多种参数调整分离质量和性能
- 支持使用 GPU/MPS 加速（如果可用）
- 支持自定义输出文件名格式

## 环境安装

```bash
# 安装依赖
pip install -r requirements.txt

# 或者直接安装demucs
pip install demucs
```

### 针对不同系统
Windows: [Windows support](https://github.com/adefossez/demucs/blob/main/docs/windows.md).
MacOS: [MacOS support](https://github.com/adefossez/demucs/blob/main/docs/mac.md).
Linux: [Linux support](https://github.com/adefossez/demucs/blob/main/docs/linux.md).

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

# 使用 MPS 加速 (Mac M1/M2)
python demo.py path/to/audio.mp3 -d mps

# 设置 MP3 比特率 (自动输出为 MP3)
python demo.py path/to/audio.mp3 --mp3-bitrate 320

# 只分离人声和伴奏
python demo.py path/to/audio.mp3 --two-stems vocals
```

### 高级选项

```bash
# 使用更多随机移位提高质量
python demo.py path/to/audio.mp3 --shifts 10

# 使用多核处理加速 (0 为自动)
python demo.py path/to/audio.mp3 -j 4

# 设置分段大小以节省显存
python demo.py path/to/audio.mp3 --segment 10

# 自定义输出文件名格式
python demo.py path/to/audio.mp3 --filename "{track}_{stem}.{ext}"
```

## 参数说明

以下是可用的命令行参数：

| 参数 | 说明 |
|------|------|
| `track` | 要分离的音频文件路径 |
| `-o, --output` | 输出目录，默认为"separated" |
| `-n, --model` | 预训练模型名称，默认为"htdemucs" |
| `-d, --device` | 使用的设备，可选"cuda"、"cpu"或"mps"，默认为"cpu" |
| `--shifts` | 随机移位数量，增加质量但需要更多时间，默认为8 |
| `--overlap` | 分割之间的重叠，默认为0.5 |
| `--no-split` | 不将音频分成块处理，可能会占用大量内存 |
| `--segment` | 设置每个块的分割大小，可以帮助节省显卡内存，默认为7 |
| `--two-stems` | 仅分离为两个声部，如"vocals"将分离为vocals和no_vocals，默认为"vocals" |
| `--clip-mode` | 避免削波的策略：rescale（必要时缩放整个信号）或clamp（硬削波），默认为"rescale" |
| `--mp3-bitrate` | MP3转换的比特率，默认为256 |
| `--mp3-preset` | MP3编码器预设，2为最高质量，7为最快速度，默认为2 |
| `--filename` | 设置输出文件名，支持变量{track}、{trackext}、{stem}、{ext}，默认为"{track}_{stem}.{ext}" |
| `-j, --jobs` | 并行作业数，0表示自动选择最佳值，默认为0 |
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

本项目基于[Demucs](https://github.com/adefossez/demucs)模型。
