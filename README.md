> **再分发说明（2026-11-17）**
>
> 本仓库是 [p-e-w/heretic](https://github.com/p-e-w/heretic) 的再分发副本。本次只把根目录 `README.md` 换成中文译本，程序代码未改。原英文 README 仍在上游仓库。
>
> 版权所有 © 2025-2026 Philipp Emanuel Weidmann（<pew@worldwidemann.com>）及贡献者。本程序按 GNU Affero General Public License v3.0 再分发，许可证全文见仓库内 [`LICENSE`](/LICENSE)。

<div align="center">

<img width="128" alt="Heretic logo" src="https://github.com/user-attachments/assets/df5f2840-2f92-4991-aa57-252747d7182e" />

# Heretic

### 无需训练的语言模型修改

**去除审查**，抑制套话，降低谄媚，以及更多。<br>
一个下午，在你的游戏电脑上就能做完。

[![Discord](https://img.shields.io/discord/1447831134212984903?color=5865F2&label=discord&labelColor=black&logo=discord&logoColor=white&style=for-the-badge)](https://discord.gg/gdXc48gSyT)&nbsp;&nbsp;&nbsp;&nbsp;
[![Matrix](https://img.shields.io/badge/Matrix-black?logo=matrix&style=for-the-badge)](https://matrix.to/#/#heretic:matrix.org)&nbsp;&nbsp;&nbsp;
[![Follow us on Hugging Face](https://huggingface.co/datasets/huggingface/badges/resolve/main/follow-us-on-hf-md-dark.svg)](https://huggingface.co/heretic-org)&nbsp;&nbsp;&nbsp;
[![Codeberg mirror](https://img.shields.io/badge/Codeberg%20mirror-black?logo=codeberg&style=for-the-badge)](https://codeberg.org/p-e-w/heretic)

</div>

-----

&nbsp;

[![#1 Repository of the Day](https://trendshift.io/api/badge/repositories/20538)](https://trendshift.io/repositories/20538)&nbsp;&nbsp;&nbsp;&nbsp;
[![Model and download statistics](https://github.com/user-attachments/assets/c9b5a660-7ef9-43c7-991c-fd9d63ea8bd8)](https://heretic.sdad.pro/)


<a id="modify-any-llm"></a>

## 修改任何你的硬件跑得动的语言模型[^1]

[^1]: Heretic 支持用 bitsandbytes 做即时 4-bit 量化。开启后，处理模型所需的显存可以降到与运行一个典型量化版本相近，代价是精度略有损失。把 `quantization` 设为 `bnb_4bit` 即可启用。

Heretic 让你做一件通常只有掌握大量算力的公司才做的事：*修改*语言模型，而不只是运行它。它不做昂贵的训练，而是用一组数学方法，根据对照示例提示词和对应的评分器来引导模型行为。

按配置不同，Heretic 可以：

* **抑制拒答 / 去除审查**（这是[默认动作](/config.default.toml)）
* 减少套话（slop）（[`config.noslop.toml`](/config.noslop.toml)）
* 减少幽默（[`config.nohumor.toml`](/config.nohumor.toml)）
* 减少谄媚（sycophancy）（[`config.nosyco.toml`](/config.nosyco.toml)）
* 调整情绪对齐（[`config.emotions.toml`](/config.emotions.toml)）
* 缩短回答长度（[`config.terse.toml`](/config.terse.toml)）

以及很多其他事情。

### :bangbang: 使用 Heretic 不需要理解语言模型的内部原理 :bangbang:

事实上，你可以[几秒内](#usage)从零开始，对第一个模型做去除审查。Heretic 支持大多数稠密模型，包括许多多模态模型、若干种 MoE 架构，以及许多现代混合架构模型。

社区已经用 Heretic 制作并发布了[数千个](https://huggingface.co/models?other=heretic)模型。这些模型经过 MMLU、GSM8K 等标准指标的独立评测，相对其他 abliteration 工具产出的模型表现更好。[^2][^3]

[^2]: [Qwen2.5-7B：三种 abliteration 对比](https://huggingface.co/DreamFast/Qwen-2.5-7b-abliterlitics)：*"Heretic is the most surgical and most effective."*（Heretic 最精准，也最有效。）
[^3]: [Gemma 4 E4B：23 种 abliteration 变体对比](https://huggingface.co/DreamFast/Gemma4-e4b-abliterlitics)：*"Best overall: heretic."*（综合最佳：heretic。）

&nbsp;

<img width="600" alt="Heretic screenshot" src="https://github.com/user-attachments/assets/a030f595-67ef-49a6-9d9a-779fdf752b58" />

&nbsp;


<a id="usage"></a>

## 用法

准备 Python 3.10+ 环境，并按你的硬件装好 PyTorch 2.6+。然后运行：

```sh
pip install -U heretic-llm
heretic Qwen/Qwen3-4B-Instruct-2507
```

把 `Qwen/Qwen3-4B-Instruct-2507` 换成你要处理的模型。**默认情况下，Heretic 做的是去除审查（抑制拒答）。** 如果要做别的，选用上面链接的配置文件，或自己写一份。可用配置项见 [`config.default.toml`](/config.default.toml)。Heretic 默认读取工作目录里的 `config.toml`，也可以用环境变量 `HERETIC_CONFIG_FILE` 指定配置文件路径。

> [!IMPORTANT]
>
> 为 Heretic 1.x 写的配置文件**不能**用于 Heretic 2.x！
> 旧配置要改什么，见[迁移指南](/docs/migration_v1_to_v2.md)。

程序一开始会先做基准测试，按现有硬件选出合适的 batch size。在 RTX 3090 上，使用默认配置处理 [Qwen3-4B-Instruct-2507](https://huggingface.co/Qwen/Qwen3-4B-Instruct-2507) 大约需要 30–40 分钟。

处理结束后，你可以选择：保存模型、上传到 Hugging Face、跟它对话看效果、跑标准基准，或任意组合。


<a id="scorers"></a>

## 用评分插件控制 Heretic 做什么

Heretic 通过优化一个或多个分数来引导模型行为。分数由评分插件（scorer plugin）计算。你可以自己写评分器，完全控制 Heretic 对模型做的事。

评分器必须继承的 `Scorer` 接口（抽象基类）定义在 [`scorer.py`](/src/heretic/scorer.py)。唯一必须实现的方法是 `get_score`，需要时还有更多机制可用（例如设置管理）。

默认情况下，Heretic 用拒答率（实现于 [`keyword_rate.py`](/src/heretic/scorers/keyword_rate.py)）和 KL 散度（实现于 [`kl_divergence.py`](/src/heretic/scorers/kl_divergence.py)）给模型打分。[`scorers` 目录](/src/heretic/scorers/)里还有其他评分器。它们可以当例子，也可以按你的需要改。

评分器通过[主配置文件](/config.default.toml)配置。哪些评分插件生效，由 `scorers` 设置决定；它同时决定优化时是最小化还是最大化该分数。可以同时使用任意多个评分器，它们的分数会一起优化。同一个评分器也可以配多份不同设置。这一机制的完整说明见配置文件。

评分插件路径可以写成完整导入路径（`fully.qualified.module.MyScorerClass`），也可以写成「文件路径 + 类名」（`path/to/plugin.py:MyScorerClass`）。使用自己的插件时，**不必**改 Heretic 包内的代码。

Heretic 还有修改器插件，见[下一节](#how-heretic-works)。


<a id="how-heretic-works"></a>

## Heretic 如何工作

Heretic 把可插拔的修改器（接口在 [`modifier.py`](/src/heretic/modifier.py)）和基于 TPE 的超参数优化器结合起来，优化器由 [Optuna](https://optuna.org/) 驱动。优化器找出一组参数，交给修改器后，使结果模型上的一组可配置分数被最小化或最大化（[见上一节](#scorers)）。

Heretic 自带两种修改器：

* **任意秩消融（Arbitrary-Rank Ablation，ARA）**[^4]：这是默认修改器，专为 Heretic 开发。ARA 的概述见[最初的 pull request](https://github.com/p-e-w/heretic/pull/211)，精确实现见 [`ara.py`](/src/heretic/modifiers/ara.py)。

* **方向消融，也就是 “abliteration”**[^5]：抑制拒答的经典方法。Heretic 的实现在各层上使用灵活的消融权重核，并同时支持投影消融（projected abliteration）[^6]和保范数（MPOA）[^7]。完整代码在 [`abliteration.py`](/src/heretic/modifiers/abliteration.py)。

[^4]: *Weidmann 2026.* 解释该技术的预印本即将发布。
[^5]: *[Arditi et al. 2024](https://arxiv.org/abs/2406.11717)*
[^6]: *[Lai 2025 (1)](https://huggingface.co/blog/grimjim/projected-abliteration)*
[^7]: *[Lai 2025 (2)](https://huggingface.co/blog/grimjim/norm-preserving-biprojected-abliteration)*

用 `modifiers` 设置切换修改器实现。这个设置的名字是复数，但目前一次处理只允许一个修改器。以后可能会变。


<a id="grimoire"></a>

## 可复现性与 Heretic 魔典

Heretic 可以做出*可复现模型*：模型仓库里带有按需生成逐字节相同模型所需的全部信息，前提是环境与当初完全一致。PyTorch 不保证运算结果逐字节相同，除非硬件、驱动、操作系统，以及包含全部包版本在内的整个软件栈都完全一样。

这些复杂度由 Heretic 替你管。把处理好的模型上传到 Hugging Face 时，Heretic 会询问要不要附上可复现信息。这完全可选。如果选择附上，上传的仓库会有一个 `reproduce` 目录，里面是重建该模型的详细说明（[示例](https://huggingface.co/heretic-org/LFM2.5-VL-3B-heretic/tree/main/reproduce)）。这个目录既有人读的说明，也有机器可读的清单文件 `reproduce.json`。Heretic **自动**复现模型所需的信息都在这份清单里。

自动复现：

```sh
heretic --reproduce path/to/reproduce.json
```

Heretic 会带你走完流程：把你的环境与创建该模型时的环境对比，并指出可能有问题的差异。当初做原始模型时那几个小时的计算**不必**重做，整个过程通常几分钟。导出结果模型后，Heretic 会把权重文件的哈希与复现清单里保存的哈希核对（是否相同，取决于你的系统与原系统有多接近）。

很多人已经把带可复现信息的模型上传到了 Hugging Face。一份复现清单大约只有 9 KB，这些清单构成了一个可随时复现的便携模型库。

### 这个库叫 Heretic 魔典（Heretic Grimoire）。可以在[这里](https://huggingface.co/spaces/heretic-org/Heretic-Grimoire)查看。

你不必依赖外部托管的网页来访问这份清单库。魔典从根本上按*分布式*系统设计。随时可以从 Hugging Face 拉取公开清单的本地副本：

```sh
heretic --collect-reproducibles path/to/target/folder/
```

这样你就有了一份编目好的、全部可复现 Heretic 模型的本地备份。要更新这个集合，再跑一次同一条命令即可。它是只追加的备份：即使对应模型已经不在 Hugging Face 上，也不会删除本地文件。


## Heretic 是人写的

以下是原作者 [Philipp Emanuel Weidmann](https://github.com/p-e-w) 的说明译文。

和今天很多 AI 相关软件（以及很多软件本身）不同，Heretic **不是** “vibe-coded”（凭感觉让模型生成的代码）。

在 2026 年 11 月之前，[我](https://github.com/p-e-w)对本项目的全部提交都是 100% 手写的，沿用我几十年来的编程方式。此后我只*非常*有限地使用 LLM 辅助。许多 Heretic 贡献者会用编程代理，但我合并的每一行代码都会仔细审阅。这里没有 “LGTM”。

不信的话，看[这个 pull request](https://github.com/p-e-w/heretic/pull/53)：超过 7 个月里积累了**近 350 条人手写的评论**，我们在里面讨论实现。还有[另一个 PR](https://github.com/p-e-w/heretic/pull/191)，**评论超过 200 条**。

我不是卢德分子，其他项目里我也大量用代理。但我为自己写的代码感到自豪，在这么在意的项目上，到目前为止还是不愿意让机器替我写。结果是，Heretic 的代码库精简、相对简单。在我看来，以及在一些其他人看来，写起来是愉快的。

如果你也喜欢这样，加入我们。到 Discord 上讲你的改进想法，或者直接提 issue、开 PR。


## 先前工作

Heretic 首次发布时（2025 年 11 月），我知道的、已经公开的 abliteration 实现有：

* [AutoAbliteration](https://huggingface.co/posts/mlabonne/714992455492422)
* [abliterator.py](https://github.com/FailSpy/abliterator)
* [wassname's Abliterator](https://github.com/wassname/abliterator)
* [ErisForge](https://github.com/Tsadoq/ErisForge)
* [Removing refusals with HF Transformers](https://github.com/Sumandora/remove-refusals-with-transformers)
* [deccp](https://github.com/AUGMXNT/deccp)

Heretic 是从头写的，没有复用上述任何项目的代码。


## 致谢

Heretic 的开发参考了：

* [最初的 abliteration 论文 *(Arditi et al. 2024)*](https://arxiv.org/abs/2406.11717)
* [Maxime Labonne 关于 abliteration 的文章](https://huggingface.co/blog/mlabonne/abliteration)，以及他本人消融模型卡片中的一些细节，例如使用非常数消融权重
* Jim Lai 介绍[「投影消融」](https://huggingface.co/blog/grimjim/projected-abliteration)和[「保范数双投影消融」](https://huggingface.co/blog/grimjim/norm-preserving-biprojected-abliteration)的文章
* 与社区成员的数千次交流


## 引用

如果研究中使用了 Heretic，请用下面的 BibTeX 引用。引用对象是上游仓库，不是本中文再分发副本。

```bibtex
@misc{heretic,
  author = {Weidmann, Philipp Emanuel},
  title = {Heretic: Training-free language model modification},
  year = {2025},
  publisher = {GitHub},
  journal = {GitHub repository},
  howpublished = {\url{https://github.com/p-e-w/heretic}}
}
```


## 许可证

Copyright &copy; 2025-2026 Philipp Emanuel Weidmann (<pew@worldwidemann.com>) + contributors

本程序是自由软件：你可以按自由软件基金会发布的 GNU Affero General Public License 第 3 版，或（依你的选择）任何后续版本，再分发和/或修改它。

本程序的发布是希望它有用，但**没有任何担保**；也没有对适销性或特定用途适用性的默示担保。详情见 GNU Affero General Public License。

你应当已随本程序收到 GNU Affero General Public License 的副本。如果没有，见 <https://www.gnu.org/licenses/>。

**向本项目贡献代码，即表示你同意以同一许可证发布你的贡献。**

本中文 README 是对上游说明的翻译。程序许可证、版权人和贡献条款不因翻译而改变。
