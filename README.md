这里是**纯文本**版本的 README 内容（不含任何 Markdown 格式符号，直接复制粘贴到 README.md 文件里即可正常显示为 Markdown）

# Z-Image-Turbo 交互式生成工具（MPS 优化版）

一个专为 Apple Silicon（M 系列芯片）优化的交互式文本到图像生成工具，使用高效的 Z-Image-Turbo 4bit 量化模型。

只需加载一次模型（首次较慢），之后即可在终端不断输入不同中文/英文提示词，快速生成图片，非常适合快速试验 prompt、风格探索或批量产出素材。

主要特点

- 基于 Z-Image-Turbo（高效单流扩散模型，6~12步即可出高质量图）
- 使用 SDNQ uint4 + svd-r32 4bit 量化，大幅降低显存占用
- 专为 Apple MPS 优化（M1/M2/M3/M4 系列 Mac 流畅运行）
- 交互式命令行：输入提示词 → 回车即生成 → 继续输入下一条，无需重启程序
- 自动保存图片，文件名包含种子 + 时间戳，方便区分与管理
- 默认 8 步推理 + guidance_scale=0.0，速度与质量平衡最佳

系统要求

- 操作系统：macOS 12+（推荐最新版）
- 硬件：Apple Silicon M1 或更新（8GB 内存起步，16GB+ 更舒适）
- Python：3.10 或 3.11（推荐）
- PyTorch：支持 MPS 的版本（2.0+）

快速安装

1. 克隆本仓库
   git clone https://github.com/wvqkhn/z-image-turbo-demo-mac.git
   cd z-image-turbo-interactive

2. 创建并激活虚拟环境（强烈推荐）
   python -m venv venv
   source venv/bin/activate          # macOS/Linux
   # Windows: venv\Scripts\activate

3. 安装核心依赖
   pip install torch torchvision torchaudio
   pip install diffusers transformers accelerate
   pip install sdnq

   如果 diffusers 版本不兼容，可尝试最新开发版：
   pip install git+https://github.com/huggingface/diffusers.git

使用方法

1. 运行脚本
   python generate.py

2. 等待模型加载完成（首次需下载约 6GB 模型文件 + 编译，较慢）

3. 看到提示后直接输入提示词，回车生成：
   请输入提示词 (或输入 q/exit/quit 退出): 倪仙和倪忠文在古风书房一起写书法，汉服，水墨渲染，电影光影，极致细节，8k

4. 生成完成后自动保存为类似以下文件：
   z-turbo_123_20260111_105932.png

5. 继续输入下一条提示词，或输入 q 退出程序

推荐参数修改（直接编辑代码）

在交互循环上方的默认参数区域，可根据需要调整：

negative_prompt = "模糊，畸形，低质量，难看，穿帮，文字，水印，畸形手，多余肢体，丑陋"
num_steps = 8           # 推荐范围：6~12
height, width = 640, 480   # 保持 16 的倍数
guidance_scale = 0.0       # Turbo 模型建议 0~1，0 最自由

想固定种子或完全随机？可修改：
seed = 42                      # 固定种子
# 或
seed = torch.seed()            # 完全随机

常见问题解答

Q：第一次运行非常慢怎么办？
A：正常现象，需要下载模型文件 + 首次 MPS kernel 编译，之后会明显变快。

Q：内存不足（OOM）？
A：已开启 model_cpu_offload + attention_slicing，8GB 机型基本可跑。
如仍出现问题，可尝试：降低分辨率到 512×384 或步数到 6。

Q：生成的图风格不够稳定？
A：可尝试固定种子，或在 prompt 中加入更强的风格描述词，如 “masterpiece, best quality, ultra detailed”。

鸣谢

- 模型来源：Tongyi-MAI/Z-Image-Turbo
- 量化版本：Disty0/Z-Image-Turbo-SDNQ-uint4-svd-r32
- 量化工具：SDNQ
- 推理框架：huggingface/diffusers
