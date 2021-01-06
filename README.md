# Optimizing an ML Pipeline in Azure

Author: Avineil Jain

## Overview
This project is part of the Udacity Azure ML Nanodegree.
In this project, we build and optimize an Azure ML pipeline using the Python SDK and a provided Scikit-learn model.
This model is then compared to an Azure AutoML run.

## Summary
The dataset contains information about clients such as their job, education, contact details etc, and we have to predict whether the customer will subscribe to the term deposit. This is a classification task, with the output variable being either 1 or 0. 

The best performing model, was found out by the Azure AutoML and it was a VotingEnsemble model, with an accuracy of 0.917%. 

## Scikit-learn Pipeline
For Scikit-Learn, we train using the standard Logistic Regression model. The dataset is first cleaned, converting all strings to categorial numbers, and then converting some fields via one-hot encoding. The 2 hyper-parameters that are used for the training include the regularization constant C, and the max number of iterations. The Hyperdrive solution involves iterating over these parameters. 

For the hyperdrive config, I select the parameters from a list of choices that I give. The following logic is used - 
1) For C, it is advised to have a exponential scale for the parameters, thus, I select a wide range of parameters in the multiple of 2, 5 or 10. 
2) For max iterations, usually, the more the better, so I select again select some choices. 
I decided to go with fixed choices, since I knew what parameters I wanted to select, and this limited the number of iterations for finding the optimal parameters. Choosing uniform or random sampling would have required more iterations (not cost effective, since I am using my own subscription.) 

I use the bandit early stopping policy that is provided by Azure. i use a slack_amount of 0.1, which means that whatever runs result in an accuracy which is 10% less than the best are discarded. This is more useful when we have random sampling over many iterations, but its not that useful in my use-case. 

## AutoML
For AutoML, I let it run with all the available models it had, with 50 minutes of run-time. It ran a bunch of different algorithms - such as XGBoost, LightGBM, VotingEnsemble, Logistic Regression, Random Forest etc, with VotingEnsemble method performing the best.  VotingEnsemble uses the outputs of other models, and creates an ensemble machine for better prediction.


## Pipeline comparison
For the hyperdrive run, we use logistic regression with the best performing model giving an accuracy of 0.913 % with hyperparameters: C=50, and max_iter=200. 
For the AutoML run, it uses a VotingEnsemble which has an accuracy of 0.917%. The VotingEnsemble consists of XGBoost models. 

There is a small difference in accuracy, and the differences in architecture come from the fact that I allowed AutoML to test on a variety of different algorithms. We only used logistic-regression with hyperdrive, whereas AutoML was free to select whatever model fit the data the best. 


## Future work
The dataset is highly biased with only 10% of samples being positive. Thus, a simple model which always preducts 0, will have an accuracy close to 90%. Future improvements include steps to target the bias such as using a different optimization metric and weighting the positive class during training. These steps will definitely help us get a better model. 


## Proof of cluster clean up
I use my own machine, hence this section is not applicable. 
