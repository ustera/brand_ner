# Brand recognition for Russian

Igor Dyatlov, Ekaterina Zalivina, Ekaterina Sannikova, Polina Kozlova

## Description

In the context of Named Entity Recognition tasks, brand recognition refers to the ability of a model to identify and extract mentions of specific brand names within text data. This capability is often critical in applications such as social media monitoring, brand sentiment analysis, and market research.

## Data

There are no datasets in Russian for this task at the moment. In order to form the dataset, we took Ozon's analytical data, which was collected to determine which products users added to Favorites most often. From the dataset, we took the pairs "product name - brand" and cleared the dataset from duplicate positions and converted it to the IOB2 format.

In order to understand how well brand recognition fits into the context of recognition of the main entities, the Nerus dataset was taken. To avoid a strong predominance of the main entities, only a part was taken from the dataset. As a result, we got a dataset that contains about 80 thousand entries of each entity: LOC, ORG, PER and BRAND.

## Approaches

Detecting only the entity BRAND

1. Spacy - Baseline
2. CRF
3. XML Roberta
4. Bert uncased, Bert-Ner

For the two best models we decided to check the results that will be for core entities + brand

1. CRF
2. XML Roberta

SpaCy provides a convenient framework for creating one’s own NER model from the ground up, which we took advantage of. We trained a NER brand classifier on our data using gensim’s Word2Vec custom embeddings trained on our title corpus. It should be noted that only words with >=2 instances were vectorized, and our data preprocessing was minimal, as we believe it is important to preserve capitalization in titles for better inference, so a lot of the wordforms were not initialized with vectors. For training NER modules, spaCy uses a series of Stack LSTMs. 

We decided to compare our results with a CRF (Conditional Random Field) model. We took it because it is one of the most popular and effective ways to get named entities from texts. The results were pretty interesting. It seems that (at least on our training data) CRF does a better job at NER with brands than neural networks. We believe that is because our dataset was not big enough for high quality neural network training. Our guess is that (as our dataset will become bigger and better) neural networks will eventually surpass CRF model in brand recognition.

We took Transformers to compare results with previous approaches. In the first case it is bert-uncased, in the second it is bert-ner, which is already pre-trained for the NER task on the CoNLL-2003 Named Entity Recognition dataset. As part of our task, it was necessary to obtain a model for brand recognition. Both models performed lower than CRF and XLM-Roberta. The XML-Roberta has been pre-trained on a large amount of text data, including text in various domains and languages. This training allows it to recognize patterns and relationships in language, making it capable of identifying core entities and also brands (with the help of fine-tuning).

## Results

**Only brands**
|  | Precision | Recall | F1 |
| --- | --- | --- | --- |
| CRF | 0.984 | 0.985 | 0.984 |
| XLM Roberta | 0.965 | 0.966 | 0.966 |
| bert-base-NER | 0.957 | 0.961 | 0.959 |
| bert-base-uncased | 0.954 | 0.957 | 0.955 |
| Spacy - baseline | 0.940 | 0.950 | 0.946 |

**Brands + core entities**
|  | Precision | Recall | F1 |
| --- | --- | --- | --- |
| CRF (unweighted entities) | 0.980 | 0.980 | 0.980 |
| CRF (weighted entities) | 0.978 | 0.978 | 0.978 |
| XLM Roberta (weighted entities)  | 0.967 | 0.969 | 0.968 |
