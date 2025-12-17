# 大模型

大模型的本地管理工具

[ollama](https://ollama.com/download)

```bash
curl -fsSL https://ollama.com/install.sh | sh
```

```bash
ollama list

ollama pull gpt-oss:20b
ollama pull llama3.2:latest
ollama pull deepseek:r1
```





给模型更多时间思考

~~~python
text = f"""
In a charming village, siblings Jack and Jill set out on \ 
a quest to fetch water from a hilltop \ 
well. As they climbed, singing joyfully, misfortune \ 
struck—Jack tripped on a stone and tumbled \ 
down the hill, with Jill following suit. \ 
Though slightly battered, the pair returned home to \ 
comforting embraces. Despite the mishap, \ 
their adventurous spirits remained undimmed, and they \ 
continued exploring with delight.
"""

prompt_1 = f"""
Perform the following actions: 
1 - Summarize the following text delimited by triple \
backticks with 1 sentence.
2 - Translate the summary into French.
3 - List each name in the French summary.
4 - Output a json object that contains the following keys: 
french_summary, num_names.

Separate your answers with line breaks.

Text:
```{text}```
"""

from tools.get_completion import Get_Completion
completion = Get_Completion(prompt_1)
print(completion.get_completion())


prompt_2 = f"""
Your task is to perform the following actions: 
1 - Summarize the following text delimited by <> with 1 sentence.
2 - Translate the summary into French.
3 - List each name in the French summary.
4 - Output a json object that contains the 
following keys: french_summary, num_names.

Use the following format:
Text: <text to summarize>
Summary: <summary>
Translation: <summary translation>
Names: <list of names in French summary>
Output JSON: <json with summary and num_names>

Text: <{text}>
"""
completion = Get_Completion(prompt_2)
print(completion.get_completion())

~~~







### 提示词-迭代优化







## 文本概括



