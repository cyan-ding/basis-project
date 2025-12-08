# BASIS Final Project

> Cyan Ding and Brandon Qi

We do PGD **(projected gradient descent)** adversarial training to fine tune an instruct model to be more robust against attacks.

We then run various attack algorithms to benchmark the performance of our model.


## Fine tuning process

For each forward pass on Llama 3.1 8B or Gemma, we run three iterations of projected gradient descent to craft a suffix.
- This suffix is optimized to increase loss, or push the model response toward the non-safe direction as much as possible
- We then do supervised fine tuning using the 30k train dataset using on PKU-Alignment/BeaverTails.
- This dataset is originally meant for RLHF work and preference opimization. However, we adapt it for PGD fine tuning. 
- The way we do this is by filtering the dataset for is_safe, meaning responses that are safe in respect to an unsafe prompt

We then take the 11604 safe rows out of 27186 total rows and format a prompt that would mimic the assistant format in a real chat bot. 
- In this process, we append the unsafe suffix onto the prompt, and train the model to still produce safe responses given additional unsafe additions.
- We run 100 epochs, with a learning rate of 2e-4. We did not include a val dataset, which could have led to overfitting/lack of hyperparam optimization. That is something to explore in the future. 


## Benchmarking

Our goal during benchmarking is to see whether our fine tuned PGD model performs better against adversarial prompt suffix optimization attacks than the base model


## Results

## Models

You can find our models here:
- [Fine tuned Llama 3.1](https://huggingface.co/blueplus/basis-project-llama-3.1-8b-finetune)


