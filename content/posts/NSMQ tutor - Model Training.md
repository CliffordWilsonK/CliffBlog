+++
author = "Clifford Wilson"
title = "NSMQ tutor model"
date = "2025-12-03"
description = "Fine-tuning an AI model to help students become NSMQ pros"
tags = [
    "AI",
    "model",
]
+++

# 🧠 Building a Brain from Scratch: How I Engineered an AI Coach for NSMQ 🇬🇭

Everyone wants to build "AI" these days. Usually, that means wrapping a prompt around the ChatGPT API and calling it a startup.

I didn’t want to do that. I wanted to build a model that actually *knew* something specific—specifically, the **National Science and Maths Quiz (NSMQ)**.

### 🎯 The Mission: It's Not About the Answer, It's About the Trick

Back in the day, I was just like every other science student in Ghana: I entered high school dreaming of the NSMQ stage. But here’s the cold truth: knowing the textbook isn't enough.

The "big schools" dominate not because they are smarter, but because they know the **heuristics**. They know the shortcuts. They know that you don't actually solve the full quadratic equation—you just look at the sum of roots.

**That is the gap I wanted to close.**

Students in deprived schools have the raw talent, but they lack the **"Game Theory"** of the quiz. I built this AI to democratize those specific tricks. The goal was simple: **give every student the cheat codes to the game, regardless of their zip code.**

But you can’t just `pip install nsmq-tricks`. I had to build the data from scratch.

Here is the engineering breakdown of how I went from 585 raw YouTube videos to a quantized Llama-3 model that thinks like a grandmaster.

---

## Phase 1: The Great Data Heist (Ingestion) 📺

The first step in any ML project isn't modeling; it's being a digital hoarder. I needed historical data, and the only place it existed was on YouTube.

**The Stack:** `yt-dlp` (the backend MVP) and `FFmpeg`.

I scraped **585 distinct video files**. But here’s the constraint: Internet data in Ghana isn't free, and bandwidth is precious. 💸 I didn’t need the video. I didn't need to see the contestants sweating; I just needed the audio.

I built an extraction pipeline that discarded video streams immediately, pulling only the audio tracks and converting them to **16kHz mono .wav** files. This is the gold standard for Speech Recognition engines. Even with this optimization, it took **7 days** of high-latency ingestion to avoid rate limits.

---

## Phase 2: The Janitorial Work (Cleaning) 🧹

If you take nothing else from this post, take this: **Data Quality > Data Quantity.**

I started with 585 files. I ended up with **432**. I threw away nearly **26%** of my dataset.

Why? Because "Noise" kills models. 🗑️
* Opening ceremonies? **Trash.**
* Advertisements? **Trash.**
* Crowd cheering for 5 minutes? **Trash.**

If I fed this junk to the model, it wouldn't just hallucinate; it would start outputting bank adverts in the middle of a calculus problem. I manually and semi-automatically filtered the dataset to ensure we only kept "High-Signal" content.

> 🔥 **Hot Take:** Real AI engineering is 80% cleaning data and 20% tweaking parameters. If you aren't willing to clean your data, don't bother training.

---

## Phase 3: Turning Sound into Text (The Pipeline) 🗣️➡️📝

Now I had 432 clean audio files. I needed text.

I spun up a distributed transcription pipeline on **Microsoft Azure** Cloud. I used 3x **Standard_E8ds_v4** VMs. That gave me 24 vCPUs to play with. 💻

I chose **CPU over GPU** here for a specific reason: `faster_whisper` (a CTranslate2 implementation of OpenAI’s Whisper) is insanely optimized for vector instructions on modern CPUs. Burning GPU credits for simple batch transcription is a waste of money.

**The Result:**
* **Runtime:** 96 hours (4 days). ⏳
* **Throughput:** 432 hours of audio converted to timestamped text.

---

## Phase 4: Encoding the "Shortcuts" (The ETL Layer) 🤖

This is the secret sauce. **I didn't just want the AI to solve the problem. I wanted it to teach the TRICK.**

Raw transcripts just give you the answer. That’s useless for a competition where speed is everything. I needed a structured JSONL format that encoded the strategy:

* **System:** The "NSMQ Coach" persona. 🎓
* **User:** The extracted science/math riddle. ❓
* **Assistant Output:**
    1.  **The Answer:** (The correct value).
    2.  **The Strategy:** "Don't integrate this fully. Use the symmetry of the curve."
    3.  **The Trap:** "Most students forget the constant of integration here. Don't be them."

I used a larger LLM (GPT-4 class) as a transformation agent to force the raw text into this strict, strategy-focused schema.

**The Brutal Reality:** Out of 432 transcripts, only **260** made the cut to become valid JSON files.
* **Yield:** 60.1%. 📉

I ruthlessly filtered out incoherent dialogue. If the "Coach" couldn't explain the **shortcut**, the data point died. This left me with **~20,384 high-quality training tokens** focused purely on game strategy.

---

## Phase 5: The Training Arc (Fine-Tuning) 🚀

For the actual brain surgery, I moved to **Google Colab Pro+** with an **NVIDIA A100 (40GB VRAM)**. 🏎️

I used the **Unsloth** framework for fine-tuning. If you are fine-tuning Llama 3 and not using Unsloth, you are doing it wrong. It makes training 2x faster and drops memory usage by 60%.

**The Technique: LoRA (Low-Rank Adaptation)**
Full fine-tuning updates every parameter in the model (expensive). LoRA freezes the main brain and only trains a tiny "adapter" layer (0.5% - 2% of parameters). This allowed me to teach the model the NSMQ format without needing a cluster of H100s.

**The Challenge: "Catastrophic Forgetting"** 🤯
At one point, the model got so good at math riddles it forgot how to have a normal conversation. I had to re-balance the dataset and adjust the System Prompt to stop the model from collapsing into a pure riddle machine.

---

## Phase 6: Shrinking the Brain (Edge Deployment) 📱
I had to take a model trained in **FP16 (6GB)** and crush it down to fit on a phone with 4GB RAM.

**The Solution: Quantization.** 📦
I used the `q4_k_m` method to convert the model to **4-bit GGUF**. This reduced the size to **1.7GB**—a 70% reduction.


## Conclusion: The "AI-First" Workflow 💡

This project taught me that the role of a software engineer is shifting.

I didn't write every line of boilerplate code for this pipeline. I acted as the **Systems Architect**. I used AI tools to generate the syntax for the scraper and the visualization scripts, while I focused on the **logic verification, architectural decisions, and debugging**.

This "AI-Augmented" workflow turned what should have been a 3-month project into a few weeks of intense engineering. ⚡

The final result? An app that doesn't just Google answers, but actually *thinks* like an NSMQ champion—giving every student the tools to win. 🏆
