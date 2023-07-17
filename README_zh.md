# JSON 文档嵌入工具

一个用于将文档条目的文本及其矢量嵌入信息合并在 JSON 文件中的在线工具。
[更新日志](CHANGELOG.md) | [英文说明](README.md)

## 介绍

利用”嵌入“（Embedding）和”提示工程“（Prompt Engineering），  
可以在聊天会话期间向像 ChatGPT 这样的大语言模型（LLM）添加临时新知识，其成本较低。

假设我们正在为一个新产品建立问答系统（FAQ），而 ChatGPT 并不熟悉该产品。  
我们已经准备了 300 页的文档，但要把它们都放到提示词中可能会遇到限制或者花费太高。  
要是我们可以只挑选 3 页与用户问题最相关的内容，然后把它们发送给 ChatGPT 来理解呢？

这就是”嵌入“的作用。  
通过”嵌入“，可以一段文本字符串与一个由数字组成数组进行”配对“。  
然后通过计算”余弦相似度“（Cosine Similarity），我们可以找到任意两个数组的相似度得分（从 -1 到 1）。  
得分越高，则表示两个文本的含义越有可能 "接近"。  
通过这种方法，我们可以很容易地从 300 个页面中找到 3 个与用户输入最相关的页面。

这个工具可以帮助那些正在准备文档的人通过 Azure/OpenAI API 来获得嵌入数据。  
然后导出为静态的 JSON 文件，这样就可以在每个聊天会话中重复使用。  
它还可以将用户的输入与文档进行比较，并计算相似度得分。

JSON 示例：

```jsonc
[{
  "title": "How to Use Embedding",              // 标题，可选，文档项目的标题
  "text": "Embedding and prompt...",            // 文本，必填，需要嵌入的主要文本
  "embedding": [-0.013891131,-0.0090339715...], // 嵌入，导入时为可选, 矢量嵌入的数字数组
  "lock": false,                                // 锁定，导入时为可选，文本和嵌入是否已配对
  "extra": false,                               // 更多，导入时为可选，显示以下额外字段
  "link": "https://github.com/horans/",         // 链接，可选
  "image": "https://oai.azure.com/favicon.png", // 图片，可选
  "video": "",                                  // 视频，可选
  "version": "",                                // 版本，可选
  "function": ""                                // 功能，可选
}]
```

## 特性

### 添加

通过点击”添加“（Add）按钮，您可以在当前文档列表中添加一个新的条目。

### 删除

通过点击”删除“（Delete）按钮，您可以从文档列表中删除当前条目。

### 编辑

您可以输入文档条目中除”嵌入“（Embedding）以外的所有字段。  
完成”文本“（Text）字段的编辑后，点击右侧的”配对“（Pair）按钮。  
它将请求 Azure/OpenAI 的嵌入 API 并回复结果。  
然后它将锁定您的”文本“（Text）字段，这意味着现在文本和嵌入已”配对“（Paired）。  
您必须先解除锁定，才能再次”编辑“（Edit）文本。

您不必逐项配对，当您“导出”时，所有条目将自动配对。

### 导入

通过点击“导入”（Import）按钮，您可以从一个 JSON 文件导入文档列表。  
需要导入的 JSON 文件必须遵循介绍章节的“JSON 示例”的格式。  
一个最基本的 JSON 可以是：

```json
[
  { "text": "some words" },
  { "text": "other words" }
]
```

### 导出

通过点击“导出”（Export）按钮，您可以将当前的文档列表导出为 JSON 文件。  
如果有任何条目没有“文本”（Text），这些条目将被跳过。  
如果有任何条目带有“文本”（Text）但没有“嵌入”（Embedding），这些条目将自动配对。

### 重置

通过点击“重置”（Reset）按钮，您可以清除当前文档列表中的所有条目。

### 配置

在执行嵌入（Embedding）配对之前，您需要准备好以下内容：

- 终结点（Endpoint）：`https://{your-resource-name}.openai.azure.com/openai/deployments/{deployment-id}/embeddings?api-version={api-version}`
- 接口密钥（API Key）：`{your-api-key}`
- 请求路径（Request Path）：默认为`input`
- 响应路径（Response Path）：默认为`data[0].embedding`

通过点击“配置”（Config）按钮，您可以调整这些参数。  
如果您没有使用 Azure/OpenAI 的嵌入 API，您可能需要调整代码中的`fetch()`参数。  
详细参考：<https://learn.microsoft.com/en-us/azure/cognitive-services/openai/reference#embeddings>  

### 比较

通过点击“比较”（Compare）按钮，您可以暂时输入一些文本并与列表中的文本进行比较。  
点击“检查”（Check）按钮后，将计算输入嵌入和列表条目嵌入之间的“余弦相似度”。  
计算完成后，相似度得分将显示在每个条目上。  
您可以调整阈值（Threshold），以突出显示高于阈值的条目。（默认为 0.7）  
该功能将帮助您测试您的预设文档是否能被用户的输入“选中”（Picked）。

在以下情况下，相似度得分将被清除：

- 添加/删除/导入文档条目
- 将任何文本与嵌入进行配对

### 记忆

文档列表和 API 配置将存储在浏览器的`localStorage`中。  
您可以随时回来继续您的工作。

## 依赖

- [Vue](https://github.com/vuejs/core)
- [Bootstrap](https://github.com/twbs/bootstrap)
- [You don't (may not) need Lodash/Underscore](https://github.com/you-dont-need/You-Dont-Need-Lodash-Underscore)
- [ChatGPT](https://chat.openai.com/chat)
