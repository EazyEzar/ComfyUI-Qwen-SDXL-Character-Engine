\# ♾️ Qwen-to-SDXL: The Infinite Character Engine



\*\*A "Zero-to-Hero" ComfyUI pipeline that generates consistent character sheets using Qwen-2.5-VL and immediately fine-tunes a custom SDXL LoRA on them.\*\*



!\[Banner](assets/final\_result\_test.png)



\## 🎯 The Concept

The biggest challenge in AI character design is consistency.

\* \*\*Problem:\*\* Generating the same character in different poses using only prompts is unreliable.

\* \*\*Solution:\*\* This workflow uses a two-stage architecture:

&nbsp;   1.  \*\*Spatial Consistency (Qwen):\*\* Uses Qwen-2.5-VL's superior spatial intelligence and a custom "3-Box Template" to force the generation of a perfectly aligned character sheet (Front, Side, Back).

&nbsp;   2.  \*\*Style Locking (SDXL Training):\*\* Automatically crops these views and trains a \*\*LyCORIS/LoCon\*\* adapter (LoRA) entirely within ComfyUI.



The result? You go from a text prompt to a fully trained, reusable character model in ~15 minutes on a consumer GPU (RTX 4080 12GB).



\## 🖼️ The Pipeline



\### 1. Generation (The "Golden Source")

We utilize a hard-edged 3-panel mask (`assets/template\_3box.png`) to constrain Qwen. This forces the model to generate three distinct views of the \*same\* entity in one pass, ensuring outfit and facial consistency.



| Input Template | Generated Dataset (Qwen) |

| :---: | :---: |

| !\[Template](assets/template\_3box.png) | !\[Dataset](assets/generated\_dataset.png) |



\### 2. Training (In-Comfy Kohya)

Leveraging the \[ComfyUI-FluxTrainer](https://github.com/kijai/ComfyUI-FluxTrainer) nodes (wrappers for Kohya-ss), we train an SDXL LoRA on the fly.

\* \*\*VRAM Optimization:\*\* Uses Batch Size 1 with Gradient Accumulation to fit on 12GB VRAM.

\* \*\*Optimizer:\*\* Adafactor (memory efficient).

\* \*\*Type:\*\* LyCORIS/LoCon (Bypasses standard LoRA limitations for better feature capture).



\## 📉 The Truth About "Target Loss"

A common question is: \*"What is the perfect loss number?"\*

In this experiment, I pushed the training to \*\*1200 steps\*\* (approx. 400 epochs on a small dataset).



\* \*\*High Loss (> 0.15):\*\* Underfitting. The model hasn't learned the concept.

\* \*\*Healthy Loss (0.12 - 0.08):\*\* The "Goldilocks" zone. Flexible and editable.

\* \*\*Overfitting Zone (< 0.06):\*\* The model starts memorizing pixels/noise rather than the concept.



\*\*My Result:\*\* The final loss averaged \*\*0.0527\*\*, which is technically overfit.

\*\*The Fix:\*\* When using an overfit LoRA, we lower the `strength\_model` during inference (e.g., \*\*0.76\*\* instead of 1.0). This recovers the flexibility while keeping the likeness, as seen in the final results.



\## 🚀 How to Use



\### Prerequisites

\* \[ComfyUI](https://github.com/comfyanonymous/ComfyUI)

\* \[ComfyUI-FluxTrainer](https://github.com/kijai/ComfyUI-FluxTrainer) (Thanks to Kijai for the amazing work!)

\* \[WAS Node Suite](https://github.com/WASasquatch/was-node-suite-comfyui)

\* A Qwen Image Model (e.g., Qwen-2.5-VL) \& SDXL Base Checkpoint.



\### Steps

1\.  \*\*Setup:\*\* Place `template\_3box.png` in your ComfyUI input folder.

2\.  \*\*Generate \& Train:\*\*

&nbsp;   \* Load `workflows/01\_generate\_and\_train.json`.

&nbsp;   \* Set your prompt in the Qwen text node.

&nbsp;   \* Click Queue. (Approx. time: 10-15 mins).

3\.  \*\*Inference:\*\*

&nbsp;   \* Load `workflows/02\_inference\_test.json`.

&nbsp;   \* Select your newly created LoRA.

&nbsp;   \* Set strength to ~0.75 if the loss was low.

&nbsp;   \* Generate new poses!



\## 👏 Credits

\* \*\*Workflow Design:\*\* \[Your Name]

\* \*\*Training Nodes:\*\* Based on the excellent work by \*\*Kijai\*\* (\[ComfyUI-FluxTrainer](https://github.com/kijai/ComfyUI-FluxTrainer)).

\* \*\*Core Tech:\*\* Qwen Team (Vision Model), Kohya-ss (Training Scripts).



---

\*Created with ComfyUI on NVIDIA RTX 4080 (12GB).\*

