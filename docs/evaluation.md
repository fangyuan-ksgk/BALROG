# 🚀 Evaluation
The following are various options you can use to evaluate your agents on BALROG.

## ⚡️ Evaluate using local vLLM server
We support running LLMs/VLMs out of the box using [vLLM](https://github.com/vllm-project/vllm). You can spin up a vLLM client and evaluate your agent on BALROG in the following way:

```
vllm serve meta-llama/Llama-3.2-1B-Instruct --port 8080

python eval.py \
  agent.type=naive \
  agent.max_image_history=0 \
  agent.max_history=16 \
  eval.num_workers=16 \
  client.client_name=vllm \
  client.model_id=meta-llama/Llama-3.2-1B-Instruct \
  client.base_url=http://0.0.0.0:8080/v1
```

Check out [vLLM](https://github.com/vllm-project/vllm) for more options on how to serve your models fast and efficiently.


## 🛜 Evaluate using API
We support how of the box clients for OpenAI, Anthropic and Google Gemini APIs. If you want to evaluate an agent using one of these APIs, you first have to set up your API key in one of two ways:

You can either directly export it:

```
export OPENAI_API_KEY=<KEY>
export ANTHROPIC_API_KEY=<KEY>
export GEMINI_API_KEY=<KEY>
```

Or you can modify the `SECRETS` file, adding your api keys.

You can then run the evaluation with:

```
python eval.py \
  agent.type=naive \
  agent.max_image_history=0 \
  agent.max_history=16 \
  eval.num_workers=16 \
  client.client_name=openai \
  client.model_id=gpt-4o-mini-2024-07-18
```

## 🖼️ VLM mode

You can activate the VLM mode by increasing the `max_image_history` argument, for example

```
python eval.py \
  agent.type=naive \
  agent.max_history=16 \
  agent.max_image_history=1 \
  eval.num_workers=16 \
  client.client_name=openai \
  client.model_id=gpt-4o-mini-2024-07-18
```

## ▶️ Resume an evaluation
To resume an incomplete evaluation, use eval.resume_from. For example, if an evaluation in the folder results/2024-10-30/16-20-30_naive_gpt-4o-mini-2024-07-18 is unfinished, resume it with:

```
python eval.py \
  agent.type=naive \
  agent.max_image_history=0 \
  agent.max_history=16 \
  eval.num_workers=16 \
  client.client_name=openai \
  client.model_id=gpt-4o-mini-2024-07-18 \
  eval.resume_from=results/2024-10-30_16-20-30_naive_gpt-4o-mini-2024-07-18
```

## ⚙️ Configuring Eval

`eval.py` is configured using Hydra. We list some options below. For more details, refer to the [eval config](https://github.com/DavidePaglieri/BALROG/blob/main/config/config.yaml).

| Parameter                 | Description                                                                                       | Default Value                             |
|---------------------------|---------------------------------------------------------------------------------------------------|-------------------------------------------|
| **agent.type**            | Type of agent used                                 | `naive`                                      |
| **agent.remember_cot**    | Whether the agent should remember chain-of-thought (CoT) during episodes.                         | `True`                                    |
| **agent.max_history**     | Maximum number of dialogue history entries to retain.                                             | `16`                                      |
| **agent.max_image_history**| Maximum number of images included in the history. Use >= 1 if you want to use VLM mode           | `0`                                      |
| **eval.num_workers**      | Number of parallel environment workers for parallel evaluation.                                                        | `1`                                       |
| **eval.num_episodes**     | Number of episodes per environment for evaluation.                                                | `{nle: 5, minihack: 5, babyai: 25, ...}` |
| **eval.save_trajectories**| Whether to save agent trajectories during evaluation.                                             | `True`                                    |
| **eval.save_images**      | Whether to save images of the trajectory  during evaluation.                                      | `False`                                    |
| **client.client_name**    | Type of the client used, e.g. for vLLM servers you would use `vllm`.                              | `openai`                                  |
| **client.model_id**       | Name of the model used.                                                                           | `gpt-4o`                                  |
| **client.base_url**       | Base URL of the model server for API requests with vllm.                                          | `http://localhost:8080/v1`                       |
| **client.is_chat_model**  | Indicates if the model follows a chat-based interface.                                            | `True`                                    |
| **client.generate_kwargs.temperature** | Temperature for model response randomness.                                           | `0.0`                                     |
| **envs.names**            | Dash-separated list of environments to evaluate, e.g., `nle-minihack`.                            | `babyai-babaisai-textworld-crafter-nle-minihack`|