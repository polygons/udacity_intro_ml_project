# udacity_intro_ml_project

## Answers to Project Questions

1) The goal of this project was to attempt to classify _persons of interest_ (POIs) using financial data and email-frequency counts collected beforehand by Katie (and perhaps other staff) at _Udacity_. Outliers were present in the data (calculated as any feature values below 0.5% or above 99.5% percentiles per feature), and their removal prior to training classifiers changed the vertical shape of the data by 7% (i.e., 10 examples were removed along with their corresponding labels; none of these removals was labeled as a POI). After removing outliers, all missing values (encoded as 'NaN's) were imputed with their respective columnar medians. 

2) Prior to training classifiers, **SelectKBest** was implemented to score each feature according to its apparent importance. _exercised_stock_options_ and _total_stock_value_ were the highest-scoring features (25.87 and 22.17, respectively). The next two highest-scoring features, _bonus_ and _salary_ (14.86 and 11.39), were multiplied together to create a hand-engineered feature.

Different preprocessors and classifiers were tested using a control loop. For preprocessing, a **Pipeline** was implemented wherein all features were scaled using **StandardScaler**. After scaling, three different additional preprocessing techniques were attempted to perform either dimensionality reduction and orthogonal transformation on the available features and/or to omit any apparently superfluous features: a) **RandomizedPCA** in isolation, b) **SelectKBest** in isolation, and c) the **FeatureUnion** of **RandomizedPCA** and **SelectKBest**.

3) The classifiers attempted were: **GaussianNB**, **DecisionTreeClassifier**, and **SVC**. Regardless of how the parameters were tuned via **GridSearchCV** (see below), the latter classifier typically failed to classify anyone in the final testing set as a POI, except when **RandomizedPCA** was used in isolation during preprocessing (at which point only 1 POI was correctly identified).

4) Most algorithms have parameters that should be tuned in order to modify their training performance, which is in part dependent on the nature of the data set under consideration.

For both preprocessing and classification, **GridSearchCV** was implemented to tune various parameters for each preprocessor and classifier (where applicable) as **GridSearchCV** attempts to find the best parameters. As a few examples of the parameters tuned (as there were many), the number of components for **RandomizedPCA**, the $k$ value for **SelectKBest**, the number of features to consider at split locations for **DecisionTreeClassifier**, and the penalty parameter for **SVC**.

5) Breaking the data into training and testing sets is a component of a statistical assessment method called _cross-validation_, which is an effort to estimate how well a model will perform/generalize when it encounters _new_ data. To this end in the current context, data sets used for training machine-learning models are broken up into separate _training_ and _testing_ sets. After models are trained with the training data alone, models are then evaluated using only the testing data.

As the current data set under consideration includes only 146 records (only 18 of which are identified as POIs), **StratifiedShuffleSplit** was used via **GridSearchCV** during the initial preprocessor and classifier evaluation phase since it allows for the possiblity of training and testing on all of the data available (i.e., as opposed to training only on a single training set and testing only on a single testing set), averaging the test results of each learning session in order to offer a clearer picture of model performance.

An important additional note: if the data separation process described above is not randomized, it is possible to partition the data in such a way that the training and testing sets do not both properly represent the data in a balanced manner (e.g., when experimenting on the Enron data set, it would be possible to create a training set containing only non-POI-related data and a testing set with only POI-related data). 

6) Given the distribution of the final testing set, in particular the small number of POIs, and likely in part due to the binary nature of this specific classification problem, the generic score $\big(\frac{\text{correct predictions}}{\text{total_predictions}}\big)$ for each classifier is easily made higher when no POIs are identified at all (i.e., all predicted labels are $0$). 

Thus, in this case, using a _confusion matrix_ (and the resulting $F_{1}$ score) is a better indicator of model/prediction performance. 

The highest-scoring combination of preprocessors and classifier was the following set: the **FeatureUnion** of **RandomizedPCA** (parameters: copy=True, iterated_power=1, n_components=6, random_state=42, whiten=True) and **SelectKBest** (k=8) and the classifier **GaussianNB** (no parameters).



I'm most pleased with the **DecisionTreeClassifier** (and **RandomizedPCA**) results since, although that clasifier, on the final testing set, incorrectly identified 1 individual as a POI, **DecisionTreeClassifier** was the only classifier out of the four implemented to identify correctly any POI (on final testing set). As a result, the label-class averages of both the _precision_ score $$\big(\frac{\text{true positives}}{\text{true positives} + \text{false positives}}\big)$$ and the _recall_ score $\big(\frac{\text{true positives}}{\text{true positives} + \text{false negatives}}\big)$ were quite high.


Preprocessing via StandardScaler and the feature union of PCA (keeping 6 components) and SelectKBest ($k = 8$) followed by training a GaussianNB classifier provided the highest F1 score of 0.84.


type out the values for each classifier

use different words to describe precision and recall

---

_I hereby confirm that this submission is my work. I have cited above the origins of any parts of the submission that were taken from Websites, books, forums, blog posts, github repositories, etc._

Aside from standard usage of scikit-learn and Python documentation/examples, none of the work above is dependent on any particular source and, thus, the work is my own.

---

## Epilogue: email text as data

I had hoped to use email-text data along with the financial data, but clearly there is a discrepancy between the inidividuals represented by the financial data and the email data.

Unfortunately, it seems clear that it would be difficult to join the two data sets in a meaningful way due to their lack of overlap. 

After this project, I may spend some time with text-vectorization of the email corpus and examine things like word frequencies (I have started working on some of the initial code for that process below); however, with such an apparent low number of known POI-emails being available (only 3!?), it's entirely unclear how useful such an endeavor would be for identifying POIs, although there are surely other interesting insights to be gleaned.
