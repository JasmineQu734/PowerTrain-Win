# PowerTrain-Win — 天枢·变电孪生智训智能体平台（Windows 免安装交付包 v1.2.1）

变电站 3D 数字孪生 + 本地微调诊断智能体的一体化 Windows 交付包。
自带精简 JRE、绿色版 MySQL、llama.cpp 推理运行时和训练好的模型（Qwen2.5-3B 基座 + 两个 LoRA 适配器），**不需要安装任何环境**。

## 下载

因为 GitHub 单文件上限为 2 GiB，v1.2.1 拆成两个分卷，都在 [Releases](../../releases) 页面：

| 文件 | 大小 | 内容 |
|---|---|---|
| `PowerTrain-Win-1.2.1-part1-app.zip` | ~400 MB | 后端 jar、前端（含 3D 园区）、建库脚本、JRE、MySQL、llama.cpp 运行时、全部启动/停止脚本与文档 |
| `PowerTrain-Win-1.2.1-part2-models.zip` | ~1.9 GB | `models\` 目录：Qwen2.5-3B-Instruct Q4_K_M 量化基座（1.8 GB）+ 个人诊断 LoRA（58 MB）+ 公司诊断 LoRA（58 MB） |

每个分卷的 SHA-256 见 Release 说明。

## 运行步骤（评委视角）

1. 把 **两个分卷解压到同一个文件夹**（两个 zip 内部都是 `PowerTrain-Win-1.2.1\` 顶层目录，先后解压会自动合并成完整目录）。
2. 进入 `PowerTrain-Win-1.2.1\`，双击 **`start.bat`**。脚本自动完成 8 步：
   检测环境 → 生成配置（已预置，默认指向本地模型）→ 检查端口 → 初始化数据库（53 张表）→ **自动加载两个本地模型并各做一次真实推理验证** → 启动后端与网页 → 等待就绪 → 自动打开浏览器并打印访问地址。
3. 登录（演示账号统一口令见启动窗口与《使用说明.txt》）：
   - `zhangwei` 员工 · 个人诊断（走 8000 端口本地模型）
   - `wangzong` 老总 · 公司运行诊断（走 8002 端口本地模型）
   - `limin` 班组长 / `admin` 管理员
4. 演示结束双击 **`stop.bat`** 一次性停止后端、数据库与两个模型进程。

## 系统要求

- Windows 10/11 x64，8 GB 内存以上
- 磁盘剩余空间 ≥ 6 GB
- 浏览器需支持 WebGL2（Edge / Chrome 均可，用于 3D 园区）
- 显卡可选：有 NVIDIA/AMD/Intel 显卡自动走 Vulkan 加速（双模型共约 4.6 GB 显存，8G 显卡实测通过）；无显卡自动回退 CPU，结论一致、速度变慢（约 8-15 tok/s）
- 完全离线可运行，数据不出本机

## 常用命令

| 命令 | 作用 |
|---|---|
| `start.bat` | 一键启动全部服务 + 两个本地模型 |
| `start.bat -Check` | 只做环境体检，不建库不起服务 |
| `stop.bat` | 停止全部服务并释放资源 |
| `启动Agent.bat` | 只启动/重启两个本地模型 |
| `停止Agent.bat` | 只停止本地模型 |

## 目录结构

```
PowerTrain-Win-1.2.1\
├─ app\backend.jar        预构建后端（网页与接口同端口）
├─ app\webapp\            预构建前端（含 3D 数字孪生园区）
├─ app\db-init\           建库脚本（001 结构 → 002~009 种子 → 010~013 增量）
├─ runtime\jre\           jlink 裁剪的精简 JRE（44 MB）
├─ runtime\mysql\         绿色版 MySQL（161 MB，仅监听 127.0.0.1）
├─ runtime\llama\         llama.cpp b12040 预编译运行时（cpu + vulkan 双后端）
├─ models\                量化基座 + 两个 LoRA（构成、转换命令与 SHA-256 见 models\README.md）
├─ tools\                 启动/停止脚本（PowerShell）
├─ start.bat / stop.bat   一键入口
└─ 使用说明.txt / README.md
```

## 模型说明

- 基座：Qwen2.5-3B-Instruct（Apache-2.0），f16 转 GGUF 后量化为 Q4_K_M。
- 适配器：`sft16_3b`（个人学习诊断）、`sft17_exec`（公司运行诊断），由 LLaMA-Factory SFT 训练后转 GGUF，推理时挂载到共享基座上。
- 接线：`.env` 预置 `AI_BASE_URL=http://127.0.0.1:8000/v1` 与 `AI_EXEC_BASE_URL=http://127.0.0.1:8002/v1`，由 `start.bat` 自动拉起，无需任何配置。
- 详细构成、训练链路、校验值见包内 `models\README.md`。

## 3D 素材授权

3D 园区中的设备模型采用 CC BY 4.0 授权的开源素材（出处与清单见包内 `app\webapp\twin\models\README.txt`），其余代码、脚本与文档为本项目自研。
