# NLP Binary Text Classification Model

## 1.	Data Pre-processing:

The train data has 44K entries and 3 columns. The “Outcome” column is the label; the “Comment” column contains the text that the model needs to train on. By printing and inspecting some of the comments, following points were highlighted: there are website links referring to solutions or ideas, functions with brackets and special punctuations, contractions, and also numbers mixed with texts. 
Bear these in mind, the pipeline of data pre-processing was conducted in the following steps:

•	Denoise the text: by expanding contractions (e.g. “I’m” to “I am”; “can’t” to “can not”), which makes the tokenization stage cleaner and more straight forward;

•	Tokenize the text: using nltk (the natural language toolkit) library to split sentences into list of words, which helps further normalization of the words;

•	Normalize the tokenized words (list of words): first by converting all characters to lower case (seems they have already been converted), then removing all punctuations from the list of words, thereafter replacing the integer occurrences with textual representation with reflect library, and at last remove all the English stop words (e.g. “a”, “about”, “for”, etc.);

•	Stem/Lemmatize the list of words using nltk library, which aimed to reduce inflectional forms and derivationally related forms of a word to a common base form;

•	Detokenize the list of words to text for the feature generation with count vector or TF-IDF. The comparison of the original comment and cleaned comment was shown below:

![alt text](https://github.com/damengjin/NLP-Classification-model/blob/main/NLP/1.png)

## 2.	Feature Engineering:

The next step is the feature engineering step. In this step, raw text data was transformed into feature vectors and new features were created using the existing dataset. The following methods were implemented to try to find the optimal features to fit the models:

•	Count Vectors as features: generated a matrix notation of the data frame in which every row represents a comment from the train datasets, every column represents a term from the comments, and every cell represents the frequency count of a particular term in a particular document;

•	TF-IDF Vectors as features: instead of simple frequency counts of terms, TF-IDF generated scores composed by two frequencies: normalized Term Frequency = (# of times term t appears in a document) / (# of terms in the document), and Inverse Document Frequency = loge (# of documents / # of documents with term t in it). This score could represent the relative importance of a term in the comments and entire corpus; (TF-IDF vectors were generated in words, characters, and n-grams levels);

•	Word Embeddings: mainly used for neural networks models. word embedding is a dense vector representation, where the position of words within the vector space are learnt from text based on its surrounding words. For this competition, word embedding transfer learning was generated using Glove ("glove.6B.50d”: pre-trained words 50 dimensions);

•	Text / NLP based features: In order to boost performance of text classification models, a number of extra text based features were also created from the uncleaned comment texts (since after cleaning, some features might not be representative): number of words, number of characters, length of the words, number of punctuations or websites, and the frequency distribution of part of speech tags (like count of noun, verb, adjective, adverb, pronoun). Most of these were experimental ones, not all were used in the final model. Sample features are shown as below:

![alt text](https://github.com/damengjin/NLP-Classification-model/blob/main/NLP/2.png)

## 3.	Choice of Models:

The next step in the text classification framework is to train a classifier using those features created in the previous step. The following different classifiers were implemented for guiding purpose:

•	Naive Bayes: a classification technique based on Bayes’ Theorem with an assumption of independence among predictors. This assumption may not be valid (baseline);

•	Linear Classifier: logistic regression was also used to measure the relationship between the categorical label and one or more independent features by estimating probabilities using a logistic/sigmoid function, which also serves as a baseline;

•	SVM: support vector machine is a supervised machine learning algorithm which was used for text classification problem in this challenge. The model extracts a best possible hyper-plane that segregates the two classes;

•	Random Forest: RF was chosen among many bagging models (ensemble models). It is one of the tree based model family. This model was selected since bagging model could potentially avoid overfitting by building many trees and also good for feature importance estimates;

•	Xtereme Gradient Boosting: XGB is another type of ensemble models part of tree based models. Boosting is a technique for primarily reducing both bias and variance in supervised learning, and a technique that converts weak learners to strong ones;

•	Shallow Neural Networks: a simple shallow NN was implemented with mainly three layers, trying to recognize complex patterns and relationships that exists within a labelled data. This provided a guide for the later CNN deep NN models.

For all the above mentioned models, 75% of the train dataset was used to fed into them and the rest 25% used as validation dataset to obtain the accuracy score. These accuracy results were merely a guide to choose models to implement and features to use in the finalized infrastructure. The accuracy results are shown below:

![alt text](https://github.com/damengjin/NLP-Classification-model/blob/main/NLP/3.png)

TF-IDF word level were selected as features, together with the text based features; and most of the models were selected to be used in the final Stacking infrastructure model as weak learners. Furthermore, since shallow neural networks did not learn properly, the keras library would be implemented to train CNN deep learning models in the final Stacking infrastructure.

## 4.	Stacking Infrastructure & Validation: 

Final infrastructure adopted stacking techniques. The initial 44K samples in the training dataset were split into train and validation dataset (75% train and 25% validation). The following models: logistic, random forest, XGB, Adaboost, and extra-tree classifier were fine-tuned using k-folder (k=5) cross validation on the 75% of the training data to obtain the optimal hyper-parameters. After tuning, the overall accuracy has all been raised up (All above 0.68, XGB accuracy has been raised up above 0.7). Then using the validation data to examine the accuracy of the trained models. Additionally, using the CNN model from keras library to build a deeper network than the previous one, train the model with training data and validate on the rest. At the same time, all these predicted probability of the outcome of the validation dataset were kept and then aggregated as the input of the training samples in the final stacking model. The validation accuracies have been recorded here:

![alt text](https://github.com/damengjin/NLP-Classification-model/blob/main/NLP/4.png)

The stacking model implemented the logistic classifier, trained on the output probabilities of the outcome being “1” from previous trained models, and then predict the outcome of the test dataset as the final output. The Stacking model was trained on 8.3K samples (75% of the original validation set and validated on the 2.8K samples), which was validated to have the highest validation accuracy: 0.713.

![alt text](https://github.com/damengjin/NLP-Classification-model/blob/main/NLP/5.png)

## 5.	Discussion of model performance:
### 5.1.	 Ways implemented intend to improve the model performance

•	Text Cleaning: pre-processing section helped reduce the noise present in text data in the form of stop words, punctuations marks, suffix variations etc; H-stacking Text/NLP features with text feature vectors : In the feature engineering section, the generated feature vectors were combined together can help to improve the accuracy of the classifier, however, the result didn’t show much improvement. (Therefore additional features were dropped);

•	Tuning the hyper-parameters is an important step, a number of parameters such as tree length, leaves, network parameters were all fine-tuned and did result in a higher accuracy;

•	Ensemble Model: Stacked seven different models and blended their outputs to further improve the results.

### 5.2.	 CNN performance:
•	Networks with convolutional and pooling layers are useful for classification tasks, CNN could learn that certain sequences of words are good indicators of the topic, and do not necessarily care where they appear in the document. Convolutional and pooling layers allow the model to learn to find such local indicators, regardless of their position;

•	The maximum length of sequence was set to 1,000, and embedding dimension set to 50 (due to pre-trained word embedding was in 50-dimension space); The validation accuracy improves steadily from 0.60 to 0.66 with epoch increasing from 1 to 3, with greater computing power, the training accuracy could be further improved.


![alt text](https://github.com/damengjin/NLP-Classification-model/blob/main/NLP/6.png)
