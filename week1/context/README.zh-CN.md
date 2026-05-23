# 支持上下文感知与消融实验的 AI Agent

这是一个高级 AI Agent 示例项目，支持多个大模型服务商（SiliconFlow Qwen、字节跳动 Doubao、Moonshot Kimi），用于通过系统化的消融实验展示上下文组件对 Agent 行为和性能的重要影响。

## 项目概览

本项目实现了一个上下文感知 AI Agent，集成 PDF 解析、货币转换、计算器和代码解释器等工具，并提供完整的消融测试流程，用于观察不同上下文组件如何影响 Agent 的决策、执行效率和任务完成质量。

### 核心特性

- **多模型服务商支持**：支持 SiliconFlow（Qwen）、Doubao（字节跳动）和 Kimi（Moonshot）
- **多工具 Agent**：支持 PDF 解析、货币转换、数学计算和 Python 代码执行
- **上下文模式**：提供 5 种上下文配置，用于消融实验
- **交互与批处理模式**：既可以运行单个任务，也可以执行完整测试套件
- **对话历史**：在同一会话中保留上下文，支持多轮任务
- **详细分析**：输出性能指标、可视化图表和完整报告

## 支持的 LLM 服务商

### Doubao（字节跳动）- 默认

- **模型**：`doubao-seed-1-6-thinking-250715`（可自定义）
- **API**：通过火山引擎提供 OpenAI 兼容接口
- **适合场景**：高级推理、快速响应、中英文任务

### SiliconFlow

- **模型**：`Qwen/Qwen3-235B-A22B-Thinking-2507`（可自定义）
- **API**：OpenAI 兼容接口
- **适合场景**：复杂推理任务、详细分析

### Kimi（Moonshot AI）

- **模型**：`kimi-k2-0905-preview`（K2 模型）
- **API**：通过 Moonshot 平台提供 OpenAI 兼容接口
- **适合场景**：高级推理、多轮对话、中英文任务
- **特性**：支持上下文缓存，便于优化成本

## 系统架构

### 上下文组件

1. **完整上下文（Full Context）**：包含所有组件的完整 Agent
2. **无历史记录（No History）**：不记录历史工具调用
3. **无推理过程（No Reasoning）**：不进行策略规划
4. **无工具调用（No Tool Calls）**：不能执行外部工具
5. **无工具结果（No Tool Results）**：无法看到工具执行结果

### 可用工具

- **`parse_pdf(url)`**：下载并提取 PDF 文档文本
- **`convert_currency(amount, from, to)`**：进行货币转换
- **`calculate(expression)`**：计算简单数学表达式
- **`code_interpreter(code)`**：执行 Python 代码，用于复杂计算、汇总和数据处理

## 环境要求

