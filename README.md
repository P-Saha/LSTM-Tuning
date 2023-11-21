# LSTM-Tuning
Priyonto Saha, Katherine Lu, Zhi Lin Zhou

## Introduction
Due to the widespread adoption of mobile devices, a continuous stream of data from accelerometers and gyroscopes can be collected and utilized towards healthcare improvement. Predicting human activity based on raw sensor data has profound implications for healthcare, with potential in remote patient monitoring, rehabilitation, personalized fitness, and early detection of health conditions through movement patterns. Thus, the objective of this paper is to train a Long Short-Term Memory (LSTM) model capable of recognizing and classifying human activities on sequential sensor data, and to analyze the influence of hyperparameter adjustments on predictive model performance.

## Data Engineering Process
A collection of multi-dimensional time-series data from wearable sensors placed on 10 subjects’ left ankles and right lower arms was used for model training and testing. Raw sensor measurements for acceleration and gyroscopic across three axes provided a total of 12 sensor measurement variables in total (a combination of left ankle and right arm, gyroscopic angle and linear acceleration, and the x, y, z axes). These sensor measurements are sampled at 50 Hz in a time ordered sequence, and are annotated with the following 13 activity labels: 0: Nothing, 1: Standing still, 2: Sitting, 3: Lying down, 4: Walking, 5: Climbing stairs, 6: Waist bends, 7: Arm lifts, 8: Crouching, 9: Cycling, 10: Jogging, 11: Running, 12: Jumping. Overall, there were 999,999 records across the 10 subjects in total, with no missing values within the dataset.

Exploratory data analysis began with descriptive statistics and data visualization through histograms to determine how the records were distributed across subjects and the distribution of activities across subjects. Subject 9 was found to only display sedentary behavior consisting only of nothing, standing still, or sitting, and had the least number of records (17,726), and thus these records were removed from the data as outliers. No records were found for a supposed subject 10. Subjects 1 to 8 each had around 100,000 to 160,000 records, all distributed similarly with regards to activity. These subjects all had at least 60,000 records with no activity (labeled 0), exactly 3072 records for activities 1, 2, 3, 4, 5, 9, 10, and 11, and exactly 1024 records for the running activity (labeled 12) except for subject 1, who had 1075. This pattern does not hold for activities 6, 7, and 8, but each subject has a similar number of records (all between 2000 and 3500) for these activities. 

## Analysis
An LSTM model was used in order to accommodate the sequential nature of the data. To prepare for analysis, data was first split into training and validation sets by patient with 6 patients in the training set and 2 patients in the validation set. Both datasets were then normalized, grouped by activity and subject, and lastly split into sequences of length 100. The label for each sequence was determined by the max activity level present in each sequence, allowing for a more representative distribution of the activity levels. To address class imbalance in the training dataset while maintaining the integrity of the temporal nature of the data, undersampling was performed by randomly dropping entire sequences labeled as 0 from the training set with a 95% chance.

A total of 36 LSTM models were trained using 100 epochs, each with an LSTM layer, a linear hidden layer, and a softmax activation function for multi-class classification. The base model was purposefully kept to be simple in order to determine how hyperparameter tuning affected the overall predictive performance of the model. Gridsearch was used to compare combinations of batch sizes (64, 128, 256, and 512), learning rates (0.001, 0.01, and 0.1), and the number of neurons in the hidden layer (16, 32, and 64). The Adam optimizer was chosen to compute the gradients and adjust model weights, with Cross Entropy Loss chosen as the loss function to optimize. 

## Findings
Our exploratory data analysis revealed a significant class imbalance alongside strange behavior in the distribution of activities among subjects as mentioned in our data engineering process. As the data was sorted by subject and then by time, data for a subject 10 was expected, and there are very little records for subject 9 (these records being the very last rows of the dataset), it is believed that data may have been truncated in the middle of subject 9’s sequential data after 999,999 observations. A spaghetti plot of subject activity overtime revealed that subjects typically went from doing no activity (0) to one specific activity for some amount of time before going back to no activity, never switching between two different activities.

Overall, the accuracy of the best models were around 68% to 73%. The best number of epochs for almost all of the models was found to be around 30, with accuracy not improving much with more epochs beyond. The best overall batch sizes were found to be 64 and 128, and the best overall learning rate was found to be 0.01. There is no clear winner for the size of the hidden layer. It seemed that a learning rate of 0.1 consistently yielded low accuracy scores under 50%, with one exception in the model with batch size of 512 and 16 neurons in the hidden layer which stabilized at around 69% accuracy after 30 epochs. We note that although a larger batch size typically implies a larger learning rate should be used, other models with batch size 512 and learning rate 0.1 but different hidden layer sizes performed very poorly. Thus it is clear that hidden layer size has a meaningful impact on model performance, even within other models, but the specifics of its influence eludes us. Large batch sizes of 512 also appeared to perform quite badly, especially with smaller sizes for the hidden layers, except for our outlier model case.

## Conclusion
Neural networks are powerful and flexible predictive models, and LSTMs further emphasize the flexibility of these models demonstrating their capability to utilize the temporal nature of sequential data. However, extensive hyperparameter tuning is a necessary step to unlock the full potential of these models as seen. The long training time, high complexity, and a lack of interpretability makes model tuning difficult, especially in more complex models with additional hidden layers and varying activation functions. Future direction includes trying different optimizers and loss functions to see how they affect model performance in unison with our other hyperparameters tuned. 
