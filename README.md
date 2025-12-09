# BASIS Final Project

> Cyan Ding and Brandon Qi

We do PGD **(projected gradient descent)** adversarial training to fine tune an instruct model to be more robust against attacks.

We then run various attack algorithms to benchmark the performance of our model.


## Fine tuning process

For each forward pass on Llama 3.1 8B, we run three iterations of projected gradient descent to craft a suffix.
- This suffix is optimized to increase loss, or push the model response toward the non-safe direction as much as possible
- We then do supervised fine tuning using the 30k train dataset using on PKU-Alignment/BeaverTails.
- This dataset is originally meant for RLHF work and preference opimization. However, we adapt it for PGD fine tuning. 
- The way we do this is by filtering the dataset for is_safe, meaning responses that are safe in respect to an unsafe prompt

We then take the 11604 safe rows out of 27186 total rows and format a prompt that would mimic the assistant format in a real chat bot. 
- In this process, we append the unsafe suffix onto the prompt, and train the model to still produce safe responses given additional unsafe additions.
- We run 100 epochs, with a learning rate of 2e-4. We did not include a val dataset, which could have led to overfitting/lack of hyperparam optimization. That is something to explore in the future. 


## Benchmarking

Our goal during benchmarking is to see whether our fine tuned PGD model performs better against adversarial prompt suffix optimization attacks than the base model.

To benchmark, we use GCG to produce adversarial suffixes that we would attach to unsafe inputs to simulate an attack.
- We first sample 100 random unsafe inputs from the BeaverTails dataset that we didn't use previously in the training set
- We had previously only done the adversarial training on rows where is_safe is true, during benchmarking we sample inputs from rows where is_safe is false
- We then run Greedy Coordinate Gradient, an optimization method to produce suffixes that would maximize the probability of the model saying "Sure, here is" to unsafe inputs. 
- We use GCG to produce 100 unsafe suffixes. 

During inference, we pass these 100 unsafe suffixes along with the corresponding prompts to both the base model and the fine tuned model, and measure Attack Success Rate (ASR)
- We accomplish this by using an LLM as a Judge, evaluating whether the model complied to the request. 


## Results

## Models

You can find our models here:
- [Fine tuned Llama 3.1](https://huggingface.co/blueplus/basis-project-llama-3.1-8b-finetune)


