# Custom Data GPT

[![label](https://img.shields.io/badge/%E4%B8%AD%E6%96%87%E6%96%87%E6%A1%A3-ZH-brightgreen)](https://github.com/aboutmydreams/custom_data_gpt/blob/main/README_ZH.md)
[![label](https://img.shields.io/badge/English-EN-brightgreen)](https://github.com/aboutmydreams/custom_data_gpt/blob/main/README.md)
[![label](https://img.shields.io/badge/aiis.life-AI%20is%20life-orange)](https://www.aiis.life)
[![label](https://img.shields.io/badge/NonceGeek-cool--oriented%20programming-orange)](https://noncegeek.com)

本项目是一个基于 OpenAI，适用于企业私有化数据微调的聊天机器人，能够回答用户提出的各种和企业产品相关的问题。使用 OpenAI 的 API 进行语言模型的训练和生成，使用 Faiss 进行文本搜索和相似度匹配，支持从本地文件或 URL 获取训练数据。本项目为 [Custom-Company-Chatbot](https://replit.com/@DavidAtReplit/Custom-Company-Chatbot) 的增强版，受原项目启发，扩展了从网络以及本地获取 train 的私有数据 (注意，原理上讲，这不是真正训练，而是找到相关的上下文后再告诉 GPT)。感谢 [@leeduckgo](https://github.com/leeduckgo) 的激励支持。

## 使用步骤

1. 在 .env 文件中添加 `API_SECRET=MySecret` 和 `OPENAI_API_KEY=<your-key>`，其中 `<your-key>` 为你的 OpenAI API key。
2. 使用 poetry 安装项目所需的依赖，
    - `pip install poetry`
    - `poetry install`
    - `poetry shell`
3. 启动虚拟环境，运行 `python3 main.py` 选择 1，将生成的模型文件修改为 zym.index zym.pkl 移动到 ./training/models 。
4. 运行 `python3 main_api.py` 运行后端，新建命令行执行:
    - `cd ./test`
    - `python3 chat_zym_api.test.py` 查看回答， 如果全程没有报错，正常情况你会看到 `test` 文件夹下多了一个 `text.txt` 保存了指定问题和回答内容

## API 接口文档

### /train

该接口用于将数据在本地储存为向量空间

#### 请求

- 方法: POST
- 请求体: JSON 格式
- 请求体参数:
  - file_list: 要训练的文件列表
  - space_name: 向量空间名称
  - file_tag: url(网络 txt 数据) or path(本地数据)

```json
{
    "file_list": ["https://cdn.vitae3.me/public-static/213113032213132120.1680148867987.txt"],
    "space_name": "train_space_name",
    "file_tag": "url",
}
```

#### 响应

- 状态码: 200
- 响应体: JSON 格式
- 响应体参数:
  - code: 状态信息 0 正常，1 异常
  - data: 数据
  - msg: success

```json
{
  "code": 0,
  "data": {
    "index_space_size": 6.0439,
    "space_name": "train_space_name",
    "pkl_space_size": 2.4023,
    "size_type": "kb"
  },
  "msg": "success"
}
```

### /chat

该接口用于进行聊天。

#### chat 请求

- 方法: POST
- 请求体: JSON 格式
- 请求体参数:
  - space_name: 本地数据储存的向量空间名称
  - question: 问题
  - history: 历史记录
  - model_name: 模型名称, eg: text-davinci-003, gpt-3.5-turbo, gpt-3.5-turbo-0301, 你可以在 [模型列表](https://platform.openai.com/docs/models) 中尝试更多语言生成模型，以及在 [价格列表](https://openai.com/pricing) 中查询不同模型的价格。

```json
{
  "history": ["human: how are u", "Bot: fine"],
  "question": "How to build a GPT3 Chatbot for your Company",
  "space_name": "train_space_name",
  "model_name": "text-davinci-003",
}
```

#### chat 响应

- 状态码: 200
- 响应体: JSON 格式
- 响应体参数:
  - code: 状态信息 0 正常，1 异常
  - data: 数据
  - msg: success

```json
{
    "code": 0,
    "msg": "success",
    "data": {
        "answer": " Building a GPT3 chatbot for your company requires a few steps. First, you need to get your OpenAI API key and add it to Secrets as OPENAI_API_KEY. Next, you need to create an API_KEY for the JSON API. After that, you need to fill the training/facts folder with as many text documents as you can containing information about the company you're training it on. Finally, you need to edit the prompt/master.txt file to represent how you want the bot to behave when interacting with the users.",
        "history": [
            "human: how are u",
            "Bot: fine",
            "Human: How to build a GPT3 Chatbot for your Company",
            "Bot:  Building a GPT3 chatbot for your company requires a few steps. First, you need to get your OpenAI API key and add it to Secrets as OPENAI_API_KEY. Next, you need to create an API_KEY for the JSON API. After that, you need to fill the training/facts folder with as many text documents as you can containing information about the company you're training it on. Finally, you need to edit the prompt/master.txt file to represent how you want the bot to behave when interacting with the users."
        ]
    }
}
```

## 注意事项

- 本项目由 python3.10 构建，仅供学习和研究使用，请勿用于破坏社会环境的用途。
- 若文本数量较大，需要较长时间的训练时间和大量的计算资源。
- 使用前请仔细阅读文档和代码，并确保已经正确配置 API key 和训练数据。
