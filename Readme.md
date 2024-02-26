# Katherine Cole's Repository

This is a link to assignment 4/5: [Link]()

## Collaborators:
* None

# Choices

* For this assignment, I classified symbols a-->g and then completed the DIGIT VS LETTER classification from the original assignment
* For both questions, I performed nested cross fold validation (outer loop K=5) to practice this technique
* For both questions, I set aside a test set of 20% used at the very end. The complete training data was then subdivided into a validation subset (25%), and the remainder as a training set.
* I generated confusion matrices and performance metrics for the predictive performances on the entire training data AND the holdout test data.
* I performed model selection based on test accuracy

## Part 1- Classify all symbols from a --> g
* I selected the random forest model, as it is performed the best in the digit vs letter classification (I did this question first), and because RF was less computationally intensive.
* As indicated above, I performed nested cross validation. To ensure that the best model was correctly identified in the inner loop hyperparameter tuning, I printed the validation accuracy for each hyperparameter iteration, stratified by fold. This enabled me to easily identify errors in my code (as I knew what the "right" answer should be).
* I tuned based on number of estimators, and maximum depth, and each fold was subdivided based on the number of hyper-parameter combinations.
* For the outer fold training, I selected the best hyper-parameter set and trained across the 5 folds (I did not print the output per fold, but I did test it outside of the submitted assignment). 
* From here, the best model was trained on the complete training set (80%, inclusive of the train and validate data).
* Afterwards, I used the best model to assess the predictive capability of the hold out test set (20%) which had not previously been seen by the model.
* After running my initial model on all the a--> g data, I decided to conduct a sensitivity analysis where I removed the poorest performing labels- in this case, I removed a, c, g and e. I then retrained a new model, and examined the differences in performance. The missclassification rate was not particularly high, however, there were still some improvement in predictive performance in removing labels with a misclassification rate higher than 40.

## Part 2- Classify digits vs letters
* For this question, I considered random forest, xgboost, and logistic regression.
* The methodology behind this question is very similar to part 1 --> I used nested CV (k=5 for the outer loop).
* I did confirm that the model did not converge for LG without scaling of the data, so I performed scaling prior to doing this question
* During hyper-parameter tuning, I examined each of the 3 algorithms up front. The algorithm with the best val accuracy and associated hyper-parameters from inner loop training was used in the later outer fold assessment. I needed to insert a specifification for handling of the hyperparameters unique to logistic regression, as there were challenges in identifying the best hyperparameters when I ran code isolated to this model type only.

## Difficulties with Assignment #4
* The most challenging part of this assignment was visualizing the nested CV process and working to develop the correct code
* I learned the importance of testing each step of the process, as I found errors in the "best model" that I would not have identified if I hadn't printed each loop iteration.




