<div align="center">

# 生成式推荐系统

[![python](https://img.shields.io/badge/-Python_3.10-blue?logo=python&logoColor=white)](https://github.com/pre-commit/pre-commit)
[![pytorch](https://img.shields.io/badge/PyTorch_2.0+-ee4c2c?logo=pytorch&logoColor=white)](https://pytorch.org/get-started/locally/)
[![lightning](https://img.shields.io/badge/-Lightning_2.0+-792ee5?logo=pytorchlightning&logoColor=white)](https://pytorchlightning.ai/)
[![hydra](https://img.shields.io/badge/Config-Hydra_1.3-89b8cd)](https://hydra.cc/)
[![license](https://img.shields.io/badge/License-MIT-green.svg?labelColor=gray)](https://github.com/ashleve/lightning-hydra-template#license)
[![PRs](https://img.shields.io/badge/PRs-welcome-brightgreen.svg)](https://github.com/ashleve/lightning-hydra-template/pulls)

使用 Lightning 和 Hydra 复现生成式推荐系统。

_欢迎提出建议！_

</div>

<br>

## 项目简介

本仓库旨在使用 Lightning 和 Hydra 复现 [Generative Recommenders](https://github.com/facebookresearch/generative-recommenders)。该项目包含论文 [\"Actions Speak Louder than Words: Trillion-Parameter Sequential Transducers for Generative Recommendations\"](https://arxiv.org/abs/2402.17152) 的代码实现。虽然主要用于个人学习，但本仓库提供了以下几个关键特性：

- **高效训练与推理**：通过优化 GPU 利用率来提升训练和推理速度。因此，在单个 4090 或 L4 机器上，MovieLens-1M 数据集的 100 轮训练现在可以在 10 分钟内完成。
- **简化实验流程**：通过配置文件和命令行选项轻松管理和创建分层配置，支持各种实验。
- **模块化配置**：通过配置文件动态实例化对象，允许在不同数据集或模块之间无缝切换，无需大量重写代码。
- **硬件无关**：移除了对 NVIDIA GPU 的依赖，使您可以在任何设备上运行脚本，包括本地机器进行训练、评估和调试。
- **提高可读性**：代码经过大幅重构以提高清晰度。生成式推荐系统模块现在分为四个主要组件：嵌入层（embeddings）、预处理器（preprocessor）、序列编码器（sequence encoder）和后处理器（postprocessor），使训练和评估过程更加透明。

## 安装

推荐使用 `uv` 来安装库：

```bash
uv venv -p 3.10 && source .venv/bin/activate
uv pip install --extra dev --extra test -r pyproject.toml
uv pip install -e . --no-deps
```

对于支持 GPU 的 Linux 系统，您还可以安装 `fbgemm-gpu` 以提升性能：

```bash
uv pip install fbgemm-gpu==0.7.0
```

## 如何运行

根据配置准备数据集。

```bash
make prepare_data data=ml-1m
```

使用默认配置训练模型

```bash
# 在 CPU 上训练
make train trainer=cpu

# 在 GPU 上训练
make train trainer=gpu
```

使用特定实验配置训练模型。从 [configs/experiment/](configs/experiment/) 中选择一个实验配置：

```bash
make train experiment=ml-1m-hstu
```

使用给定检查点评估模型

```bash
make eval ckpt_path=example_checkpoint.pt
```

使用给定检查点进行预测

```bash
make predict ckpt_path=example_checkpoint.pt output_file=example_output.csv
```

从命令行覆盖参数

```bash
make train trainer.max_epochs=20 data.batch_size=64
```

## 实验结果

### MovieLens-1M (ML-1M)

为确保可重现性并消除随机性，数据集生成中的采样已被移除，训练中的种子设置为 42。

| 方法                                                  | HR@10  | NDCG@10 | HR@50  | NDCG@50 | HR@100 | NDCG@100 | HR@200 | NDCG@200 | MRR    |
| ----------------------------------------------------- | ------ | ------- | ------ | ------- | ------ | -------- | ------ | -------- | ------ |
| [HSTU](configs/experiment/ml-1m-hstu.yaml)            | 0.2975 | 0.1680  | 0.5815 | 0.2308  | 0.6887 | 0.2483   | 0.7735 | 0.2602   | 0.1455 |
| [HSTU w/ Aux](configs/experiment/ml-1m-hstu-aux.yaml) | 0.3031 | 0.1726  | 0.5798 | 0.2337  | 0.6861 | 0.2510   | 0.7724 | 0.2631   | 0.1493 |

欢迎探索和修改配置以满足您的需求。我们随时欢迎您的贡献和建议！
