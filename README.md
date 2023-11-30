# LAB-4: Backdoor-Attacks
This lab coursework is done as part of the course 'EL-GY-9163: Machine Learning for Cyber-security' offered in Fall 2023

## Data
   1. Download the validation and test datasets from [here](https://drive.google.com/drive/folders/1Rs68uH8Xqa4j6UxG53wzD0uyI8347dSq?usp=sharing) and store them under `data/` directory.
   2. The dataset contains images from YouTube Aligned Face Dataset. We retrieve 1283 individuals and split into validation and test datasets.
   3. bd_valid.h5 and bd_test.h5 contains validation and test images with sunglasses trigger respectively, that activates the backdoor for bd_net.h5.

```bash
├── data 
    └── cl
        └── valid.h5 // this is clean validation data used to design the defense
        └── test.h5  // this is clean test data used to evaluate the BadNet
    └── bd
        └── bd_valid.h5 // this is sunglasses poisoned validation data
        └── bd_test.h5  // this is sunglasses poisoned test data
├── models
    └── bd_net.h5
    └── bd_weights.h5
├── architecture.py
└── eval.py // this is the evaluation script
```

  


## III. Evaluating the Backdoored Model
   1. The DNN architecture used to train the face recognition model is the state-of-the-art DeepID network. 
   2. To evaluate the backdoored model, execute `eval.py` by running:  
      `python3 eval.py <clean validation data directory> <poisoned validation data directory> <model directory>`.
      
      E.g., `python3 eval.py data/cl/valid.h5 data/bd/bd_valid.h5 models/bd_net.h5`. This will output:
      Clean Classification accuracy: 98.64 %
      Attack Success Rate: 100 %

## IV. Important Notes
Please use only clean validation data (valid.h5) to design the pruning defense. And use test data (test.h5 and bd_test.h5) to evaluate the models.


----------------------

## Table of Contents
1. [Overview](#overview)
2. [Observations and submissions](#submission)

### 1. Overview <a name='overview'></a>
You must do the project individually. In this HW you will design a backdoor detector for BadNets trained on the YouTube Face dataset using the pruning defense discussed in class. Your detector will take as input:
1. *B*, a backdoored neural network classifier with *N* classes.
2. *Dvalid*, a validation dataset of clean, labeled images.

What you must output is *G* a “repaired” BadNet. G has *N+1* classes and given unseen test input, it must: 
1. Output the correct class if the test input is clean. The correct class will be in *[1, N]*.
2. Output class *N+1* if the input is backdoored.

You will design *G* using the pruning defense we discussed in class. That is, you will prune the last convolution layer of BadNet $B$ (the layer just before the FC layers) by removing one channel at a time from that layer. Channels should be removed in increasing order of average activation values over the entire validation set. Every time you prune a channel, you will  measure the new validation accuracy of the newly pruned BadNet. You will stop pruning once the validation accuracy drops at least X% below the original accuracy. This will be your new network $B'$. Now, your GoodNet *G* works as follows. For each test input, you will run it through both $B$ and $B'$. If the classification outputs are the same, i.e., class i, you will output class i. If they differ you will output *N+1*.

Evaluate this defense on:
1. A BadNet, *B1*, (“sunglasses backdoor”) on YouTube Face for which we have already told you what the backdoor looks like. That is, we give you the validation data and test data with examples of clean and backdoored inputs.

### 02. Observations and Submissions <a name='submission'></a>

The primary objective of this project was to refine a machine learning model through a series of steps including layer pruning, accuracy-based model saving, vulnerability assessment, and the creation of an optimized composite model.

1. **Layer Pruning and Model Saving:**
   Our approach involved pruning the `conv_3` layer based on the average activation from the last pooling operation across the validation dataset. We implemented a strategy to save models at specific accuracy drop thresholds of 2%, 4%, and 10%. The models were named `model_X=2.h5`, `model_X=4.h5`, and `model_X=10.h5`, respectively, signifying the accuracy drop percentage.

2. **Vulnerability Assessment:**
   Notably, we assessed the attack success rate when the model's accuracy dropped by at least 30%. This metric was observed at 6.954187234779596%, indicating a vulnerability threshold.

3. **Model Integration (GoodNet Design):**
   To enhance the model's performance, we pursued a strategy to combine two models: the flawed or compromised "BadNet" and a refined model post-repair. This process aimed to create a superior composite model, referred to as "GoodNet."

4. **Implementation Details:**
   The entire program and implementation steps are detailed in the `Lab3_sp5894.ipynb` file. This file encapsulates the codebase, encompassing the procedures for model creation, pruning, evaluation, vulnerability assessment, and the integration of models to form GoodNet.


Instructions on running the code - </br>
- Make sure that there is enough memory available for the execution (Ideally, Google Colab Pro subscription would do)
- The data set can be available from the links mentioned [here](https://github.com/csaw-hackml/CSAW-HackML-2020/tree/master/lab3) and the Google drive link to the data files is [here](https://drive.google.com/drive/folders/1Rs68uH8Xqa4j6UxG53wzD0uyI8347dSq)

Now, upon pruning the model, we can use the clean validation data set and test it on the test data set. The accuracy and attack success rate would look like this for the validation dataset -

![Diagram 1](https://github.com/Sagar-py/ECE9133-MachineLearningForCybersecurity/blob/main/Homework%2003/screenshots/accuracy-attack-success.png)

We can notice that the prune defense is not that successful here because the attack success rate does not drop too much. The attack success rate is okay, but not too good because it compromises the accuracy way too much. My hypothesis is that attack method is prune immune attack and that the pruned model is retained with the poisoned data.

Also, there is a side-by-side comparison of the performance of the repaired model and it can be seen here -

![Diagram 2](https://github.com/Sagar-py/ECE9133-MachineLearningForCybersecurity/blob/main/Homework%2003/screenshots/performance-repaired-model.png)
