# Woodpecker Harness 运行依赖包

Woodpecker Harness 客户端的可选运行依赖。装好客户端后按需下载本仓的包，**无需联网即可离线使用 Office 文档能力**（生成 / 编辑 / 校验 docx、xlsx、pptx，以及 PDF 转换）。

本仓库只有第三方运行时（Python、Node.js、PyPI 上的开源库），不含任何客户端源码。

## 各平台状态

| 平台 | 包 | 状态 |
|---|---|---|
| Windows 10/11 x64 | `primary-runtime-win32-x64-0.2.0-rc.2.zip` | **已在真机验证通过**（2026-11-08），见下方说明 |
| macOS（Apple 芯片） | 暂无 | 走内部地址分发，不在本仓 |

## Windows 安装步骤

1. 下载 `primary-runtime-win32-x64-0.2.0-rc.2.zip`（约 56 MB）
2. 解压，得到 `dsh-primary-runtime` 文件夹
3. 把它整个移动到：

   ```
   %USERPROFILE%\.woodpecker\dsh-runtimes\
   ```

   移动完成后路径应当是：

   ```
   C:\Users\<你的用户名>\.woodpecker\dsh-runtimes\dsh-primary-runtime\runtime.json
   ```

   > 注意：目录名必须是 `dsh-primary-runtime`，**不是** `primary-runtime`——客户端只认前一个名字。
   > 如果该目录已存在，说明客户端自己下过一份，删掉旧的再放新的。

4. 重启 Woodpecker Harness。左上角菜单 →「诊断信息」里应显示运行时正常；也可以直接让 agent 生成一个 docx 试试。

## 包里有什么

| 组件 | 版本 | 用途 |
|---|---|---|
| Python | 3.12.10（embeddable） | 运行 Office 脚本 |
| Node.js | 24.21.0 | LibreOffice 渲染 CLI 的解释器 |
| python-docx | 1.2.0 | Word 文档 |
| python-pptx | 1.0.2 | PowerPoint |
| openpyxl | 3.1.5 | Excel 读写 |
| XlsxWriter | 3.3.1 | Excel 写入 |
| Pillow | 13.0.0 | 图像处理 |
| lxml | 6.1.3 | OOXML 解析 |

LibreOffice 渲染引擎不在此包内——它已经随 Windows 安装包一起分发。

## 验证情况

**已在真实 Windows 机器上验证通过（2026-11-08）**，覆盖：

| 测试 | 结果 |
|---|---|
| 包内 Python / Node 直接运行 | ✅ Python 3.12.10、Node 24.21.0 |
| 全部 Python 库导入 | ✅ docx / pptx / openpyxl / PIL / lxml / xlsxwriter |
| 生成 docx（中文标题、正文、3×3 表格） | ✅ |
| `check_office.py` 结构校验 | ✅ verdict: pass |
| python-docx 读回中文文本 | ✅ 无乱码 |
| xlsx / pptx 生成与校验 | ✅ pass |
| LibreOffice Kit docx → PDF / PNG | ✅ 转换与渲染正常，中文与表格无异常 |
| 客户端带运行时完整启动 | ✅ 无 fatal、无 deps-missing |

### 已知问题

**1. 系统 VC++ 运行库过旧时 PDF 转换会崩溃**

现象：转 PDF 时进程以 `0xC0000005` 退出，事件日志指向 `MSVCP140.dll`。
原因：包内 LibreOffice 26.8 引擎需要 VC++ 运行库 14.4x 以上，而旧系统可能只有 14.29（2020 年版）。

解决（任选其一）：

- **推荐**：装微软官方最新 VC++ 2015-2022 x64 运行库 <https://aka.ms/vs/17/release/vc_redist.x64.exe>，系统级生效
- 应急：从任意自带新版运行库的程序（如 Edge）复制 `msvcp140.dll`、`msvcp140_codecvt_ids.dll`、`vcruntime140.dll`、`vcruntime140_1.dll`、`concrt140.dll` 到
  `...\resources\runtime\dsh\node_modules\@deepseek-ai\libreoffice-kit-win32-x64\bin\`

**2. 目录内必须有 `dependencies/node/node_modules`**

宿主启动时会校验该目录为目录类型，缺失会报 `primary runtime: expected directory`。
早期版本的 zip 漏了这个空目录，已在 2026-11-08 重打包补上（现在包内带 `README.txt` 占位）。

## 校验值

```
819874da8730e151bce99d0ac882d7c94bd508a82d3d944cbd58e282bdee7168  primary-runtime-win32-x64-0.2.0-rc.2.zip
a3789af8a6b486dad93a3d728b414cefe0ee8eaf81d954b561e921be54d09086  primary-runtime-win32-x64-0.2.0-rc.2.tar.zst
```
