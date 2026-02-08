1 -> Classification finetuning of GPT2 : "FinetuningGPT2forClassification" first download gpt2 weights from the file gpt_download and then loads into GPT2 model code which are downloaded from the file GPT2ClassesFunctionsCode.py.

2 -> Nano GPT-OSS : I built the GPT-OSS architecture from scratch, and instead of 20B model, I built a 588M model. I used the A100 from lightning AI to accomplish this.

3 -> Optimizers : Trying to derive Keller's co-efficients. In Keller Jordan's blog, he says that he derived the co-efficients for his optimizer via an ad-hoc method, so I wanted to derive and see whether my experiments would also converge to his numbers. And that is what this notebook is about.

4 -> Activation Engineering : Upon reading Turner et al 2024 paper, I came across so many papers which various pov's, so I did mock experiments on those papers and tried to understand their contributions. 

      A> Intervening on token embedding with soft prompting - Li & Liang 2021 add trainable vectors to every single layer of the transformer network but I experimented with Lester et al., 2021 who added the trainable vector only at the input layer. 
      
      B> Intervening on activations - Subramani et al. (2022) - 
      Goal: They wanted to see if they could force a frozen model to output an exact specific sentence (e.g., "The weather is blue") just by injecting a vector.
      Method: They freeze the model.They create a random vector z.They run the model and compare the output to the target sentence.
      Gradient Descent: They calculate the error (loss) and use backpropagation to update the vector z (not the model weights).
      They repeat this until the vector  z  is perfect.
      Result: They found "Steering Vectors" that act like a remote control, forcing the model to say anything they want.

      C> Intervening on activations - Hernandez et al. (2023): "The Knowledge Editor" - 
      Goal: They wanted to find where facts (like "Paris is in France") are stored and edit them (make the model think "Paris is in Rome").
      Method: They realized that relationships (like City  →  Country) often look like straight lines (linear) in the activation space. 
      Gradient Descent (Jacobian): They used the model's gradients (specifically the derivative of the output with respect to the input) to mathematically calculate the "direction" of that fact.
      They found a vector that, when added to "Paris", shifts the activations to land on "Rome".
      Result: They could "edit" the model's beliefs by injecting this vector.

      The Experiment :
      The Prompt: "The Eiffel Tower is in" (Model expects "France").
      The Goal: Edit the model to believe it is in "Rome".
      The Calculation: Instead of training, we run one pass. We calculate the gradient of the word "Rome" with respect to the activations. This tells us: "Which direction do I push the neurons to make 'Rome' the most likely next word?"
      The Injection: We freeze the model and add this gradient vector (scaled) to the activation during inference.

      The model learned to say Rome, but it was tough to make it forget paris because eiffel tower is linked to paris at so many level not just country.

      D> Invervening on activations - Li et al. 2023b: Inference-Time Intervention (ITI)
      Method : Rank heads by probe accuracy; select top‑K. For each selected head, define a truth direction (probe weight or mean-difference direction). During inference, add ασθ\alpha \sigma \thetaασθ to those head activations, where σ\sigmaσ is the standard deviation along that direction. Repeat autoregressively for each generated token.
      Results : ITI substantially improves TruthfulQA performance (they emphasize a large boost, including on instruction-tuned variants). The method is minimally invasive, computationally cheap, and adjustable via α\alphaα. They observe a truthfulness ↔ helpfulness tradeoff, controllable by intervention strength.
      
      
