# Quora_question_pair_similarity

![Quora_question_pair_similarity](https://user-images.githubusercontent.com/25454660/62774526-90f66780-bac2-11e9-95cf-5b31d3244537.jpeg)

case study of Quora question pair similarity problem

### Data Overview
- Data will be in a file Train.csv 
- Train.csv contains 5 columns : qid1, qid2, question1, question2, is_duplicate 
- Size of Train.csv - 60MB 
- Number of rows in Train.csv = 404,290

### Problem Statement

* Identify which questions asked on Quora are duplicates of questions that have already been asked.
* This could be useful to instantly provide answers to questions that have already been answered.
* We are tasked with predicting whether a pair of questions are duplicates or not.

### Type of Machine Leaning Problem
It is a binary classification problem, for a given pair of questions we need to predict if they are duplicate or not.


### Basic Feature Extraction (before cleaning)
Let us now construct a few features like:

* freq_qid1 = Frequency of qid1's
* freq_qid2 = Frequency of qid2's
* q1len = Length of q1
* q2len = Length of q2
* q1_n_words = Number of words in Question 1
* q2_n_words = Number of words in Question 2
* word_Common = (Number of common unique words in Question 1 and Question 2)
* word_Total =(Total num of words in Question 1 + Total num of words in Question 2)
* word_share = (word_common)/(word_Total)
* freq_q1+freq_q2 = sum total of frequency of qid1 and qid2
* freq_q1-freq_q2 = absolute difference of frequency of qid1 and qid2

### Preprocessing of Text
Preprocessing:
* Removing html tags
* Removing Punctuations
* Performing stemming
* Removing Stopwords
* Expanding contractions etc. 

### Advanced Feature Extraction (NLP and Fuzzy Features)
* cwc_min : Ratio of common_word_count to min lenghth of word count of Q1 and Q2 
* cwc_min = common_word_count / (min(len(q1_words), len(q2_words)) 
* cwc_max : Ratio of common_word_count to max lenghth of word count of Q1 and Q2 
* cwc_max = common_word_count / (max(len(q1_words), len(q2_words)) 
* csc_min : Ratio of common_stop_count to min lenghth of stop count of Q1 and Q2 
* csc_min = common_stop_count / (min(len(q1_stops), len(q2_stops)) 
* csc_max : Ratio of common_stop_count to max lenghth of stop count of Q1 and Q2
* csc_max = common_stop_count / (max(len(q1_stops), len(q2_stops)) 
* ctc_min : Ratio of common_token_count to min lenghth of token count of Q1 and Q2
* ctc_min = common_token_count / (min(len(q1_tokens), len(q2_tokens)) 
* ctc_max : Ratio of common_token_count to max lenghth of token count of Q1 and Q2
* ctc_max = common_token_count / (max(len(q1_tokens), len(q2_tokens)) 
* last_word_eq : Check if First word of both questions is equal or not
* last_word_eq = int(q1_tokens[-1] == q2_tokens[-1]) 
* first_word_eq : Check if First word of both questions is equal or not
* first_word_eq = int(q1_tokens[0] == q2_tokens[0]) 
* abs_len_diff : Abs. length difference
* abs_len_diff = abs(len(q1_tokens) - len(q2_tokens)) 
* mean_len : Average Token Length of both Questions
* mean_len = (len(q1_tokens) + len(q2_tokens))/2 
* fuzz_ratio : https://github.com/seatgeek/fuzzywuzzy#usage http://chairnerd.seatgeek.com/fuzzywuzzy-fuzzy-string-matching-in-python/ 
* fuzz_partial_ratio : https://github.com/seatgeek/fuzzywuzzy#usage http://chairnerd.seatgeek.com/fuzzywuzzy-fuzzy-string-matching-in-python/ 
* token_sort_ratio : https://github.com/seatgeek/fuzzywuzzy#usage http://chairnerd.seatgeek.com/fuzzywuzzy-fuzzy-string-matching-in-python/ 
* token_set_ratio : https://github.com/seatgeek/fuzzywuzzy#usage http://chairnerd.seatgeek.com/fuzzywuzzy-fuzzy-string-matching-in-python/ 
* longest_substr_ratio : Ratio of length longest common substring to min lenghth of token count of Q1 and Q2
* longest_substr_ratio = len(longest common substring) / (min(len(q1_tokens), len(q2_tokens))

### Featurizing text data with tfidf weighted word-vectors
* After we find TF-IDF scores, we convert each question to a weighted average of word2vec vectors by these scores.
* here we use a pre-trained GLOVE model which comes free with "Spacy". https://spacy.io/usage/vectors-similarity
* It is trained on Wikipedia and therefore, it is stronger in terms of word semantics.

### Results of different models
<table>
  <th> <td>Model</td>	<td>Train log loss</td>	<td>Test log loss </td> </th>
<tr><td>4	</td><td>Linear SVM with simple tf-idf	</td>	<td>0.532385</td>	<td>	0.558293</td></tr>
<tr><td>3	</td><td>Logistic Regression with simple tf-idf	</td>	<td>0.516410</td>	<td>	0.548527</td></tr>
<tr><td>0	</td><td>Logistic Regression with weighted Tf-idf	</td>	<td>0.513843</td>	<td>	0.520036</td></tr>
<tr><td>1	</td><td>Linear SVM with weighted Tf-idf	</td>	<td>0.478055</td>	<td>	0.489669</td></tr>
<tr><td>2	</td><td>XGBoost without hyperparam tuning	</td>	<td>0.345568</td>	<td>	0.357054</td></tr>
</table>

#### Here above in after hyperparameter tuning log loss of XGBoost is 9.992007221626413e-16 at value of larning rate 0.1, max depth 1 and n_estimators 1. 
