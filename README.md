# Woodpecker Harness 运行依赖包

Woodpecker Harness 客户端的可选运行依赖。装好客户端后按需下载本仓的包，**无需联网即可离线使用 Office 文档能力**（生成 / 编辑 / 校验 docx、xlsx、pptx，以及 PDF 转换）。

本仓库只有第三方运行时（Python、Node.js、PyPI 上的开源库），不含任何客户端源码。

## 各平台状态

| 平台 | 包 | 状态 |
|---|---|---|
| Windows 10/11 x64 | `primary-runtime-win32-x64-0.2.0-rc.2.zip` | **未在真机验证**，见下方说明 |
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

## 未验证说明

这个包是在 macOS 上交叉组装的（Windows 版 Python + PyPI 的 `win_amd64` wheel），**没有在真实的 Windows 机器上完整跑过一次 Office 生成流程**。

已经核对过的：

- 目录结构符合客户端校验（`runtime.json`、`python.exe`、`Lib\site-packages` 三项齐备）
- `python.exe`、`python312.dll`、`node.exe` 均为 PE32+ x86-64
- `python312._pth` 已加入 `Lib\site-packages` 并启用 `import site`
- 包内无任何内部主机名或密钥

没核对过的：实际执行 `python.exe -c "import docx"` 以及文档生成、PDF 转换。

遇到问题请把日志发出来：

```
%USERPROFILE%\.woodpecker\logs\host.log
```

## 校验值

```
0ddca1a871b9305563303435ed65682b14f1b152e038e1d86ef2d22d204ec9cc  primary-runtime-win32-x64-0.2.0-rc.2.zip
a3789af8a6b486dad93a3d728b414cefe0ee8eaf81d954b561e921be54d09086  primary-runtime-win32-x64-0.2.0-rc.2.tar.zst
```
