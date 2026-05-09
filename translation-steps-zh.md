# Markdown 中文译文生成步骤

本文记录本仓库英文 Markdown 文件批量生成中文译文文件的操作流程。译文文件命名规则为：在原 Markdown 文件名后追加 `-zh`，例如 `README.md` 生成 `README-zh.md`。原文文件不修改。

## 1. 检查 Markdown 文件

先确认仓库中的 Markdown 文件数量和工作区状态：

```sh
rg --files -g '*.md'
find . -maxdepth 2 -type f -name '*.md' -print | wc -l
git status --short
```

本次处理的正文 Markdown 文件共 51 个，不包含隐藏目录和临时虚拟环境中的 Markdown 文件。

## 2. 创建翻译虚拟环境

在项目根目录创建专用虚拟环境：

```sh
python3 -m venv .venv-translate
```

安装离线翻译库：

```sh
.venv-translate/bin/pip install argostranslate
```

## 3. 安装英译中模型

下载 Argos Translate 的语言包索引，并安装 English -> Chinese 模型：

```sh
.venv-translate/bin/python -c "from argostranslate import package; package.update_package_index(); pkgs=package.get_available_packages(); p=next(p for p in pkgs if p.from_code=='en' and p.to_code=='zh'); path=p.download(); package.install_from_path(path); print('installed', p)"
```

安装完成后可用一个小样本确认翻译器可用，并检查 Markdown 链接、HTML、代码片段是否会被破坏。

## 4. 编写批量转换脚本

批量脚本的核心处理策略：

- 跳过隐藏目录，例如 `.github`、`.venv-translate`。
- 跳过已生成的 `*-zh.md` 文件。
- fenced code block 使用 ``` 或 `~~~` 包裹的代码块整体保留原样。
- 缩进代码块保留原样。
- inline code、Markdown 链接目标、URL、HTML 标签尽量保留结构。
- 包含 `|` 的表格或提示框行按单元格翻译，再用原 `|` 拼回。
- 译文写入同级目录，不覆盖原文。

本次实际运行的脚本放在临时目录 `/private/tmp/translate_md_zh.py`，运行方式如下：

```sh
.venv-translate/bin/python /private/tmp/translate_md_zh.py /Users/huangwenhao/FrontProjects/learn/You-Dont-Know-JS
```

## 5. 校验生成结果

确认生成 51 个中文译文文件：

```sh
find . -path './.venv-translate' -prune -o -name '*-zh.md' -print | sort | wc -l
```

确认原文文件均有对应译文，且 fenced code block 与原文逐块一致：

```sh
python3 - <<'PY'
from pathlib import Path

root = Path(".")
files = sorted(
    p for p in root.rglob("*.md")
    if "-zh" not in p.stem and not any(part.startswith(".") for part in p.parts)
)

def fences(s):
    out = []
    cur = []
    in_fence = False
    mark = ""
    for line in s.splitlines():
        st = line.lstrip()
        if st.startswith("```") or st.startswith("~~~"):
            m = st[:3]
            if not in_fence:
                in_fence = True
                mark = m
                cur = [line]
            elif m == mark:
                cur.append(line)
                out.append("\n".join(cur))
                cur = []
                in_fence = False
                mark = ""
            else:
                cur.append(line)
        elif in_fence:
            cur.append(line)
    return out

bad = []
missing = []
for p in files:
    z = p.with_name(f"{p.stem}-zh{p.suffix}")
    if not z.exists():
        missing.append(str(p))
        continue
    if fences(p.read_text()) != fences(z.read_text()):
        bad.append(str(p))

print("source_files", len(files))
print("missing", len(missing))
print("fence_mismatch", len(bad))
if missing:
    print("\n".join(missing[:20]))
if bad:
    print("\n".join(bad[:20]))
PY
```

检查是否有翻译占位符残留：

```sh
rg 'ZQ+[A-Z0-9]*Q?X?Z|ZXQ|QXZ|\bQQ\b' -g '*-zh.md'
```

最终校验结果：

```text
source_files 51
missing 0
fence_mismatch 0
```

## 6. 查看 Git 状态

确认原文未修改，只新增中文译文文件和本文档：

```sh
git status --short
```

`.venv-translate` 是本次翻译所用的本地虚拟环境，按要求保留。
