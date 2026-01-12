1 -> Classification finetuning of GPT2 : "FinetuningGPT2forClassification" first download gpt2 weights from the file gpt_download and then loads into GPT2 model code which are downloaded from the file GPT2ClassesFunctionsCode.py.

2 -> Nano GPT-OSS : I built the GPT-OSS architecture from scratch, and instead of 20B model, I built a 588M model. I used the A100 from lightning AI to accomplish this.

3 -> Trying to derive Keller's co-efficients : In Keller Jordan's blog, he says that he derived the co-efficients for his optimizer via an ad-hoc method, so I wanted to derive and see whether my experiments would also converge to his numbers. And that is what this notebook is about.
