# Katherine Cole's Repository

This is a link to assignment 4: [Link]()

## Collaborators:
* None

# Package Requirements
* I used ***

# Choices

## Part 1- Classify all symbols
* I selected the XG Boost model, as it is robust to overfitting and quite flexible. As I learned however, it is very computationally intensive in this large database. As a result, hyperparameter tuning was very time consuming, and so was deferred for this part of the assignment.
* I decided to conduct a sensitivity analysis where I removed the poorest performing labels- in this case, there were 4 outlier labels with high misclassification rates, which were excluded (0,1,L,I). I then retrained a new model, and examined the differences in performance.
* As an example, the original model with all labels had a f1 value of 0.69, compared to the trimmed model, which had a f1 score of 0.72

## Part 2- Classify digits vs letters
* The model evaluated consisted of 2 phases:
* Phase 1 involved assessing all 4 models (RF, LR, XGBoost, NN) and then comparing performance based on F1 scores.
* F1 scores were selected represents a more balanced overall assessment of model performance (unlike accuracy, which can perform well, but have poor sensitivity).
* The final test set consists of 10% of the data. 
* I created 2 train/validate splits which consisted of 30%/15% splits
* For this binary classification problem, "letter" was considered the "positive" label
* For part 2- I included the two best performing models based on F1 score (NN and RF) for use in the second train/validate split
* I used CV for hyper-parameter tuning in this set********
* At the end of part 2, NN had the best f1 score, so this model was utilized to assess performance in a final unseen test split
* The final f1 performance in the NN was 0.894


## Difficulties with Assignment #4
* The most challenging part of this assignment was dealing with the slow runtime




