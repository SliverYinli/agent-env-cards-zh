# 实例：一个中文 Windows 科研环境的卡（脱敏节选）

下面是真实卡片删掉路径与软件清单后的样子。**结构可以照抄，内容必须自己填**——
尤其第四节，那些错误只有你自己踩过才知道长什么样。

---

## AGENTS.md

```markdown
- 涉及本机路径、命令执行、文件读写，或遇到环境报错时，先读 `D:\agent\tools\env-card.md`；
  该卡未覆盖时再读同目录 `env-ref.md`。
```

一行。这是唯一常驻上下文的东西。

---

## env-card.md（节选）

```markdown
# 环境卡（热卡）

> 最后验证：2026-11-21 ｜ 下次复核：2027-02
> 默认不读冷卡 env-ref.md。仅当本卡未覆盖或命令执行失败时才读。

## 一、固定入口

| 代号 | 实际路径 |
|---|---|
| `PY` | `D:\envs\agent\python.exe` |
| `TOOLS` | `D:\agent\tools` |
| `RSCRIPT` | `D:\R\R-4.5.2\bin\x64\Rscript.exe` |
| `ARCPY` | `C:\Program Files\ArcGIS\Pro\bin\Python\envs\arcgispro-py3\python.exe` |

## 二、常用命令

| 任务 | 命令 |
|---|---|
| 读 docx / xlsx / pdf | `<PY> "<TOOLS>\read.py" <文件>` |
| 运行 R | `<RSCRIPT> <脚本>` |
| 运行 arcpy | `& "<ARCPY>" <脚本>`（启动约 8 秒） |
| 安装 Python 包 | `conda install -p D:\envs\agent -c <镜像> <包>` |

## 三、硬约束

1. 编码：改文本/代码只用专用工具，禁 PowerShell 文本 cmdlet（GBK 会毁 UTF-8）。
2. Python：直调 `<PY>`，不 activate；装包只走 conda `-p`，禁 pip。
3. Word COM：仅晚绑定，try/finally 里 Quit()。

## 四、禁区

- 未装，勿调：pandoc、LaTeX、LibreOffice、WPS、QGIS、7-Zip、ffmpeg
```

注意三件事：

- **路径只在第一节出现一次**，正文用 `<PY>` 代号。改机器时只改一处。
- **"未装，勿调"排在前面**，因为它拦下的无效尝试最多。
- **硬约束写的是"禁什么"**，不是"推荐什么"——前者可执行，后者是建议。

---

## env-ref.md（节选，第四节是重点）

```markdown
## 三、Office 文档结构（python-docx / OOXML）

1. run 拆分：格式一变就断 run，跨 run 替换会失效；先按 run 试探。
2. 域保护：引文、交叉引用、自动编号属域（w:fldChar / w:instrText），
   整段重写会摧毁，只能改纯文本 run。
3. 字符缩进：缩进可能在 w:ind/@w:leftChars，left_indent = None 清不掉，
   须删整个 w:ind。
4. 内容控件：自动目录在 w:sdt 内，遍历 w:p 找不到。
5. 表格列：删列须同步删 w:tblGrid/w:gridCol 与每行 w:tc。

## 四、报错对照

| 报错 / 异常现象 | 根因与解法 |
|---|---|
| Word 未能引发事件 | 沙箱模式禁用 COM → 切全权限 |
| TYPE_E_ELEMENTNOTFOUND | 误用 COM 早绑定 → 改晚绑定 |
| 静默退出、码 0xC06D007F | 缺 sitecustomize.py，DLL 路径未注入 |
| 中文方块 / 缺负号 / GDAL_DATA is not defined | sitecustomize.py 或 MATPLOTLIBRC 未生效 |
| pip install 写 .whl 被拒 | 误走 pip → 改 conda install -p |
| import fitz 报 FutureWarning | 改 import pymupdf |
| 控制台乱码 / UnicodeEncodeError | PS 默认 GBK → 文件强制 UTF-8，或先 chcp 65001 |
| 中文路径 No such file（GDAL / rasterio 尤甚） | ①写文件时用 UTF-8；②拷至工作空间内 ASCII 路径 |
| Office 受保护视图黄条 / 打开报错或只读 | 文件带 MotW（Zone.Identifier 流）→ Unblock-File |
| conda env list 仅见 base | 环境不在默认位置，统一走 -p 完整路径 |
```

这十行是整个方案里最花时间、也最难被替代的部分。

**它不能从别人的库里抄**——每个条目背后都是你这台机器上真实发生过的一次失败。
但**格式可以照抄**：一行一条，左边是现象（最好是报错原文的关键片段），右边是根因加解法。

筛选标准只有三条：**能稳定复现、换个项目还会遇到、同类合并成一行。**
不满足这三条的，宁可记在最开始的笔记里，也不要进这张表——表一旦开始膨胀，你就懒得看它了。
