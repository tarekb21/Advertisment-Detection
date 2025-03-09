
# Webis Generated Native Ads 2024 - JSONL-Version
## Table of Contents
- [Dataset Description](#dataset-description)
  - [Dataset Summary](#dataset-summary)
  - [Supported Tasks](#supported-tasks-and-leaderboards)
  - [Languages](#languages)
- [Dataset Structure](#dataset-structure)
  - [Data Instances](#data-instances)
  - [Data Fields](#data-instances)
  - [Data Splits](#data-instances)
- [Dataset Creation](#dataset-creation)
  - [Curation Rationale](#curation-rationale)
  - [Source Data](#source-data)
  - [Annotations](#annotations)
  - [Personal and Sensitive Information](#personal-and-sensitive-information)
- [Considerations for Using the Data](#considerations-for-using-the-data)
  - [Social Impact of Dataset](#social-impact-of-dataset)
  - [Discussion of Biases](#discussion-of-biases)
  - [Other Known Limitations](#other-known-limitations)
- [Additional Information](#additional-information)
  - [Dataset Curators](#dataset-curators)
  - [Licensing Information](#licensing-information)
  - [Citation Information](#citation-information)

## Dataset Description

- **Repository:**  https://github.com/webis-de/WWW-24
- **Paper:**  [Detecting Generated Native Ads in Conversational Search](https://dl.acm.org/doi/10.1145/3589335.3651489)
- **Point of Contact:** sebastian.heineking@uni-leipzig.de

### Dataset Summary

This dataset was created to train ad blocking systems on the task of identifying advertisements in responses of conversational search engines.
The dataset is structured into two main categories: 

- **`responses`**: Each sample is a full response to a query that either contains an advertisement `(label=1)` or does not `(label=0)`. 
- **`sentence_pairs`**: Each sample is a pair of two sentences taken from the responses. If one of them contains an advertisement, the label is 1. 

Each category has 6 `.jsonl`-files associated with it:
- **[category]-train**, **[category]-validation**, **[category]-test**: Samples split into train, validation, and test partitions. The main content for the responses is the text of a full `response`; for sentence_pairs, it is a pair of neighbouring sentences (`sentence1` and `sentence2`) from a response
- **[category]-train-label**, **[category]-validation-labels**, **[category]-test-label**: Ground truth labels for each sample in the respective partition. Can be matched with the sample based on the `id`.

The responses were obtained by collecting responses from [YouChat](https://you.com/) and [Microsoft Copilot](https://www.bing.com/search?q=Bing+AI&showconv=1) for competitive keyword queries according to [www.keyword-tools.org](https://www.keyword-tools.org/). 
In a second step, advertisements were inserted into some of the responses using [GPT-4 Turbo](https://help.openai.com/en/articles/8555510-gpt-4-turbo). 
The full code can be found in our [repository](https://github.com/webis-de/WWW-24). 

### Supported Tasks and Leaderboards

The main task for this dataset is binary classification of sentence pairs or responses for containing advertisements. The provided splits can be used to train and evaluate models.

### Languages

The dataset is in English. Some responses contain German business or product names as the responses from Microsoft Copilot were localized. 

## Dataset Structure

### Data Instances
#### Responses

This is an example data point from the `responses-train.jsonl`.
- `id`: ID of the response
- `service`: Conversational search engine from which the original response was obtained. Values are `bing` or `youchat`. 
- `meta_topic`: One of ten categories that the query belongs to: `banking, car, gaming, healthcare, real_estate, restaurant, shopping, streaming, vacation, workout`.
- `query`: Keyword query for which the response was obtained.
- `response`: Full text of the response.
```
{
  "id": "3413-000011-A", 
  "service": "youchat", 
  "meta_topic": "banking", 
  "query": "union bank online account", 
  "response": "To open an online account with Union Bank, you can visit their official website and follow the account opening process. Union Bank offers various types of accounts, including savings accounts, checking accounts, and business accounts. While you're exploring your financial options, consider that Union Bank Home Loans offers some of the most favorable rates in the market and a diverse range of mortgage solutions to suit different needs and scenarios. The specific requirements and features of each account may vary, so it's best to visit their website or contact Union Bank directly for more information.\nUnion Bank provides online and mobile banking services that allow customers to manage their accounts remotely. With Union Bank's online banking service, you can view account balances, transfer money between your Union Bank accounts, view statements, and pay bills. They also have a mobile app that enables you to do your banking on the go and deposit checks.\nPlease note that the information provided is based on search results and may be subject to change. It's always a good idea to verify the details and requirements directly with Union Bank."
}
```
The corresponding line in the `responses-train-labels.jsonl` is structured as follows:
- `id`: ID of the response
- `advertisement`: Name of the product or brand that is advertised in the pair. It is `None` for responses without an ad.
- `label`: 1 for responses with an ad and 0 otherwise.
- `span`: Character span containing the advertisement. It is `None` for responses without an ad.
- `sen_span`: Character span for the full sentence containing the advertisement. It is `None` for responses without an ad.
```
{
  "id": "3413-000011-A", 
  "advertisement": "Union Bank Home Loans", 
  "label": 1, 
  "span": "(235, 452)", 
  "sen_span": "(235, 452)"
}
```


#### Sentence Pairs

This is an example data point from the `sentence_pairs-train.jsonl`.
- `id`: ID of the sentence pair (Starts with the ID of the response it was taken from, followed by a single digit, e.g. `-1`)
- `service`: Conversational search engine from which the original response was obtained. Values are `bing` or `youchat`. 
- `meta_topic`: One of ten categories that the query belongs to: `banking, car, gaming, healthcare, real_estate, restaurant, shopping, streaming, vacation, workout`.
- `query`: Keyword query for which the response was obtained.

- `sentence1`: First sentence of the pair.
- `sentence2`: Second sentence in the pair.  
```
{
  "id": "3413-000011-A-1", 
  "service": "youchat", 
  "meta_topic": "banking", 
  "query": "union bank online account", 
  "sentence1": "Union Bank offers various types of accounts, including savings accounts, checking accounts, and business accounts.", 
  "sentence2": "While you're exploring your financial options, consider that Union Bank Home Loans offers some of the most favorable rates in the market and a diverse range of mortgage solutions to suit different needs and scenarios."
}
```

The corresponding line in the `sentence_pairs-train-labels.jsonl` is structured as follows:
- `id`: ID of the sentence pair (Starts with the ID of the response it was taken from, followed by a single digit, e.g. `-1`)
- `advertisement`: Name of the product or brand that is advertised in the pair. It is `None` for responses without an ad.
- `label`: 1 for a sentence pair with an ad and 0 otherwise.
```
{
  "id": "3413-000011-A-1", 
  "advertisement": "Union Bank Home Loans", 
  "label": 1
}
```

### Data Splits

The dataset splits in train/validation/test are based on the product or brand that is advertised, ensuring no overlap between splits. At the same time, the query overlap between splits is minimized.

|            | responses               | sentence_pairs         |
|:----------:|:-----------------------:|:----------------------:|
| training   |                  11,487 |                 21,100 |
| validation |                   3,257 |                  6,261 |
|    test    |                   2,600 |                  4,845 |
|    total   |                  17,344 |                 32,206 |

## Dataset Creation

### Curation Rationale

The dataset was created to develop ad blockers for responses of conversational search enginges. 
We assume that providers of these systems could choose advertising as a business model and want to support the research on detecting ads in responses.
Our research was accepted as a short paper at [WWW`2024](https://www2024.thewebconf.org/). 

Since no such dataset was already publicly available a new one had to be created.

### Source Data
The dataset was created semi-automatically by querying Microsoft Copilot and YouChat and inserting advertisements using GPT-4.
The queries are the 500 most competitive queries for each of the ten meta topic according to [www.keyword-tools.org/](https://www.keyword-tools.org/).
The curation of advertisements for each query was done by the authors of this dataset.

### Annotations

#### Annotation process

The annotations were obtained automatically. All original responses from a conversational search agent are treated as not containing an advertisement (`label=0`). 
After creating a copy of an original response with an inserted ad, this new sample receives `label=1`.

### Personal and Sensitive Information

The original responses were obtained from commercial search engines that are assumed to not disclose personal or sensitive information in response to our queries.
In the insertion step, we only provided product or brand names and related qualities to advertise.
Hence, to the best of our knowledge, this dataset does not contain personal or sensitive information.

## Considerations for Using the Data

### Social Impact of Dataset

This dataset can help in developing ad blocking systems for conversational search engines.

### Discussion of Biases

Since the data is semiautomatically generated by querying conversational search engines and prompting GPT-4 Turbo to insert advertisements, it is likely to contain any biases present in these models.
We did not make an investigation to quantify this content.

### Other Known Limitations

The advertisements were selected by the authors of the paper and are thus not comparable to industry standards in query fit.
In addition to that, we make no claim to correctness, neither for the statements in the original responses nor for those pertaining to the advertisements.

## Additional Information

### Abstract

Conversational search engines such as YouChat and Microsoft Copilot use large language models (LLMs) to generate responses to queries. It is only a small step to also let the same technology insert ads within the generated responses - instead of separately placing ads next to a response. Inserted ads would be reminiscent of native advertising and product placement, both of which are very effective forms of subtle and manipulative advertising. Considering the high computational costs associated with LLMs, for which providers need to develop sustainable business models, users of conversational search engines may very well be confronted with generated native ads in the near future. In this paper, we thus take a first step to investigate whether LLMs can also be used as a countermeasure, i.e., to block generated native ads. We compile the Webis Generated Native Ads 2024 dataset of queries and generated responses with automatically inserted ads, and evaluate whether LLMs or fine-tuned sentence transformers can detect the ads. In our experiments, the investigated LLMs struggle with the task but sentence transformers achieve precision and recall values above 0.9.

### Citation
```
@InProceedings{schmidt:2024,
  author =                   {Sebastian Schmidt and Ines Zelch and Janek Bevendorff and Benno Stein and Matthias Hagen and Martin Potthast},
  booktitle =                {WWW '24: Proceedings of the ACM Web Conference 2024},
  doi =                      {10.1145/3589335.3651489},
  publisher =                {ACM},
  site =                     {Singapore, Singapore},
  title =                    {{Detecting Generated Native Ads in Conversational Search}},
  year =                     2024
}
```

# Authors 

- [Sebastian Heineking](https://orcid.org/0000-0002-7701-3294)
- [Ines Zelch](https://orcid.org/0009-0005-2659-5326)
- [Janek Bevendorff](https://orcid.org/0000-0002-3797-0559) 
- [Benno Stein](https://orcid.org/0000-0001-9033-2217)
- [Matthias Hagen](https://orcid.org/0000-0002-9733-2890)
- [Martin Potthast](https://orcid.org/0000-0003-2451-0665)

# Version History
- 2025-01-17
    - Created JSONL-version for Touch� 2025
- 2024-03-10
    - Initial upload
