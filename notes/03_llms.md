# Introduction to LLMs

An **Agent** needs an **AI Model** at its core and **LLMs** are the most common type of AI models for this purpose.

So, what is a **Large Language Model**?  

Imagine you're talking to someone who has read *every book, article and internet post* available up until a certain point in time. This person doesn't have opinions or real understanding like most humans, but this person is ridiculously good at predicting what words *should* come next in a sentence.  

That's a **Large Language Model**, a giant pattern-matching machine that has seen so much text that it can predict what *probably* makes sense next.  

The model has been trained on tons of data, books, wikipedia, blogs and conversations. It doesn't memorize them like a parrot, but instead, it learns the *relationships* between words, sentences and ideas.  

For example, if it sees enough sentences like:  
- "The cat sat on the ___."  
- "I like to eat ___ for breakfast."  

It starts figuring out that `"mat"` or `"floor"` might come after `"sat on the"`, while `"pancakes"` or `"cereal"` might come after `"like to eat"`. This is called **learning probabilities**, it doesn't *know* what a cat is or what pancakes really are, but it knows how these words are typically used and how people typically use them together.  

The problem is, if you just read words one by one, that's not very effective. Imagine reading an entire book, but you only remember *one word at a time*. You'd be clueless about what's happening.  

### Transformer Architecture

The **transformer architecture** is a trick that lets the model look at **many words at once** (sometimes even an entire paragraph). It figures out which words are important by using something called **attention**, it can focus on key parts of a sentence, just like you focus on important details when reading.  

For example, in the sentence:  
- *"The bank by the river flooded last night."*  
- *"I deposited money at the bank."*  

The model **knows** that `"bank"` means different things in each case because of the surrounding words. This is why **transformers** are so powerful, they don't just read words in order, they look at everything **at the same time** and decide which words matter most.  

When people say an **LLM** has millions or billions of **parameters**, think of those as tiny dials that have been adjusted while training. Each parameter represents a tiny **piece of learned knowledge**, about how words relate to each other.  

The more parameters, the better the model is at making accurate predictions. A small model might say:  
- "The sky is **green**." (Bad guess)  

A huge model, with more training, will say:  
- "The sky is **blue**." (Better guess!)  

That's because the huge model has seen the phrase **"the sky is blue"** more often and has enough memory (parameters) to recall that blue is the most likely word.  

Imagine playing a game where someone starts a sentence and you have to complete it:  

1. "The Eiffel Tower is in ___."  
2. "Water freezes at ___ degrees Celsius."  

A human who knows English well would complete these easily. An LLM does the same thing, but instead of intuition, it's just **really, really good at calculating probabilities** from all the text it has seen.  

So, a **Large Language Model** is a text-prediction machine that **learns patterns** from massive amounts of text.

It uses the **transformer architecture** to understand **relationships** between words in different **contexts** (not just one word at a time).

It stores this knowledge in millions or billions of **parameters**, which act like a fine-tuned memory of word relationships.  

When you give it a prompt, it **predicts the next word** based on everything it has learned.  

Even thought it feels like **intelligence**, it's really just math and probabilities at scale!  

The model doesn't "*understand*" language like humans do, but still can generate surprisingly **coherent, useful and even creative** texts. That's because human language itself follows patterns that can be learned!

### Types of Transformers

There are 3 types of transformers:

**1 - Encoders** are like a super readers, takes in a sentence and deeply understands its meaning. Imagine you're reading a book and underlining key ideas to understand its meaning. The encoder does this by turning words into **rich numerical representations** called **embeddings**. 

- Reads the **entire input** at once.  
- Extracts **context** and **meaning** from the text.  
- Useful for **classification**, **embeddings** and **search**.  

**Example Models:**  
- **BERT (Bidirectional Encoder Representations from Transformers)** – Good at understanding text meaning (used in search engines, sentiment analysis).  
- **DistilBERT, RoBERTa** – Variants of BERT with different optimizations.  

**2 - Decoders** are focused on **generating** text, like a storyteller coming up with the next words in a sentence. Imagine you start a story, "*Once upon a time...*" and your friend has to keep adding to it, word by word. That's exactly how a decoder works, it **predicts** the next word based on previous words.  

- Starts with a **partial input** and predicts **the next word, then the next, and so on**.  
- Doesn’t need to "understand" the whole input first—it generates on the fly.  
- Used for **text generation, dialogue, and creative writing** (like GPT).  

**Example Models:**  
- **GPT (Generative Pre-trained Transformer)** – Used in ChatGPT for generating human-like text.  
- **GPT-3, GPT-4, LLaMA, Falcon** – All variations of decoder-only models.  

**3 - Sequence-to-Sequence (Seq2Seq)** is a combination of both, something that **understands** text and **generates** new text based on that understanding. Imagine a translator, when you give them a sentence in English, they don't just translate **word-by-word**. Instead, they read the **whole sentence (encoder)** to understand its meaning, then generate a **new sentence in another language (decoder)** that conveys the same idea.

- Uses **an encoder to read and understand the input**.  
- Then uses **a decoder to generate a new output**.  
- Used for **translation, summarization, and Q&A systems**.  

**Example Models:**  
- **T5 (Text-to-Text Transfer Transformer)** – Can translate, summarize, and answer questions.  
- **Flan-T5, BART, mT5** – Variants that improve different aspects of the original.  

### Tokens

The underlying principle of an LLM is simple yet highly effective. Its objective is to predict the next **token**, given a sequence of previous tokens. A **token** is the unit of information an LLM works with. You can think of a **token** as if it was a **word**, but for efficiency reasons LLMs don't use whole words.

A **tokenizer** chops words into pieces, so a model can process them. The **decoder**  then uses those pieces to generate new text.

A **decoder** produces **token IDs** (numbers corresponding to words or subwords), but it doesn't decide how text is split into those words or subwords (tokens), that's the tokenizer's job!

For example, while English has an estimated 600,000 words, an LLM might have a vocabulary of around 32,000 tokens (as is the case with Llama 2). Tokenization often works on sub-word units that can be combined.

So each LLM has some special tokens specific to the model and the most important of those is the **end of sequence** token (**EOS**).

**SentencePiece** is a special type of tokenizer that doesn't rely on spaces to split words. Instead, it treats text as a raw stream of characters and learns the best way to break it into tokens.

To understand **sentencepiece**, imagine you're trying to teach a computer to understand language. Normally, you'd split sentences into words. But here's the problem:

- What about words the model has never seen before?
- What about languages that don't use spaces, like Chinese or Japanese?
- What about different forms of the same word, like "run" vs. "running"?

Here is where SentencePiece comes to play. Instead of treating words as the smallest unit, it breaks text into subword pieces, little chunks that the model can mix and match to understand any sentence, even with unknown words!

For example, let's take "unhappiness", a simple tokenizer might see `["unhappiness"]` as one unit.
SentencePiece might break it into `["un", "happiness"]` or even `["un", "hap", "pi", "ness"]` depending on the model.

This means the model understands that "un" means "not" and even if the model has never seen "unhappiness" before, it can guess its meaning!

### Next Token prediction

The text representation (tokenization) goes into the model, which outputs scores that rank the likelihood of each token in its vocabulary as being the next one in the sequence.

The easiest decoding strategy would be to always take the **token with the maximum score**.

A more advanced decoding strategy, like **beam search** explores multiple candidate sequences to find the one with the **maximum total score**, even if some individual tokens have lower scores.

If you've interacted with LLMs, you're probably familiar with the term **context length**, which refers to the maximum number of tokens the LLM can process, and the **maximum attention span** it has.