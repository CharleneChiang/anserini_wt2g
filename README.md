# Anserini_wt2g

This is a project implementing four different retrieval methods based on using Anserini.

Anserini, a powerful information retrieval toolkit, provides indexing, retrieving, and evaluating.

By using WT2g data collection to construct a index with stemming and another one without stemming,

then run 50 TREC queries against the collection with four different retrieval methods.

# How to index

- Index with stemming

```
nohup sh target/appassembler/bin/IndexCollection -collection TrecwebCollection -input {path/to/wt2g/*.txt} -index wt2g.stemmed.index -generator DefaultLuceneDocumentGenerator -threads 16 \
 -storePositions -storeDocvectors -storeRaw >& log.wt2g.stemmed &

```

`wt2g.stemmed` is the directory path where you want to save the stemmed index.

- Index without stemming

```
nohup sh target/appassembler/bin/IndexCollection -collection TrecwebCollection -input {path/to/wt2g/*.txt} -index wt2g.unstemmed.index -stemmer none -generator DefaultLuceneDocumentGenerator -threads 16 \
-storePositions -storeDocvectors -storeRaw >& log.wt2g.unstemmed &

```

`wt2g.unstemmed` is the directory path where you want to save the unstemmed index.

# How to retrieve

- Retrieve command -- BM25

```
nohup target/appassembler/bin/SearchCollection -index {path/to/wt2g/stemmed/index} \
 -topicreader Trec -topics {path/to/query}\
 -bm25 -bm25.k1 2 -bm25.b 0.75 -output VSM_retrieve_stemmed.txt &
```

{path/to/wt2g/stemmed/index} should be `wt2g.stemmed.index` according to the sample above.

```
nohup target/appassembler/bin/SearchCollection -index {path/to/wt2g/unstemmed/index} \
 -topicreader Trec -topics {path/to/query} \
 -bm25 -bm25.k1 2 -bm25.b 0.75 -output VSM_retrieve_unstemmed.txt &
```

{path/to/wt2g/unstemmed/index} should be `wt2g.unstemmed.index` according to the sample above.

- Retrieve command -- Dirichlet smoothing

```
nohup target/appassembler/bin/SearchCollection -index {path/to/wt2g/stemmed/index} \
 -topicreader Trec -topics {path/to/query} \
 -qld -output qld_retrieve_stemmed.txt &
```

```
nohup target/appassembler/bin/SearchCollection -index {path/to/wt2g/unstemmed/index} \
 -topicreader Trec -topics {path/to/query} \
 -qld -output qld_retrieve_unstemmed.txt &
```

- Retrieve command -- Jelinek-Mercer smoothing

```
nohup target/appassembler/bin/SearchCollection -index {path/to/wt2g/stemmed/index} \
 -topicreader Trec -topics {path/to/query} \
 -qljm -qljm.lambda 0.2 -output qljm_retrieve_stemmed.txt &
```

```
nohup target/appassembler/bin/SearchCollection -index {path/to/wt2g/unstemmed/index}  \
 -topicreader Trec -topics {path/to/query} \
 -qljm -qljm.lambda 0.2 -output qljm_retrieve_unstemmed.txt &
```

- Retrieve command -- BM25+RM3

```
nohup target/appassembler/bin/SearchCollection -index {path/to/wt2g/stemmed/index}  \
 -topicreader Trec -topics {path/to/query} \
 -bm25 -rm3 -bm25.k1 2 -bm25.b 0.75 -rm3.fbTerms  95 -rm3.fbDocs 10 -rm3.originalQueryWeight 0.20 -output VSM_RM3_retrieve_stemmed.txt &
```

```
nohup target/appassembler/bin/SearchCollection -index {path/to/wt2g/unstemmed/index} \
 -topicreader Trec -topics{path/to/query} \
 -bm25 -rm3 -bm25.k1 2 -bm25.b 0.75 -rm3.fbTerms  95 -rm3.fbDocs 10 -rm3.originalQueryWeight 0.20 -output VSM_RM3_retrieve_unstemmed.txt &
```

# How to evaluate

- trec_eval

```
tools/eval/trec_eval.9.0.4/trec_eval  src/main/resources/topics-and-qrels/qrels.adhoc.401-450.txt VSM_retrieve_unstemmed.txt > bm25_unstemmed.txt
tools/eval/trec_eval.9.0.4/trec_eval  src/main/resources/topics-and-qrels/qrels.adhoc.401-450.txt VSM_retrieve_stemmed.txt > bm25_stemmed.txt
tools/eval/trec_eval.9.0.4/trec_eval  src/main/resources/topics-and-qrels/qrels.adhoc.401-450.txt qld_retrieve_unstemmed.txt > qld_unstemmed.txt
tools/eval/trec_eval.9.0.4/trec_eval  src/main/resources/topics-and-qrels/qrels.adhoc.401-450.txt qld_retrieve_stemmed.txt > qld_stemmed.txt
tools/eval/trec_eval.9.0.4/trec_eval  src/main/resources/topics-and-qrels/qrels.adhoc.401-450.txt qljm_retrieve_unstemmed.txt > qljm_unstemmed.txt
tools/eval/trec_eval.9.0.4/trec_eval  src/main/resources/topics-and-qrels/qrels.adhoc.401-450.txt qljm_retrieve_stemmed.txt > qljm_stemmed.txt
tools/eval/trec_eval.9.0.4/trec_eval  src/main/resources/topics-and-qrels/qrels.adhoc.401-450.txt VSM_RM3_retrieve_unstemmed.txt > VSM_RM3_unstemmed.txt
tools/eval/trec_eval.9.0.4/trec_eval  src/main/resources/topics-and-qrels/qrels.adhoc.401-450.txt VSM_RM3_retrieve_stemmed.txt > VSM_RM3_stemmed.txt
```

# Files

- pooled.txt

The combination of all evaluated files