- Python 3.8+
- 至少一个支持服务商的 API Key：
  - **SiliconFlow**：从 [SiliconFlow](https://siliconflow.cn) 获取
  - **Doubao（字节跳动）**：从 [火山引擎](https://www.volcengine.com/) 获取
  - **Kimi（Moonshot）**：从 [Moonshot 平台](https://platform.moonshot.cn/) 获取

## 示例任务

系统内置 5 个示例任务，用于展示不同能力：

1. **简单货币转换**：基础多币种换算
2. **多币种预算分析**：跨办公室复杂费用分析
3. **PDF 财务分析**：解析并分析财务文档
4. **投资增长计算**：复利计算与货币转换
5. **综合财务报告**：使用所有工具完成完整工作流

这些示例用于展示 Agent 能力，以及上下文消融对任务表现的影响。

## 快速开始

### 1. 安装依赖

```bash
# 进入项目目录
cd projects/week1/context

# 安装依赖
pip install -r requirements.txt

# 复制并配置环境变量
cp env.example .env
# 编辑 .env，加入你的 API Key（SILICONFLOW_API_KEY、ARK_API_KEY 或 MOONSHOT_API_KEY）
```

### 2. 配置服务商

```bash
# Doubao（字节跳动）- 默认
export ARK_API_KEY=your_key_here
python main.py

# SiliconFlow（Qwen）
export SILICONFLOW_API_KEY=your_key_here
python main.py --provider siliconflow

# Kimi（Moonshot）
export MOONSHOT_API_KEY=your_key_here
python main.py --provider kimi

# 或指定自定义模型
python main.py --model doubao-seed-1-6-thinking-250715
```

### 3. 测试 Kimi 集成

```bash
# 快速测试 Kimi K2 模型
export MOONSHOT_API_KEY=your_key_here
python test_kimi.py

# 在主程序中使用 Kimi
python main.py --provider kimi --mode interactive

# 使用 Kimi 运行消融实验
python main.py --provider kimi --mode ablation
```

### 4. 运行交互模式（推荐）

```bash
# 默认使用 Doubao
python main.py --mode interactive

# 使用 SiliconFlow
python main.py --mode interactive --provider siliconflow
```

交互模式中可使用以下命令：

- 输入 `samples` 查看预置任务
- 输入 `sample 3` 测试 PDF 解析
- 输入 `providers` 查看可用服务商
- 输入 `provider kimi` 切换服务商
- 输入 `status` 查看当前配置
- 输入 `help` 查看全部命令

### 5. 运行示例任务

```bash
# 不带任务参数时，从示例任务中选择
python main.py --mode single

# 指定服务商
python main.py --mode single --provider doubao

# 提供自定义任务
python main.py --mode single \
  --task "Convert $1000 USD to EUR, GBP, and JPY. Calculate the average." \
  --context-mode full \
  --provider siliconflow
```

### 6. 运行消融实验

```bash
# 使用默认服务商
python main.py --mode ablation

# 使用 Doubao
python main.py --mode ablation --provider doubao
```

## 消融实验

消融实验会系统性移除某些上下文组件，以观察它们对 Agent 的影响。

### 测试场景

一个复杂财务分析任务，通常需要：

1. 解析 PDF 文档
2. 进行多次货币转换
3. 执行数学计算
4. 汇总最终结果

### 预期行为

| 上下文模式 | 预期行为 | 影响 |
|------------|----------|------|
| **Full** | 完整成功执行 | 基准表现 |
| **No History** | 可能重复操作，效率下降 | 容易重复调用工具 |
| **No Reasoning** | 执行方式不够结构化，可能出错 | 缺少策略规划 |
| **No Tool Calls** | 任务基本失败 | 无法与外部世界交互 |
| **No Tool Results** | 结论可能错误 | 无法根据反馈调整 |

### 运行测试

```bash
# 运行完整消融实验
python ablation_tests.py

# 将生成：
# - ablation_study_results.png（可视化图表）
# - ablation_study_report.md（详细报告）
# - ablation_results.json（原始数据）
```

## 理解实验结果

### 性能指标

- **成功率**：任务是否正确完成
- **执行时间**：完成任务的总耗时
- **迭代次数**：Agent 与模型交互的轮数
- **工具调用次数**：外部工具调用总数
- **推理步骤数**：策略规划步骤数

### 示例输出

```text
ABLATION STUDY RESULTS
================================================================================
| Test Name                      | Success | Time   | Iterations | Tool Calls |
|--------------------------------|---------|--------|------------|------------|
| Baseline - Full Context        | Yes     | 12.3s  | 5          | 8          |
| No Historical Tool Calls       | Yes     | 18.7s  | 8          | 12         |
| No Reasoning Process           | Yes     | 25.4s  | 10         | 15         |
| No Tool Call Commands          | No      | 3.2s   | 2          | 0          |
| No Tool Call Results           | No      | 15.6s  | 10         | 10         |
```

## 关键洞察

### 1. 工具调用是基础能力

没有工具调用能力时，Agent 无法与外部系统交互，复杂任务通常无法完成。

### 2. 工具结果提供关键反馈

如果看不到工具执行结果，Agent 会在“盲目”状态下继续推理，容易得出错误结论或陷入无效循环。

### 3. 推理过程提升效率

策略规划可以减少无效迭代和工具调用，从而提升速度与准确性。

### 4. 历史上下文避免重复

历史信息有助于避免重复操作，并在多轮任务中保持一致性。

## 高级用法

### 交互模式命令

| 命令 | 说明 |
|------|------|
| `samples` | 显示所有示例任务 |
| `sample <n>` | 运行第 n 个示例任务 |
| `providers` | 列出所有可用 LLM 服务商 |
| `provider <name>` | 切换服务商，例如 `provider kimi` |
| `modes` | 列出可用上下文模式 |
| `mode <name>` | 切换上下文模式，例如 `mode no_history` |
| `status` | 显示当前配置 |
| `reset` | 重置 Agent 轨迹并清空历史 |
| `create_pdfs` | 生成用于测试的示例 PDF |
| `quit` | 退出交互模式 |

提示符会显示当前服务商，例如 `[KIMI]>` 或 `[DOUBAO]>`。

### 对话历史

Agent 会在交互会话中保留历史：

- **持久上下文**：记住同一会话中的问题和回答
- **多轮对话**：可以引用之前提到的信息
- **工具调用记忆**：可以引用之前的工具执行结果
- **按需重置**：使用 `reset` 清空历史并重新开始

示例对话：

```text
[DOUBAO]> Remember that our budget is $10,000. Calculate 15% of it.
# Agent 会计算并记住预算

[DOUBAO]> Now convert that 15% amount to EUR
# Agent 会使用之前计算出的金额

[DOUBAO]> What was our original budget?
# Agent 会回忆起之前提到的 $10,000
```

### 自定义任务

可以创建自己的测试场景：

```python
from agent import ContextAwareAgent, ContextMode

agent = ContextAwareAgent(api_key, ContextMode.FULL)
result = agent.execute_task("""
    Download the PDF from https://example.com/report.pdf,
    extract all monetary values, convert them to EUR,
    and calculate the total.
""")
```

### 创建测试 PDF

```bash
python create_sample_pdf.py
# 会创建 test_pdfs/ 目录和示例财务报告
```

### 配置项

可以编辑 `config.py`，或通过环境变量配置：

```bash
export MODEL_TEMPERATURE=0.5
export MAX_ITERATIONS=15
export LOG_LEVEL=DEBUG
```

## 项目结构

```text
context/
├── agent.py              # Agent 核心实现
├── ablation_tests.py     # 消融实验测试套件
├── main.py               # CLI 入口
├── config.py             # 配置管理
├── create_sample_pdf.py  # PDF 生成工具
├── requirements.txt      # 依赖列表
├── env.example           # 环境变量模板
├── README.md             # 英文说明
└── README.zh-CN.md       # 中文说明
```

## 研究用途

本项目适用于：

- **AI 安全研究**：理解 Agent 失败模式
- **系统设计**：识别关键系统组件
- **优化实验**：寻找最小可行配置
- **教学演示**：讲解 Agent 架构原则

## 贡献

欢迎贡献改进，包括：

- 增加更多工具实现
- 设计更复杂的测试场景
- 提供新的上下文消融策略
- 优化性能和稳定性

## 限制

- 当前货币汇率为固定值，生产环境应接入实时汇率 API
- PDF 解析在复杂版式下可能失败
- 模型 token 限制可能影响超大文档处理

## 许可证

MIT License，详情请查看 LICENSE 文件。

## 致谢

- SiliconFlow 提供 Qwen 模型 API
- OpenAI 提供客户端库
- AI Agent 研究社区

## 联系方式

如有问题或反馈，请在 GitHub 提交 issue。

---

**说明**：这是一个用于演示 AI Agent 消融实验的教学项目。生产环境使用时，请补充完善的错误处理、限流和安全机制。
