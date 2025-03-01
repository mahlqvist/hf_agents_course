# Fine Tuning

We will fine-tune a GPT-2 model on a small dataset using lightweight methods to fit within the 8GB VRAM on my GeForce RTX 4070. 

We're going to fine-tune a small GPT-2 model on a tiny dataset. Fine-tuning means taking a pre-trained model and teaching it something new by training it on a specific dataset.  

Instead of training **all** the parameters (which is too heavy), we'll use techniques like **LoRA**, **quantization** and **gradient checkpointing** to make it lightweight.

**LoRA** (Low-Rank Adaptation) is a technique that allows us to fine-tune large language models with a small number of parameters. It works by adding and optimizing smaller matrices to the attention weights, typically reducing trainable parameters by about 90%.

First, install the required libraries.  

```bash
pip install torch transformers datasets accelerate bitsandbytes peft
```

- `torch` is the deep learning framework (**PyTorch**)
- `transformers` gives us pre-trained models like **GPT-2**
- `datasets` helps us load and preprocess datasets
- `accelerate` optimizes memory usage (so we don't run out of VRAM)
- `bitsandbytes` enables **4-bit** or **8-bit** quantization
- `peft` provides **LoRA** and other efficient fine-tuning techniques

Now, let's load the model in 8-bit to save memory.

```python
from transformers import AutoModelForCausalLM, AutoTokenizer, BitsAndBytesConfig

model_name = "gpt2"

# Define the quantization config
quantization_config = BitsAndBytesConfig(
    load_in_8bit=True, # Reduces memory usage
)

# Load GPT-2 in 8-bit to save memory
model = AutoModelForCausalLM.from_pretrained(
    model_name,
    quantization_config=quantization_config,
    device_map="auto" # Automatically places it on GPU if available
)

# Load tokenizer
tokenizer = AutoTokenizer.from_pretrained(model_name)
```

A **GPT-2** model doesn't understand words directly, it works with **tokens**, so we need a **tokenizer** to convert text into tokens.

### Preparing a Tiny Dataset

Let's create a simple dataset. We'll use the "*wikitext*" dataset from Hugging Face, which contains Wikipedia-like text.

```python
from datasets import load_dataset

# Load a small dataset for fine-tuning
dataset = load_dataset(
    "wikitext", 
    "wikitext-2-raw-v1", 
    split="train"
)

# Take a small subset (to save memory)
dataset = dataset.shuffle(seed=42).select(range(1000))

# Preview dataset
print(dataset[0])
```

We are we using a small dataset since the full dataset is huge. We select **1000 samples** to keep it manageable for the **GPU**. This helps us learn **faster** without waiting hours for training.

Now, we need to convert our dataset into tokenized text that the model can understand.

GPT-2 does not have a default padding token because it was originally trained without one. However, when we batch-process text (like padding/truncating to the same length), we need a padding token.

Use the EOS token (`<|endoftext|>`) as padding, since GPT-2 already knows `<|endoftext|>`, we can reuse it as pad_token.

Before training, modify your dataset so that `labels` match `input_ids` (this is standard for causal LM fine-tuning).

```python
# Use EOS as PAD
tokenizer.pad_token = tokenizer.eos_token

def tokenize_function(examples):
    tokens = tokenizer(
        examples["text"], 
        padding="max_length", 
        truncation=True, 
        max_length=128
    )
    # Labels should match input for causal LM
    tokens["labels"] = tokens["input_ids"].copy()  
    return tokens

# Apply tokenization
tokenized_dataset = dataset.map(tokenize_function, batched=True)

# Remove original text (we only need tokenized data)
tokenized_dataset = tokenized_dataset.remove_columns(["text"])
```

The reason we truncate at 128 tokens is **GPT-2** has a maximum context length (usually **1024 tokens**), but longer inputs take more memory. **128 tokens** is a good balance between learning and memory usage.

We'll use **LoRA (Low-Rank Adaptation)**, which trains only small adapters instead of the whole model. Memory efficient fine-tuning!

```python
from peft import LoraConfig, get_peft_model

# Configure LoRA
lora_config = LoraConfig(
    r=8,          # Rank of LoRA matrices (smaller = less memory)
    lora_alpha=16, 
    target_modules=["c_attn", "c_proj"],  # Only fine-tune attention layers
    lora_dropout=0.05
)

# Apply LoRA to GPT-2
model = get_peft_model(model, lora_config)

# Print trainable parameters
model.print_trainable_parameters()
```

Instead of updating **all** 124M parameters in GPT-2, we only train **tiny LoRA adapters**. This makes fine-tuning much lighter.

### Set Up the Trainer

Now, we define the training parameters. The `PeftModel` (which is used when applying LoRA) does not expose the labels automatically like a regular Hugging Face model does because it wraps the base model.

To fix this we manually define the label names in TrainingArguments. In most Hugging Face models, the labels for text generation are referred to as "labels" by default. Since `PeftModel` doesn't expose them, we just tell Trainer to expect "labels".

```python
from transformers import TrainingArguments, Trainer

training_args = TrainingArguments(
    output_dir="./results",
    per_device_train_batch_size=1,  # Lower batch size to fit in memory
    gradient_accumulation_steps=4,  # Accumulate gradients to simulate bigger batch
    num_train_epochs=1,             # Just 1 epoch for quick testing
    logging_dir="./logs",
    logging_steps=10,
    save_steps=500,
    save_total_limit=1,
    fp16=True,  # Mixed precision for memory efficiency
    label_names=["labels"] # Explicitly tell Trainer to use "labels"
)

trainer = Trainer(
    model=model,
    args=training_args,
    train_dataset=tokenized_dataset
)
```

We use `gradient_accumulation_steps=4`, instead of doing 4 big batches (which might not fit in memory), we **accumulate** gradients over 4 steps. This tricks the model into thinking it has a bigger batch size.

### Start Fine-Tuning

Let’s start training!

```python
trainer.train()
```

> If this line `tokens["labels"] = tokens["input_ids"].copy()` is missing in the tokenize function you will get a **ValueError**: The model did not return a loss from the inputs, only the following keys: logits,past_key_values. For reference, the inputs it received are input_ids,attention_mask.

You should see the loss decreasing over time!

After training, save the model so we can use it later.

```python
trainer.save_model("./fine_tuned_gpt2_lora")
```

To **load it later**:

```python
from transformers import AutoModelForCausalLM

model = AutoModelForCausalLM.from_pretrained("./fine_tuned_gpt2_lora")
```

> **RuntimeError**: Expected all tensors to be on the same device, but found at least two devices, cpu and cuda:0! (when checking argument for argument index in method wrapper_CUDA__index_select)

The **fine-tuned model** is partially on CPU while some tensors (like input tokens) are on GPU (cuda:0). This happens because when using PEFT (LoRA), only some model layers may be loaded onto GPU, while others stay on CPU by default.

Your input tensors (input_ids, attention_mask) are likely still on CPU, but the model is on GPU.

Hugging Face's Trainer moves the model to GPU automatically during training, but when loading manually, you must move everything explicitly.

```python
import torch
from transformers import AutoModelForCausalLM
from peft import PeftModel

device = "cuda" if torch.cuda.is_available() else "cpu"

# Load base model and fine-tuned weights
base_model = AutoModelForCausalLM.from_pretrained("gpt2")
fine_tuned_model = PeftModel.from_pretrained(base_model, "./fine_tuned_gpt2_lora")

# Move model to GPU ensures the model and inputs are on the same device
fine_tuned_model.to(device)
``` 

### Generate Text with the Fine-Tuned Model

Now, let’s test it by generating text! The fine-tuned GPT-2 should work perfectly on the RTX 4070!

```python
from transformers import AutoTokenizer

tokenizer = AutoTokenizer.from_pretrained("gpt2")
tokenizer.pad_token = tokenizer.eos_token  # Ensure padding token

# Example input
prompt = "The future of AI is"
inputs = tokenizer(prompt, return_tensors="pt", padding=True)

# Move inputs to GPU
inputs = {key: val.to(device) for key, val in inputs.items()}

# Generate text
output = fine_tuned_model.generate(**inputs, max_length=50)

# Decode output
print(tokenizer.decode(output[0], skip_special_tokens=True))
```

Now we just fine-tuned GPT-2 **efficiently** on a local GPU by using:
- **8-bit quantization** to save memory  
- **LoRA** to train fewer parameters  
- **Gradient accumulation** to avoid OOM errors  
- **Small dataset & short sequence length** for quick learning  

This is the **fundamentals** of fine-tuning!

You can now tweak different parts, like:
- Training on a **custom dataset** (your own text files)
- Adjusting **LoRA settings** for different fine-tuning levels
- Experimenting with **4-bit quantization** for even better efficiency