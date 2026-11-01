# 贡献指南

欢迎参与这个 Git 与 GitHub 学习仓库。改进文档、反馈问题和完善协作配置都属于有价值的贡献。

## 项目现状与许可证

仓库包含学习文档、MIT LICENSE、贡献模板及自动化配置；目前没有项目源代码、依赖清单、构建系统或测试套件。不要假定存在 `npm test`、构建脚本或其他开发命令。

参与开发前请阅读仓库的 [LICENSE](https://github.com/Atom-builder64123550/-git-github-/blob/main/LICENSE)，了解使用、修改和分发的要求，具体内容以许可证原文为准。请勿随意更改许可证或版权声明。

## 报告问题与提出建议

1. 在原仓库的 Issues 中搜索，确认是否已有相同或相近的问题。
2. 使用 Bug 报告或新功能建议表单，提供与本次反馈有关的信息。
3. Bug 请提供复现步骤、预期行为和实际行为。提交日志或截图前先移除敏感内容。
4. 对较大的改动，建议先通过 Issue 讨论目标和范围，再开始实现。

两个表单分别使用仓库已有的 `bug` 和 `enhancement` 标签。它们用于分类，不代表维护者已确认问题或承诺实现。

## 获取代码

推荐流程：**Fork 原仓库 → Clone 自己的 Fork → 本地开发**。

先在 [原仓库](https://github.com/Atom-builder64123550/-git-github-) 页面点击 Fork。然后在你自己的 Fork 页面，通过 Code 按钮复制 HTTPS 克隆地址，再执行 `git clone`。

下面是命令示例：请先将 `YOUR-USERNAME` 替换为你自己的 GitHub 用户名。如果你给 Fork 改了名，也需要使用实际仓库名。

```sh
git clone https://github.com/YOUR-USERNAME/-git-github-.git
cd ./-git-github-
git remote add upstream https://github.com/Atom-builder64123550/-git-github-.git
git remote -v
```

`origin` 指向自己的 Fork，`upstream` 指向原仓库。`git remote add upstream` 只需执行一次；如果已有该名称，先用 `git remote -v` 核对地址，不要重复添加。

Download ZIP 可以用于查看文件，但不包含 `.git` 版本历史和分支信息，不推荐用于常规 Git 协作。

## 创建开发分支

每项独立任务创建一个单独分支，不推荐直接在 `main` 上开发。下面以文档改动为例；执行前请先处理好本地尚未提交的修改。

```sh
git switch main
git fetch upstream
git merge --ff-only upstream/main
git switch -c docs/contribution-guide
```

如果快速前进合并失败，说明分支历史存在差异；先检查历史或请维护者协助，不要直接强制覆盖。

推荐分支命名：

- `feat/功能名称`：新功能，例如 `feat/branch-examples`。
- `fix/问题名称`：Bug 修复，例如 `fix/broken-link`。
- `docs/文档名称`：文档更新，例如 `docs/contribution-guide`。
- `refactor/模块名称`：重构，例如 `refactor/syntax-check`。

示例使用简短的英文和连字符，便于在终端中操作。这些属于贡献约定，当前配置并没有让 GitHub 自动强制执行分支命名。

## 开发规范与本地检查

- 每次修改保持目标明确，尽量避免无关改动。
- 遵循已有文件和代码结构；文档中的命令应能用于所描述的场景。
- 不提交密钥、访问令牌、私人数据或不必要的依赖文件。
- 不将第三方依赖、缓存或生成文件混入源码变更；必要时维护准确的 `.gitignore`。
- 提交前检查内容、链接和适用的源代码语法，并如实记录检查结果。

当前没有项目源代码，因此不存在必须安装的项目依赖，也没有通用的本地测试或构建命令。纯文档 PR 可以说明已核对的内容与链接，并将源代码语法检查标为“不适用”。

现有工作流保留了对普通 Python、JavaScript 和 Bash 文件的语法检查支持，分别使用 Python 内存编译、`node --check` 和 `bash -n`，不执行被检查的代码。若以后添加这些语言，可按实际文件使用下列方式；示例路径必须替换成真实文件，不需要为纯文档修改安装这些工具：

```sh
# Python：只编译内存中的代码，不运行文件，也不生成 __pycache__。
python -c "import pathlib,sys; p=pathlib.Path(sys.argv[1]); compile(p.read_bytes(), str(p), 'exec', dont_inherit=True)" path/to/changed.py

# 普通 JavaScript：语法检查，不执行脚本。
node --check path/to/changed.js

# Bash：解析语法，不执行脚本。
bash --noprofile --norc -n -- path/to/changed.sh
```

这些检查不证明程序的功能正确。添加新语言、JSX/TypeScript、其他 Shell 方言或需要特殊解析器的代码时，请同时调整语法检查工具；不要用完整类型检查、测试或构建结果冒充纯语法检查。

## 自动语法检查的范围

工作流位于 [syntax-check.yml](workflows/syntax-check.yml)，在 Push 及 PR 创建、更新、重新打开时触发。

- `Source inventory` 先发现候选源文件；当前没有源代码，会在日志和运行摘要中明确说明。
- 没有候选源文件时，`Syntax check` Job 显示为 **Skipped**，不将“未检查”描述为“语法全部通过”。
- 有适用源文件时才运行语法检查：存在语法错误或检查器失败则 Job 失败；全部检查成功才正常结束。
- 沿用 Ubuntu 24.04 运行器自带的 Python、Node.js 和 Bash，不安装项目依赖。
- 依赖/缓存目录（如任意层级的 `node_modules`、`.venv`）和根目录构建产物（如 `dist`、`build`、`out`、`coverage`）会被排除。`src/build` 等非根目录同名文件夹不会被当成构建产物排除。
- 常见压缩/生成文件，以及通过 Git 属性 `linguist-generated` 或 `linguist-vendored` 明确标记的文件会被排除。请仅对真正的生成文件或第三方代码设置这些属性，不能用它们跳过自己维护的源代码。
- 常见但尚未配置检查器的语言会产生明确错误；该扩展名列表不代表支持所有语言。新增语言时应审查并更新发现范围和解析器，避免漏检。

工作流仅使用 `contents: read` 权限，不需要贡献者提供密钥，也没有 `pull_request_target` 高权限执行流程。远程所用运行时与本地版本可能不同；新增源码时应确认版本兼容性。

## 提交 Pull Request

完整流程：**Commit → Push 到自己的 Fork → 创建 PR → 填写 PR 模板 → 等待审核**。

1. 用 `git status` 和 `git diff` 检查修改；新文件也要打开核对内容。
2. 用 `git add` 暂存与本次任务相关的文件，再用 `git diff --cached` 核对待提交内容。
3. 执行 `git commit`，用简洁的提交说明描述修改目的。首次提交前需配置真实的 Git 提交姓名和邮箱；这不是 GitHub 登录凭据。
4. 将开发分支 Push 到自己的 Fork。以前面的分支为例：

   ```sh
   git push -u origin docs/contribution-guide
   ```

5. 在 GitHub 创建 PR：目标仓库选择原仓库，目标分支选择 `main`，来源选择自己 Fork 中的开发分支。
6. 填写自动出现的 PR 模板；不适用的部分可以删除或注明“不适用”，不要勾选未完成的检查。
7. 如果 PR 解决了原仓库的 Issue，在描述中填写 `Closes #123`，并将 `123` 替换为实际编号；合并到默认分支后可自动关闭关联 Issue。仅引用、不希望关闭时，使用 `Refs #123`。没有关联 Issue 时填写“无”，不要保留示例编号。
8. 等待审核；按审核意见在同一分支继续修改、Commit 和 Push，现有 PR 会自动更新，并重新运行适用的检查。

## 审核与合并

PR 由项目维护者审核，维护者可以要求补充说明或修改实现。满足项目规范、处理完审核意见且通过必要检查后，维护者才会考虑合并；提交 PR 不代表一定会被合并。

这里描述的是协作约定。分支保护、必须审核人数和必需状态检查需要管理员另外在 GitHub 网页配置，单靠本指南或 PR 检查清单不会自动强制执行。工作流产生运行记录后，应按实际显示的名称同时要求 `Source inventory` 和 `Syntax check`，防止文件发现步骤失败却被遗漏。没有源代码时，跳过语法检查不等于已验证项目代码。
