<p align="center">
  <a href="https://github.com/mem0ai/mem0">
    <img src="docs/images/banner-sm.png" width="800px" alt="Mem0 - The Memory Layer for Personalized AI">
  </a>
</p>
<p align="center" style="display: flex; justify-content: center; gap: 20px; align-items: center;">
  <a href="https://trendshift.io/repositories/11194" target="blank">
    <img src="https://trendshift.io/api/badge/repositories/11194" alt="mem0ai%2Fmem0 | Trendshift" width="250" height="55"/>
  </a>
</p>

<p align="center">
  <a href="https://mem0.ai">官网</a>
  ·
  <a href="https://mem0.dev/DiG">加入 Discord</a>
  ·
  <a href="https://mem0.dev/demo">在线演示</a>
  ·
  <a href="https://mem0.dev/openmemory">OpenMemory</a>
</p>

<p align="center">
  <a href="https://mem0.dev/DiG">
    <img src="https://img.shields.io/badge/Discord-%235865F2.svg?&logo=discord&logoColor=white" alt="Mem0 Discord">
  </a>
  <a href="https://pepy.tech/project/mem0ai">
    <img src="https://img.shields.io/pypi/dm/mem0ai" alt="Mem0 PyPI - Downloads">
  </a>
  <a href="https://github.com/mem0ai/mem0">
    <img src="https://img.shields.io/github/commit-activity/m/mem0ai/mem0?style=flat-square" alt="GitHub commit activity">
  </a>
  <a href="https://pypi.org/project/mem0ai" target="blank">
    <img src="https://img.shields.io/pypi/v/mem0ai?color=%2334D058&label=pypi%20package" alt="Package version">
  </a>
  <a href="https://www.npmjs.com/package/mem0ai" target="blank">
    <img src="https://img.shields.io/npm/v/mem0ai" alt="Npm package">
  </a>
  <a href="https://www.ycombinator.com/companies/mem0">
    <img src="https://img.shields.io/badge/Y%20Combinator-S24-orange?style=flat-square" alt="Y Combinator S24">
  </a>
</p>

<p align="center">
  <a href="https://mem0.ai/research"><strong>📄 论文：面向生产环境的 AI Agent 可扩展长期记忆 →</strong></a>
</p>
<p align="center">
  <strong>⚡ 准确率比 OpenAI Memory 高 +26% · 🚀 响应速度提升 91% · 💰 Token 用量减少 90%</strong>
</p>

> **🎉 mem0ai v1.0.0 已正式发布！** 本次大版本包含 API 现代化改造、向量存储增强以及 GCP 集成优化。[查看迁移指南 →](MIGRATION_GUIDE_v1.0.md)

---

## 目录

