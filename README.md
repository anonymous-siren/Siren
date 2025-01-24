Welcome to the official repository for **Siren**, a project aimed at understanding and mitigating harmful behaviors in large language models (LLMs). 
This repository contains the resources for reproducing the experiments described in our work.

## Repository Structure

### `train`
The `train` directory contains:
- A curated set of data samples.
- Configuration `.yaml` files for SFT (Supervised Fine-Tuning) and DPO (Direct Preference Optimization) training, prepared using the [LLaMA-Factory](https://github.com/hiyouga/LLaMA-Factory).

### `data`
The `data` directory provides the benchmark dataset [**HarmfulBehaviors**](https://github.com/llm-attacks/llm-attacks), a standardized resource for evaluating LLM jailbreak scenarios.


### `siren`
The `siren` directory contains the code for reproducing our experiments.


---

## Usage

To use Siren, follow these steps:

### Step 1: Configure Model Paths
When you have trained SFT and DPO adapters, update the specific paths in the file `utils/generate_Queries_sft_dpo.py`, lines 11 to 30:

```python
self.base_model_dict = {
    "llama3": {
        "decop_1": "path-to-/sft_llama3/decop_1",
        "decop_2": "path-to-/sft_llama3/decop_2",
        "combined": "path-to-/sft_llama3/combined"
    },
    "mistral": {
        "decop_1": "path-to-/sft_mistral/decop_1",
        "decop_2": "path-to-/sft_mistral/decop_2",
        "combined": "path-to-/sft_mistral/combined"
    },
    "qwen": {
        "decop_1": "path-to-/sft_qwen/decop_1",
        "decop_2": "path-to-/sft_qwen/decop_2",
        "combined": "path-to-/sft_qwen/combined"
    }
}

self.adaptor_path_dict = {
    "llama3": {
        "decop_1": "path-to-/LLaMA-Factory/saves/llama3_sft_dpo/train_decop_1",
        "decop_2": "path-to-/LLaMA-Factory/saves/llama3_sft_dpo/train_decop_2",
        "combined": "path-to-/LLaMA-Factory/saves/llama3_sft_dpo/train_combined"
    },
    "mistral": {
        "decop_1": "path-to-/LLaMA-Factory/saves/mistral_sft_dpo/train_decop_1",
        "decop_2": "path-to-/LLaMA-Factory/saves/mistral_sft_dpo/train_decop_2",
        "combined": "path-to-/LLaMA-Factory/saves/mistral_sft_dpo/train_combined"
    },
    "qwen": {
        "decop_1": "path-to-/LLaMA-Factory/saves/qwen_sft_dpo/train_decop_1",
        "decop_2": "path-to-/LLaMA-Factory/saves/qwen_sft_dpo/train_decop_2",
        "combined": "path-to-/LLaMA-Factory/saves/qwen_sft_dpo/train_combined"
    }
}
```

### Step 2: Configure API Keys
Set the API keys for querying external LLMs in `utils/remote_llm_response.py`, lines 10 to 22:

```python
self.gpt_api_keys = os.environ.get("OPENAI_API_KEYS")
self.gpt_api_key_list = self.gpt_api_keys.split(',')
self.gpt_base_url = os.environ.get("OPENAI_BASE_URL")

self.claude_api_keys = os.environ.get("ANTHROPIC_API_KEYS")
self.claude_api_key_list = self.claude_api_keys.split(',')
self.claude_base_url = os.environ.get("ANTHROPIC_BASE_URL")

self.qwen_api_key = os.environ.get("QWEN_API_KEY")
self.qwen_base_url = os.environ.get("QWEN_BASE_URL")

self.gemini_api_keys = os.environ.get("GEMINI_API_KEYS")
self.gemini_api_key_list = self.gemini_api_keys.split(',')
self.gemini_base_url = os.environ.get("GEMINI_BASE_URL")
```

---

## Commands

After setting up the paths and API keys, you can initiate attacks with the following commands. Refer to `commands.sh` or `commands_remote.sh` for more examples.

### Examples:

```bash
CUDA_VISIBLE_DEVICES=0,1 python main_sft_dpo.py --attacker_model llama3 --adaptor decop_1 --victim_model mistral > sft_dpo_llama3_1_mistral.log 2>&1 &
CUDA_VISIBLE_DEVICES=1,2 python main_sft_dpo.py --attacker_model mistral --adaptor combined --victim_model llama3 > sft_dpo_mistral_com_llama3.log 2>&1 &
```

or

```bash
CUDA_VISIBLE_DEVICES=0 python main_sft_dpo.py --attacker_model qwen --adaptor decop_2 --victim_model claude-3-5-haiku-20241022 > sft_dpo_qwen_2_claude.log 2>&1 &
CUDA_VISIBLE_DEVICES=4 python main_sft_dpo.py --attacker_model llama3 --adaptor decop_1 --victim_model gpt-4o-2024-08-06 > sft_dpo_llama3_1_gpt.log 2>&1 &
CUDA_VISIBLE_DEVICES=1 python main_sft_dpo.py --attacker_model mistral --adaptor decop_2 --victim_model gemini-1.5-pro-latest > sft_dpo_mistral_2_ge.log 2>&1 &
