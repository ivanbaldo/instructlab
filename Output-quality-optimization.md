There are a number of steps and parameters at various stages of the CLI workflow that you as a user could consider leveraging, if you are looking to optimize the quality of the outputs generated by the model. Some of these steps are discussed in following sections. 

It is important to note that improved response quality will come at a cost, in the form of either increased compute requirements, or increased time requirement, or both. The described steps will provide you with the best chance of improving the quality of your model's responses, but cannot guarantee an improvement in response quality. 

# Skill compositon

Composing and contributing effective and impactful skills is an iterative process. The typical workflow looks something like this:
- compose skill examples
- run `lab generate` 
- examine generated examples based on supplied skill
- If generated examples are not satisfactory in quality:
  - edit the skill examples 
- repeat the process until you are satisfied with the generated data

## Steps that can be taken in order to improve the skill yaml:
- Increase the number of examples in your skill yaml file. The more examples the model has to go off of, the faster it will be able to generate synthetic data. The generated data will also better if the input contains a wide range of examples
- Improve the quality of provided examples. Review the examples provided to it and see if they can rephrased in a way that they align better with what you are hoping to see the model generate. This will improve chances of the model generating better quality synthetic data in large quantities.


# Data generation 

The data generation step is executed via `lab generate`, and is responsible for generating synthetic data. This is an important step as it forms the basis for what the model will end up learning. 

## Steps that can be taken in order to improve the quality of generated data:
- Increase the number of instructions generated by passing the `--num-instructions` flag to `lab generate` as follows:
` lab generate --num-instructions 1000`. This will generate 1000 points of synthetic data based on your provided examples. The greater the number of instructions generated, better the model will be trained (within reasonable limits). 
- Adjust the rouge threshold via `--rouge-threshold`. Rouge threshold is a parameter that determines how accepting we want to be of the synthetic data generated by the model. Each new piece of generated data is scored for how similar it is to the data that has already been generated. The value of rouge threshold ranges from 1.0 to 0.0, where 1.0 indicates maximum leniency (we accept every new generated data point) and 0.0 indicates maximum strictness (we reject every new generated data point). Setting a rouge threshold value closer to 0.0 would force the model to generate data that is different from what it has already generated, leading to a more diverse dataset overall. Rouge threshold can be set as follows:
`lab generate --rouge-threshold 0.25`
- Using a better model via `--model-dir`. Larger models can lead to better data generation. This could mean either using a model with more nodes than the default IBM `merlinite-7b` model, such as the `Mixtral-8x7B-Instruct-v0.1` model, or using an un-quantized version of the IBM `merlinite-7b` model. This step requires users to be familiar with various existing models, and which specific models would suit their needs. It can be used as follows:
`lab generate --model mixtral0-88x7B-INstruct-v0.1`
- Set the number of CPU cores that can be used to generate data via `--num-cpus`. This defaults to 10, but increasing this value could potentially lead to better generated dat. It can be used as follows:
`lab generate --num-cpus 15`

*Still to be exposed to users*:
- Increase the number of examples that can be used by the model. This parameter controls how many of your examples are actually fed to the model to generate synthetic data. The more examples it sees, the better the quality of the synthetic data will be. This parameter is not currently exposed to users


# Training 

The training step can be invokeed via `lab train`. This step trains the model on the synthetic data that was generated. The output of this step is a set of adapter files with the general format `adapters-xxx.npz`, where `xxx` is a number. These adapter files represent a snapshot of the model's trained state and are periodically written to disk. 

## Steps that can be taken in order to train the model better:
- Increase the number of training iterations via `--iters`. A larger number of iterations usually means a better trained model (at least until a certain point). Increasing the number of iterations comes at the cost of having to wait longer for the training to complete. 
- Pick an adapter file with the lowest validation loss. The training process generates and persists an adapter file periodically. The terminal output will tell you the validation loss that each adapter is associated with. The frequency of adapter file generation will be controlled by `--save-every`. For example:
`lab train --save-every 10` would output an adapter file every 10th iteration 
  

*Still to be exposed to users:*
- Tweak learning rate, batch size etc. There are some other parameters that can be tweaked to affect the training process. These parameters are not currently exposed to the end users. 