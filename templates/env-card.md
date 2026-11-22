# 环境卡（热卡）

> 最后验证：YYYY-MM-DD ｜ 下次复核：YYYY-MM
> **默认不读冷卡** `env-ref.md`。仅当本卡未覆盖（软件清单、领域知识、报错排查）或命令执行失败时才读。

## 一、固定入口（路径只在这里写一次）

| 代号 | 实际路径 |
|---|---|
| `PY` | `<ENV>\python.exe` |
| `TOOLS` | `<TOOLS>` |
| `RSCRIPT` | `<R_HOME>\bin\x64\Rscript.exe` |
| `ARCPY` | `<ARCGIS>\bin\Python\envs\arcgispro-py3\python.exe` |

下文命令里的 `<PY>`、`<TOOLS>` 等一律替换为上表路径。

## 二、常用命令（优先复用，勿重写）

| 任务 | 命令 / 做法 |
|---|---|
| 读 docx / xlsx / pdf | `<PY> "<TOOLS>\read.py" <文件>` |
| 运行 Python | `<PY> <脚本>` |
| 运行 R | `<RSCRIPT> <脚本>` |
| 安装 Python 包 | `conda install -p <ENV> -c <镜像> <包>` |
| 修改既有 docx | `python-docx`（跨 `run`、域保护的坑见冷卡） |

## 三、硬约束

1. **编码**：改文本 / 代码只用专用工具，禁 PowerShell 文本 cmdlet（默认 GBK 会毁 UTF-8）。
2. **确认**：删文件或覆盖原始数据 / 文档前须确认。
3. **Python**：直调 `<PY>`，不 `activate`；装包只走 conda `-p`，禁 `pip`。
4. **路径**：输出与临时文件只写当前工作空间。

## 四、禁区与维护

- **未装，勿调**：`<把没装的工具列在这里>`
- **卡怎么长**：新坑只往冷卡「四」加一行；本卡原则上不加条目，加了就要删一条。
- **复核**：每次复核更新顶部日期；某个条目连续两轮用不上，删掉——**卡的目标是短，不是全**。