1. [项目简介](#项目简介)
2. [核心性能指标](#核心性能指标)
3. [整体架构](#整体架构)
4. [Memory 机制深度解析](#memory-机制深度解析)
   - [三层存储体系](#一三层存储体系)
   - [Memory 类型](#二memory-类型)
   - [add() 写入流程](#三add-写入流程)
   - [search() 检索流程](#四search-检索流程)
   - [LLM Prompt 设计](#五llm-prompt-设计)
   - [Graph Memory 知识图谱](#六graph-memory-知识图谱)
5. [项目目录结构](#项目目录结构)
6. [快速开始](#快速开始)
7. [配置说明](#配置说明)
8. [集成生态](#集成生态)
9. [引用](#引用)
10. [许可证](#许可证)

---

## 项目简介

[Mem0](https://mem0.ai)（读作 "mem-zero"）是一个为 AI 助手与 Agent 提供**智能持久化记忆层（Intelligent Memory Layer）**的开源框架。它通过在对话中自动提取、存储、更新和检索关键信息，让 AI 具备真正意义上的**长期记忆（Long-Term Memory）**能力——能够记住用户偏好、适应个人需求、在交互中持续学习。

**典型应用场景：**
- **AI 助理**：跨会话保留上下文，提供一致且个性化的对话体验
- **客服机器人**：自动关联用户历史工单与偏好，提供精准服务
- **医疗健康**：追踪患者饮食限制、用药偏好等个人健康档案
- **游戏 / 生产力工具**：根据用户行为自适应调整工作流与游戏环境
- **自主 Agent**：为多步任务 Agent 提供跨步骤的状态与计划记忆（**Procedural Memory**）

---

## 核心性能指标

基于 [LOCOMO](https://mem0.ai/research) 基准测试：

| 指标 | Mem0 vs OpenAI Memory | Mem0 vs 全上下文（Full-Context） |
|------|----------------------|--------------------------------|
| 准确率（Accuracy） | **+26%** | 相当 |
| 响应延迟（Latency） | — | **快 91%** |
| Token 消耗（Token Usage） | — | **少 90%** |

---

## 整体架构

```
用户对话 (Conversation)
        │
        ▼
┌───────────────────────────────────────────────┐
│                  Memory 类 (mem0/memory/main.py)                    │
│                                               │
│  ┌─────────────┐   ┌──────────────────────┐  │
│  │   LLM 层    │   │   Embedding 层        │  │
│  │  (事实提取)  │   │  (向量化 / 语义搜索)  │  │
│  └─────────────┘   └──────────────────────┘  │
│                                               │
│  ┌─────────────────────────────────────────┐  │
│  │             存储层 (Storage Layer)       │  │
│  │  ┌───────────┐ ┌──────────┐ ┌────────┐  │  │
│  │  │Vector Store│ │Graph Store│ │SQLite  │  │  │
│  │  │(语义向量)  │ │(知识图谱) │ │(历史)  │  │  │
│  │  └───────────┘ └──────────┘ └────────┘  │  │
│  └─────────────────────────────────────────┘  │
└───────────────────────────────────────────────┘
        │
        ▼
  个性化 AI 应用 (Personalized AI Application)
```

---

## Memory 机制深度解析

### 一、三层存储体系

Mem0 采用三层互补的存储架构，各司其职：

#### 1. Vector Store（向量数据库）—— 语义长期记忆

**核心文件：** `mem0/vector_stores/` 目录，支持 20+ 种向量数据库。

默认使用本地 [Qdrant](https://qdrant.tech/)，同时支持：

| 类型 | 支持的数据库 |
|------|------------|
| 云托管 | Pinecone, Weaviate, Supabase, MongoDB Atlas, Elasticsearch, OpenSearch |
| 本地部署 | Qdrant (本地), FAISS, Chroma, Redis, Milvus |
| 企业级 | Azure AI Search, Databricks, Vertex AI, Neptune Analytics |

所有记忆（**Memory**）以 `(向量, payload)` 对的形式存储，`payload` 包含：
```json
{
  "data": "用户喜欢意大利菜",
  "hash": "sha256_of_data",
  "user_id": "alice",
  "created_at": "2026-02-26T10:00:00",
  "updated_at": "2026-02-26T10:00:00"
}
```

#### 2. Graph Store（图数据库）—— 关系知识图谱

**核心文件：** `mem0/memory/graph_memory.py`、`mem0/graphs/`

可选启用，基于 [Neo4j](https://neo4j.com/) 构建实体关系三元组（**Entity-Relation Triples**）：

```
(用户 Alice) --[喜欢]--> (意大利菜)
(用户 Alice) --[住在]--> (上海)
(用户 Alice) --[工作]--> (软件工程师)
```

通过 LLM 自动从对话中抽取实体（**Entity**）和关系（**Relation**），支持复杂语义查询。

#### 3. SQLite History DB —— 操作历史追踪

**核心文件：** `mem0/memory/storage.py`

记录每条记忆的完整变更历史（ADD / UPDATE / DELETE），支持通过 `memory.history(memory_id)` 回溯。

```
memory_id | old_memory | new_memory | event   | timestamp
----------|------------|------------|---------|----------
uuid-001  | None       | "爱好跑步" | ADD     | 2026-01-01
uuid-001  | "爱好跑步" | "爱好马拉松"| UPDATE  | 2026-02-01
```

---

### 二、Memory 类型

**核心文件：** `mem0/configs/enums.py`、`mem0/memory/main.py`

Mem0 支持三种内存类型（**Memory Types**）：

| 类型 | 英文名 | 触发条件 | 用途 |
|------|--------|---------|------|
| 语义记忆 | **Semantic Memory** | 默认，用户对话 | 存储事实性偏好与信息 |
| 情节记忆 | **Episodic Memory** | Agent + 用户对话 | 记录对话事件与交互历史 |
| 过程记忆 | **Procedural Memory** | `memory_type="procedural_memory"` | Agent 的任务执行步骤与计划摘要 |

```python
# 语义/情节记忆（默认）
memory.add(messages, user_id="alice")

# 过程记忆（Procedural Memory）—— 适用于 Agent 任务追踪
memory.add(agent_steps, agent_id="my_agent", memory_type="procedural_memory")
```

---

### 三、`add()` 写入流程

**核心文件：** `mem0/memory/main.py` → `Memory.add()` → `Memory._add_to_vector_store()`

整个写入流程分为 **5 个步骤**，核心是利用 LLM 实现智能的增量式记忆管理：

```
输入消息 (messages)
      │
      ▼ 步骤 1：消息预处理
  parse_messages()  ← 将 role/content 列表拼接为纯文本
      │
      ▼ 步骤 2：事实提取 (Fact Extraction)
  LLM.generate_response()
  Prompt: USER_MEMORY_EXTRACTION_PROMPT / AGENT_MEMORY_EXTRACTION_PROMPT
  输出: {"facts": ["用户叫 Alice", "喜欢跑步", "住在上海"]}
      │
      ▼ 步骤 3：向量相似度检索 (Similarity Search)
  for each fact:
      embedding = EmbeddingModel.embed(fact)       ← 将每条事实向量化
      old_memories = VectorStore.search(embedding) ← 检索相似的旧记忆（Top-5）
      │
      ▼ 步骤 4：记忆决策 (Memory Decision via LLM)
  LLM.generate_response()
  Prompt: DEFAULT_UPDATE_MEMORY_PROMPT
  输入: [旧记忆列表] + [新事实列表]
  输出: {"memory": [
      {"id": "0", "text": "喜欢马拉松", "event": "UPDATE", "old_memory": "喜欢跑步"},
      {"id": "new", "text": "住在上海",   "event": "ADD"},
      {"id": "2",   "text": "...",        "event": "NONE"}
  ]}
      │
      ▼ 步骤 5：执行写入 (Execute Operations)
  ADD    → VectorStore.insert() + SQLiteManager.add_history()
  UPDATE → VectorStore.update() + SQLiteManager.add_history()
  DELETE → VectorStore.delete() + SQLiteManager.add_history()
  NONE   → 无操作（或仅更新 session ID）
```

**关键设计：并发执行 Vector Store 写入与 Graph Store 写入**

```python
# mem0/memory/main.py
with concurrent.futures.ThreadPoolExecutor() as executor:
    future1 = executor.submit(self._add_to_vector_store, messages, metadata, filters, infer)
    future2 = executor.submit(self._add_to_graph, messages, filters)
    concurrent.futures.wait([future1, future2])
```

---

### 四、`search()` 检索流程

**核心文件：** `mem0/memory/main.py` → `Memory.search()`

```
查询语句 (query string)
      │
      ▼ 向量化
  embedding = EmbeddingModel.embed(query, "search")
      │
      ▼ 向量相似度检索
  results = VectorStore.search(
      query=query,
      vectors=embedding,
      limit=limit,
      filters={"user_id": "alice"}   ← 按会话 ID 隔离不同用户的记忆
  )
      │
      ▼ 可选：Reranker 精排（需配置）
  if self.reranker:
      results = Reranker.rerank(query, results)
      │
      ▼ 可选：相似度阈值过滤（threshold）
  results = [r for r in results if r.score >= threshold]
      │
      ▼ 并发检索 Graph Store（如果启用）
  graph_results = GraphStore.search(query, filters)
      │
      ▼ 返回
  {"results": [...memories...], "relations": [...graph triples...]}
```

**会话隔离（Session Isolation）机制：**

Mem0 通过三种**会话 ID（Session ID）**实现多用户、多 Agent、多运行的记忆隔离：

| 参数 | 含义 | 典型用途 |
|------|------|---------|
| `user_id` | 用户标识符 | 个人记忆，跨会话持久 |
| `agent_id` | Agent 标识符 | Agent 的专属行为记忆 |
| `run_id` | 运行标识符 | 单次任务的临时上下文 |

三者可组合使用，至少提供一个。

---

### 五、LLM Prompt 设计

**核心文件：** `mem0/configs/prompts.py`

Mem0 的智能性主要来自两个关键 Prompt：

#### Prompt 1：事实提取（Fact Extraction）

有两个版本，根据场景自动切换：

- **`USER_MEMORY_EXTRACTION_PROMPT`**：从用户消息中提取事实，忽略 assistant 消息
- **`AGENT_MEMORY_EXTRACTION_PROMPT`**：从 assistant 消息中提取 Agent 的行为特征，忽略用户消息

切换逻辑（`_should_use_agent_memory_extraction()`）：
```python
# 当同时满足以下两个条件时，使用 Agent 提取模式：
# 1. 传入了 agent_id
# 2. messages 中包含 assistant 角色的消息
return has_agent_id and has_assistant_messages
```

**Prompt 输出格式（JSON）：**
```json
{"facts": ["名字是 Alice", "喜欢意大利菜", "住在上海"]}
```

#### Prompt 2：记忆决策（Memory Decision）

**`DEFAULT_UPDATE_MEMORY_PROMPT`** 负责对比新旧记忆，决定增删改：

```
旧记忆：
  [{"id": "0", "text": "喜欢奶酪披萨"}]

新事实：
  ["喜欢鸡肉披萨"]

决策输出：
  {"memory": [
    {"id": "0", "text": "喜欢奶酪和鸡肉披萨", "event": "UPDATE", "old_memory": "喜欢奶酪披萨"}
  ]}
```

支持四种操作（**Operations**）：

| 事件（Event） | 触发条件 | 说明 |
|--------------|---------|------|
| `ADD` | 新事实在旧记忆中不存在 | 插入新记忆 |
| `UPDATE` | 新事实与旧记忆内容不同但相关 | 合并更新，保留最多信息 |
| `DELETE` | 新事实与旧记忆相矛盾 | 删除过时记忆 |
| `NONE` | 信息已存在或不相关 | 不做修改 |

> **注意：** 为防止 LLM 产生 UUID 幻觉（UUID Hallucination），系统在调用 LLM 前将真实 UUID 临时映射为整数索引（`temp_uuid_mapping`），LLM 返回后再还原。

---

### 六、Graph Memory 知识图谱

**核心文件：** `mem0/memory/graph_memory.py`、`mem0/graphs/`

当启用 `graph_store` 配置时，Graph Memory 并发执行，通过 LLM 抽取实体与关系：

```
输入文本
    │
    ▼ 实体识别 (Entity Extraction via LLM)
    {"entities": ["Alice", "上海", "软件工程师"]}
    │
    ▼ 关系建立 (Relation Establishment via LLM)
    {"relations": [
      {"source": "Alice", "relationship": "住在", "destination": "上海"},
      {"source": "Alice", "relationship": "职业", "destination": "软件工程师"}
    ]}
    │
    ▼ 冲突检测与删除 (Conflict Detection)
    搜索图数据库中已有节点，对矛盾关系执行 LLM 决策后删除
    │
    ▼ 写入 Neo4j（Cypher 查询）
    MERGE (n:__Entity__ {name: "Alice", user_id: "alice"})
    MERGE (m:__Entity__ {name: "上海", user_id: "alice"})
    MERGE (n)-[:住在]->(m)
```

---

## 项目目录结构

```
mem0/
├── __init__.py                  # 导出 Memory, AsyncMemory, MemoryClient
├── exceptions.py                # 自定义异常类（ValidationError, LLMError 等）
│
├── memory/                      # 核心 Memory 实现
│   ├── main.py                  # ★ Memory 类主文件（add/search/get/update/delete）
│   ├── base.py                  # ABC 抽象基类（定义接口规范）
│   ├── graph_memory.py          # Graph Memory（基于 Neo4j 的知识图谱记忆）
│   ├── storage.py               # SQLiteManager（历史变更追踪）
│   ├── utils.py                 # 工具函数（消息解析、Prompt 构建、JSON 提取）
│   ├── setup.py                 # 初始化配置目录
│   └── telemetry.py             # 使用遥测（匿名统计）
│
├── configs/
│   ├── base.py                  # MemoryConfig、MemoryItem Pydantic 模型
│   ├── prompts.py               # ★ 核心 Prompt（事实提取、记忆决策、过程记忆）
│   └── enums.py                 # MemoryType 枚举
│
├── llms/                        # LLM 适配层（Provider Adapters）
│   ├── openai.py                # OpenAI / Azure OpenAI
│   ├── anthropic.py             # Anthropic Claude
│   ├── google.py                # Google Gemini
│   ├── ollama.py                # 本地 Ollama
│   └── ...                      # 20+ LLM 支持
│
├── embeddings/                  # Embedding 模型适配层
│   ├── openai.py                # text-embedding-ada-002 等
│   ├── huggingface.py           # 本地 HuggingFace 模型
│   └── ...                      # 10+ Embedding 支持
│
├── vector_stores/               # Vector Store 适配层
│   ├── qdrant.py                # 默认：本地 Qdrant
│   ├── faiss.py                 # Facebook FAISS（纯本地）
│   ├── pinecone.py              # Pinecone（云端）
│   └── ...                      # 20+ Vector Store 支持
│
├── graphs/                      # Graph Store 配置与工具
│   ├── configs.py               # GraphStoreConfig
│   ├── tools.py                 # LLM Function Calling 工具定义
│   └── utils.py                 # Cypher 查询构建、关系提取 Prompt
│
├── reranker/                    # Reranker 精排层（可选）
│   ├── base.py
│   └── ...
│
├── client/                      # Mem0 云平台 API Client
│   └── main.py                  # MemoryClient（调用 Mem0 托管服务）
│
└── utils/
    └── factory.py               # ★ 工厂类（LlmFactory, EmbedderFactory, VectorStoreFactory 等）

mem0-ts/                         # TypeScript SDK（与 Python SDK 功能对等）
openmemory/                      # OpenMemory —— 自托管记忆管理 UI
server/                          # FastAPI REST API 服务端
embedchain/                      # EmbedChain（RAG 框架，已并入 Mem0 生态）
evaluation/                      # 性能评测脚本（LOCOMO 基准）
examples/                        # 示例代码（多 Agent、多模态、Chrome 扩展等）
```

---

## 快速开始

### 方式一：托管平台（Hosted Platform）

1. 注册 [Mem0 Platform](https://app.mem0.ai)
2. 获取 API Key，通过 SDK 或 REST API 直接调用

### 方式二：自托管（Self-Hosted，开源版）

**Python 安装：**
```bash
pip install mem0ai
```

**Node.js / TypeScript 安装：**
```bash
npm install mem0ai
```

### 基础使用示例

Mem0 默认使用 OpenAI `gpt-4.1-nano-2025-04-14` 作为 LLM，需要设置 `OPENAI_API_KEY` 环境变量。

```python
from openai import OpenAI
from mem0 import Memory

openai_client = OpenAI()
memory = Memory()

def chat_with_memories(message: str, user_id: str = "default_user") -> str:
    # 1. 检索与当前问题相关的历史记忆（Semantic Search）
    relevant_memories = memory.search(query=message, user_id=user_id, limit=3)
    memories_str = "\n".join(f"- {entry['memory']}" for entry in relevant_memories["results"])

    # 2. 将记忆注入 System Prompt，生成个性化回答
    system_prompt = f"你是一个有帮助的 AI 助理，请基于用户记忆回答问题。\n用户记忆：\n{memories_str}"
    messages = [{"role": "system", "content": system_prompt}, {"role": "user", "content": message}]
    response = openai_client.chat.completions.create(model="gpt-4.1-nano-2025-04-14", messages=messages)
    assistant_response = response.choices[0].message.content

    # 3. 将本次对话自动写入记忆（Automatic Memory Extraction & Update）
    messages.append({"role": "assistant", "content": assistant_response})
    memory.add(messages, user_id=user_id)

    return assistant_response

def main():
    print("与 AI 对话（输入 'exit' 退出）")
    while True:
        user_input = input("你：").strip()
        if user_input.lower() == 'exit':
            print("再见！")
            break
        print(f"AI：{chat_with_memories(user_input)}")

if __name__ == "__main__":
    main()
```

### 记忆管理 API

```python
from mem0 import Memory

m = Memory()

# 添加记忆
result = m.add("我叫 Alice，喜欢跑步，住在上海", user_id="alice")

# 语义检索
results = m.search("Alice 的爱好是什么？", user_id="alice")

# 获取所有记忆
all_memories = m.get_all(user_id="alice")

# 获取单条记忆
single = m.get(memory_id="<uuid>")

# 更新记忆
m.update(memory_id="<uuid>", data="我叫 Alice，喜欢马拉松")

# 删除记忆
m.delete(memory_id="<uuid>")

# 查看记忆变更历史（History）
history = m.history(memory_id="<uuid>")

# 删除某用户所有记忆
m.delete_all(user_id="alice")

# 重置所有记忆
m.reset()
```

---

## 配置说明

Mem0 通过 `MemoryConfig`（Pydantic 模型）进行配置，核心组件均可自由替换：

```python
from mem0 import Memory

config = {
    # LLM 配置（支持 OpenAI, Anthropic, Google, Ollama 等 20+ 种）
    "llm": {
        "provider": "openai",
        "config": {
            "model": "gpt-4o-mini",
            "temperature": 0.1,
            "max_tokens": 2000
        }
    },
    # Embedding 配置
    "embedder": {
        "provider": "openai",
        "config": {"model": "text-embedding-3-small"}
    },
    # Vector Store 配置（默认本地 Qdrant）
    "vector_store": {
        "provider": "qdrant",
        "config": {
            "collection_name": "my_memories",
            "embedding_model_dims": 1536
        }
    },
    # Graph Store 配置（可选，启用知识图谱）
    "graph_store": {
        "provider": "neo4j",
        "config": {
            "url": "bolt://localhost:7687",
            "username": "neo4j",
            "password": "password"
        }
    },
    # Reranker 配置（可选，提升检索精度）
    "reranker": {
        "provider": "cohere",
        "config": {"api_key": "your-cohere-key", "model": "rerank-english-v3.0"}
    },
    # 自定义 Prompt（可选）
    "custom_fact_extraction_prompt": None,  # 自定义事实提取 Prompt
    "custom_update_memory_prompt": None     # 自定义记忆决策 Prompt
}

m = Memory.from_config(config)
```

---

## 集成生态

| 集成目标 | 说明 | 链接 |
|---------|------|------|
| **ChatGPT with Memory** | 记忆增强的 ChatGPT 体验 | [在线演示](https://mem0.dev/demo) |
| **浏览器扩展** | 跨 ChatGPT / Perplexity / Claude 存储记忆 | [Chrome 扩展](https://chromewebstore.google.com/detail/onihkkbipkfeijkadecaafbgagkhglop) |
| **LangGraph** | 与 LangGraph 构建客服机器人 | [示例](https://docs.mem0.ai/integrations/langgraph) |
| **CrewAI** | 为 CrewAI Agent 提供记忆能力 | [示例](https://docs.mem0.ai/integrations/crewai) |
| **AutoGen** | 基于 AutoGen 的可教学 Agent | [Cookbook](cookbooks/mem0-autogen.ipynb) |
| **OpenMemory** | 自托管记忆管理 UI（本地数据主权）| [OpenMemory](openmemory/) |

**完整文档：** https://docs.mem0.ai  
**社区支持：** [Discord](https://mem0.dev/DiG) · [Twitter/X](https://x.com/mem0ai)  
**联系我们：** founders@mem0.ai

---

## 引用

如果您在研究中使用了 Mem0，请引用我们的论文：

```bibtex
@article{mem0,
  title={Mem0: Building Production-Ready AI Agents with Scalable Long-Term Memory},
  author={Chhikara, Prateek and Khant, Dev and Aryan, Saket and Singh, Taranjeet and Yadav, Deshraj},
  journal={arXiv preprint arXiv:2504.19413},
  year={2025}
}
```

---

## 许可证

Apache 2.0 — 详见 [LICENSE](https://github.com/mem0ai/mem0/blob/main/LICENSE) 文件。