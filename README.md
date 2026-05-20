#  LLM Inference-Time Scaling

Implementing and evaluating inference-time scaling techniques for Large Language Models (LLMs) on complex mathematical reasoning tasks. This repository explores how allocating more compute during inference (rather than training) can significantly boost an LLM's problem-solving capabilities.

##  Overview
This project evaluates the performance of **DeepSeek-R1-Distill-Qwen-1.5B** on a subset of the **MATH-500 Benchmark**. To accelerate local inference, the model is served using **vLLM**. We compare a baseline approach against advanced inference scaling methods that explore multiple reasoning paths to find the correct answer.

##  Implemented Methods

1. **Chain-of-Thought (CoT) Prompting**
   - Forces the model to generate intermediate, step-by-step reasoning before outputting the final answer in a LaTeX `\boxed{}` format.

2. **Best-of-N Sampling**
   - Generates *N* independent candidate solutions.
   - **Log-Prob Scoring:** Selects the best candidate based on the highest average token log-likelihood.
   - **LLM Verification:** Uses a secondary LLM API (e.g., Gemini Mini) as a judge to evaluate the generated candidates and pick the correct one.

3. **Beam Search Reasoning**
   - Generates reasoning chains step-by-step (e.g., *Understand* -> *Plan* -> *Solve*).
   - Expands multiple candidate continuations at each step.
   - Uses token log-probabilities (or an LLM judge) to prune the search space and retain only the most promising reasoning paths.

##  Tech Stack
* **LLM Engine:** [vLLM](https://github.com/vllm-project/vllm) for high-throughput, memory-efficient local serving.
* **Model:** `deepseek-ai/DeepSeek-R1-Distill-Qwen-1.5B`
* **Evaluation:** Hugging Face `datasets` (MATH-500), Regex-based LaTeX normalization for automated answer checking.
* **External API:** Google Gemini API (used as an external evaluator/judge).

##  Evaluation Framework
The project includes a robust evaluation suite that:
- Extracts answers securely using `\boxed{}` LaTeX parsing.
- Normalizes complex mathematical expressions (fractions, matrices, intervals, polynomials) so that equivalent answers (e.g., `\frac{1}{2}` and `0.5`) are graded correctly.
- Saves execution results to JSON for later analysis.
