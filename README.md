<div align="center">

![ScholarFlow Banner](assets/readme/hero.svg)

<br/>

[![Python](https://img.shields.io/badge/Python-3.12+-3776AB?style=for-the-badge&logo=python&logoColor=white)](https://www.python.org/)
[![Flask](https://img.shields.io/badge/Flask-3.0+-000000?style=for-the-badge&logo=flask&logoColor=white)](https://flask.palletsprojects.com/)
[![LLM Support](https://img.shields.io/badge/LLM-Gemini%20%7C%20GLM--4%20%7C%20GPT--4o-8B5CF6?style=for-the-badge&logo=openai&logoColor=white)](#-多模型生态矩阵)
[![License](https://img.shields.io/badge/License-MIT-10B981?style=for-the-badge)](LICENSE)

<br/>

**ScholarFlow** 是一款专为学术研究人员、研究生和科研团队打造的 **AI 驱动文献综述自动化处理系统**。<br/>
通过结合 OCR 视觉解析、多 LLM 并发总结与文献引用匹配算法，实现从 PDF 到结构化综述报告的全流程自动化。

[快速开始](#-快速开始) • [核心特性](#-核心特性) • [系统架构](#-系统架构) • [模型矩阵](#-多模型生态矩阵) • [常见问题](#-常见问题与排障)

</div>

---

## ✨ 核心特性

| 核心功能 | 功能描述 | 技术亮点 |
| :--- | :--- | :--- |
| **🔍 智能文献解析** | 自动读取与解析 PDF 文献，支持扫描件与图片型 PDF | 集成微软 `MarkItDown` 引擎与视觉 LLM OCR |
| **🤖 深度 AI 总结** | 针对研究主题自动萃取研究问题、研究方法、结论与创新点 | 多线程并发分发，支持 Prompt 自定义扩展 |
| **🔗 引用精准匹配** | 自动分析论文文件名与参考文献列表，建立精准关联映射 | 语义匹配算法，自动纠错与置信度校验 |
| **📊 多格式结构输出** | 自动生成包含全量要素的综述表格与关系映射集 | 支持导出为 Excel 可直接查看的 CSV 和 JSON |
| **🌐 Web 实时可视化** | 提供直观优雅的浏览器工作台与实时处理状态观察 | 采用 Server-Sent Events (SSE) 实时推送进度 |
| **🔄 增强容错机制** | 断点续传、配置缓存、健康检查与请求自动重试 | 避开重复请求，极大地降低 API Token 开销 |

---

## ⚡ 系统架构

ScholarFlow 采用了高度模块化与高并发的四阶段流式处理架构：

![ScholarFlow Architecture](assets/readme/architecture.svg)

1. **解析阶段**：递归扫描指定目录下的 PDF 文本与图片，调用 OCR 提取清晰文本。
2. **提取阶段**：通过配置的并发 Worker（如 `max_workers=3`）分派大语言模型抽取学术核心四要素。
3. **匹配阶段**：精准对齐论文与参考文献文本库，自动处理断点续传。
4. **交付阶段**：同步向 Web 界面推送日志，自动导出 `summary_sorted.csv` 报告。

---

## 🚀 快速开始

### 1. 环境准备

需要安装 Python 3.12+ 运行环境：

```bash
# 克隆项目仓库
git clone https://github.com/yourusername/ScholarFlow.git
cd ScholarFlow

# 使用 uv（推荐）或 pip 安装依赖
pip install -r requirements.txt
```

### 2. 配置密钥与参数

从模板创建配置文件：

```bash
# Windows
copy "new_workflow\config example.yaml" "new_workflow\config.yaml"

# Linux / macOS
cp "new_workflow/config example.yaml" "new_workflow/config.yaml"
```

编辑 `new_workflow/config.yaml`，填入您的 API 密钥与偏好配置：

```yaml
api:
  provider: gemini_web  # 支持: gemini | gemini_web | zhipu | openai
  genai_key: "YOUR_GEMINI_API_KEY"
  zhipu_key: "YOUR_ZHIPU_API_KEY"
  openai_key: "YOUR_OPENAI_API_KEY"

proxy:
  url: "http://127.0.0.1:10808"  # 可选：代理服务器地址

concurrency:
  max_workers: 3  # 并发任务数 (建议 1-5)
```

### 3. 启动应用

#### 🌐 方式 A：Web 可视化工作台（推荐）

```bash
cd new_workflow
python app.py
```
启动完成后，在浏览器中打开：**`http://127.0.0.1:18690`**

- 🎨 **一键拖拽** 上传 PDF 文献与参考文献文本
- 📊 **实时观察** SSE 节点处理流与日志输出
- ⚙️ **在线修改** 配置参数并即时保存
- 📥 **在线预览与一键下载** 生成好的 CSV/JSON 报告

#### 🖥️ 方式 B：CLI 命令行批处理

适合集成至自动化脚本或无界面服务器：

```bash
cd new_workflow
python workflow.py
```

产出文件默认存放在 `new_workflow/txts/` 目录：
- `summary_sorted.csv` - 结构化文献综述表
- `literature_summary.json` - 详细文献提取 JSON
- `reference_mapping.json` - 引用匹配图谱 JSON

---

## 🤖 多模型生态矩阵

ScholarFlow 完美适配国内外主流大语言模型，您可以根据成本与网络状况自由切换：

| 提供商 | 默认模型 | 适用场景 / 特性优势 | 推荐并发数 |
| :--- | :--- | :--- | :---: |
| **Gemini Web** | `gemini-web` | **完全免费**，基于 Cookie，适合大批量文献处理 | `1 - 3` |
| **Gemini API** | `gemini-2.0-flash-exp` | 响应速度快，性价比高，原生支持视觉 OCR | `3 - 5` |
| **智谱 AI** | `glm-4-flash` / `glm-4v` | **国内访问稳定**，无需代理，支持扫描件视觉识别 | `3 - 5` |
| **OpenAI** | `gpt-4o-mini` | 逻辑推导强，可通过 OpenRouter 灵活切换 | `2 - 5` |

---

## 📂 项目结构概览

```text
ScholarFlow/
├── assets/
│   └── readme/                      # 🎨 README 视觉 SVG 资源库
│       ├── hero.svg                 #   Hero 头部品牌横幅
│       └── architecture.svg         #   系统处理流程图
├── new_workflow/
│   ├── app.py                       # 🌐 Flask Web 应用服务入口
│   ├── workflow.py                  # 🖥️ CLI 命令行自动化入口
│   ├── config example.yaml          # 📋 配置文件参考模板
│   ├── src/                         # 📦 核心功能引擎
│   │   ├── pdf_processor.py         #   PDF 文件读写与校验
│   │   ├── pdf_to_markdown.py       #   PDF 转 Markdown (MarkItDown / OCR)
│   │   ├── llm_client.py            #   统一 LLM API 客户端封装
│   │   ├── summary_generator.py     #   多线程文献总结提取器
│   │   ├── reference_matcher.py     #   参考文献智能精准匹配器
│   │   ├── task_manager.py          #   任务进度与 SSE 推送服务
│   │   └── results_exporter.py      #   CSV / JSON 结构化导出工具
│   ├── templates/                   # 🎨 Web 界面 HTML 模板
│   └── static/                      # 🎭 前端 CSS / JS 静态资源
├── requirements.txt                 # 📦 项目 Python 依赖清单
└── README.md                        # 📖 项目主文档
```

---

## 🐛 常见问题与排障

<details>
<summary><b>Q: 扫描件 PDF 无法正常识别文字？</b></summary>
<br/>

针对图片型扫描件 PDF，系统会自动调用视觉模型进行 OCR 处理。请确保在 `config.yaml` 中选择支持 Vision 的模型（如 `gemini-2.0-flash-exp` 或 `glm-4v`），并确保 `markitdown` 依赖已正确安装。

</details>

<details>
<summary><b>Q: 出现 API 速率限制 (Rate Limit Exceeded) 怎么办？</b></summary>
<br/>

1. 降低 `config.yaml` 中的 `concurrency.max_workers` 并发数值（设为 `1` 或 `2`）。
2. 使用系统自带的**断点续传**功能：重启任务时，系统会自动跳过已成功生成的文献，无需重复消耗 Token。

</details>

<details>
<summary><b>Q: 智谱 AI 或 OpenAI 提示 key 错误？</b></summary>
<br/>

请检查 `config.yaml` 中 `api.provider` 字段是否与配置的 key 名称一致（例如设置 provider 为 `zhipu` 时需填入 `zhipu_key`）。

</details>

---

## 🤝 贡献与许可

欢迎提交 Issue 和 Pull Request 来完善 ScholarFlow！

- 本项目采用 [MIT License](LICENSE) 开源许可。
- 致谢：[Flask](https://flask.palletsprojects.com/) | [MarkItDown](https://github.com/microsoft/markitdown) | [Google Gemini](https://ai.google.dev/) | [智谱 AI](https://open.bigmodel.cn/)

<br/>

<div align="center">

**⭐ 如果 ScholarFlow 对您的科研与学习有所帮助，欢迎点个 Star 支持一下！**

</div>
