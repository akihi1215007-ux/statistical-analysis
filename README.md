# statistical-analysis · 研究设计与结果验证

可独立安装的 Codex 技能，覆盖事前设计、方法选择、样本量与效应量、假设诊断及结果核验。
适用于实验、观察研究和预测评估，也保留纯理论／确定性仿真的验证边界。

版本 **2.2-portable**。这是已有技能的便携发布包，不是新的统计方法库。7 个诊断函数的签名与默认值保持不变；综合诊断复用一次分组准备。辅助脚本不能证明独立性、自动选择检验或替代研究设计判断。

## 安装技能

下载本仓库后，把完整的 `skills/statistical-analysis` 文件夹复制到你的技能目录。
按 [Codex 官方技能文档](https://learn.chatgpt.com/docs/build-skills)，当前推荐用户级位置为 `~/.agents/skills/`（Windows：`$env:USERPROFILE\.agents\skills`）。

若现有环境已从 `$CODEX_HOME/skills` 或默认 `~/.codex/skills` 识别技能，可沿用该位置。两种位置择一，避免重复注册；若已有同名目录，先备份，再替换。不要只复制 `SKILL.md`，引用、脚本、许可都需要保留。

本仓库不自动安装、不改主机配置、不包含上传或提交操作。安装后可用 `$statistical-analysis` 显式调用，例如：

> 使用 $statistical-analysis 帮我检查两组测量的比较方案，先明确独立单位和缺失规则。

## Python 依赖与使用

纯设计和文字核验无需 Python 包。运行辅助脚本建议使用 Python 3.12 的项目虚拟环境；安装依赖前先确认环境和授权。

```bash
# 仓库根目录；完整计算与测试，包含默认启用的绘图
python -m pip install -r requirements.txt

# 仅数值诊断（调用时显式传 plot=False）
python -m pip install -r skills/statistical-analysis/requirements.txt
```

安装技能目录内部也保留两份依赖清单：`requirements.txt` 为数值计算，`requirements-plot.txt` 追加 Matplotlib。SciPy 下界覆盖参考示例的 `rng` 参数，Matplotlib 下界覆盖 `boxplot(orientation=...)`；其余版本未锁定，并不表示所有版本组合均已测试。Pingouin、PyMC、ArviZ 仅在对应任务需要时另行评估。

从仓库根目录可直接运行一个数值示例：

```python
import sys
from pathlib import Path

sys.path.insert(0, str(Path("skills/statistical-analysis/scripts").resolve()))
from assumption_checks import check_normality

result = check_normality([1.2, 1.8, 2.1, 2.7, 3.4], plot=False)
print(result)
```

独立安装后，把示例路径换成所安装技能的 `scripts` 路径。7 个公开函数见 [SKILL.md](skills/statistical-analysis/SKILL.md)。默认 `plot=True`；`plot=False` 不加载 `matplotlib.pyplot`。异常值标记不授权删除观测，未拒绝假设不代表假设成立。

## 验证

```bash
python -B -m unittest discover -s skills/statistical-analysis/tests -v
python -B -m unittest discover -s tests -v
```

第一条运行 10 项行为测试（原9项加1项分组复用回归）；第二条把完整技能复制到临时目录，从该目录重跑行为测试，以核验脱离原路径后的可运行性。测试含无图模式、显式绘图、缺失对齐、分组异常点和 OLS 诊断边界。无需 pytest。

本次实测环境：Python 3.12.14、NumPy 2.5.4、pandas 3.0.6、SciPy 1.18.1、statsmodels 0.15.0、Matplotlib 3.11.2。未声称已经验证其他系统／版本组合，测试也不构成任意研究结论的认证。

## 文件与许可

仓库根的 [CHANGELOG.md](CHANGELOG.md) 记录改良与版本；README和LICENSE供发布使用，唯一主入口位于下列安装单元，不在根目录重复注册。

`skills/statistical-analysis/` 是完整安装单元；其中 `references/` 按任务加载，`scripts/` 提供诊断工具，`tests/` 保留行为测试。`tests/test_installation.py` 是仓库层的隔离安装测试。

可选配套技能为 `scientific-toolkit-skill`、`scientific-visualization` 和 `research-writing-skill`，均非必需，也不随本仓库安装。

按 [MIT License](LICENSE) 发布，保留 K-Dense Inc. 完整许可及版权声明；上游固定版本、修改范围见 [第三方归属](skills/statistical-analysis/THIRD_PARTY_NOTICES.md)。
