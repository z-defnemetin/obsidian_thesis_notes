Hard to evaluate RAG systems because: 
1. modular complexity: A RAG system is made up of 
2. metric limitation
3. metric reliability (2408 archive paper)

RAG System as a Whole: The full performance of the system cannot be determined by evaluating the individual parts of the system in isolation.

Retrieval: Evaluable output is the relevant documents for the query. 
- Relevance: Evaluates how well the retrieved documents match the information that is required by the query. Measures the precision and specificity of the retrieval process.
- Accuracy: Assesses how accurate the retrieved documents are, in comparison to a set of candidate documents. It measures the system's ability to identify the relevant documents and score them.

Generation: 
- Relevance: Measures how well the generated response matches the intent and content of the initial query.
- Faithfulness: Evaluates if the generated response accurately reflects the information contained within the relevant documents.
- Correctness: Evaluates the accuracy of the response in comparison to a sample response, which serves as a ground truth. Is the response correct in terms of factual information, and is it appropriate in the context of the query?
#### Evaluation Metric for: Retrieval
(it's mostly just precision and recall)
DeepEval offers the following evaluation metrics for testing of retrieval, with the use of a self-explaining LLM-eval that outputs a score and a reason:
* Contextual Precision: This metric evaluates the re-ranker. This metric tests the retriever by checking whether the nodes in the retrieval context (the context that is retrieved from external documents) that are relevant to the input (query) are ranked higher than irrelevant ones. This metric uses weighed cumulative precision to calculate the precision score, which means that the top-ranked results have a stronger impact on the final score. It also rewards relevant ordering, and can handle varying degrees of variance -in contrast to precision, which is binary. It is calculated by the following formula:
$Contextual Precision = \frac{1}{Number of Relevant Nodes} ​\sum^{n}_{k=1}​(\frac{Number of Relevant Nodes Up to Position k}{k}​×r_{k})$
	- $r_k$ is the binary relevance for the $k^{th}$ node
	This metric requires input, actual_output, expected_output, retrieval_context
* Contextual Recall: This metric tests whether the embedding model in the retriever can capture relevant information. This metric measures how much the retrieval context (the context retrieved from the external documents) aligns with the expected output (golden answer). Firstly, all statements made in the expected output is extracted, and then the same LLM is used to classify whether each statement can be attributed to the retrieved context. The metric is calculated as the number of attributable statements divided by the total number of statements. A higher score for this metric means that the retrieval system can capture all relevant information from the available relevant set within the knowledge base.
* Contextual Relevancy: This metric tests whether the text chunk size and the top-K of the retriever actually retrieves relevant information. This metric evaluates the overall relevancy of the information given in the retrieved context to the input. The metric accepts input, actual_output and retrieval context as inputs for evaluation. Similar to the AnswerRelevancy, this metric uses an LLM to extract all statements made in the retrieval context, and then uses the same LLM to classify whether each statement is relevant to the input.


#### Evaluation Metric for: Generation 
Check out:
* TruLens/RAG Triad: measured in terms of context relevance, groundedness, answer relevance. Answer Relevance(calculated ) has the highest correlation with human evaluation
* [RAGAs](https://arxiv.org/abs/2309.15217): Can measure faithfulness, answer relevance/similarity/correctness. Answer Similarity (calculated based on the semantic similarity between the generated answer and the ground truth, with the ada text embedding model) and Answer Correctness (calculated by quantifying the semantic similarity and the factual overlap between the response and the ground truth answer) have the highest correlation with human evaluation.
* CRUD-RAG: its a benchmark :(
* SelfCheckGPT (as mentioned in RAGAS paper): checks the variation in the generation of multiple answer for one query. the reasoning is that the factual answers are more stable.

Most important aspects in an evaluation metric, and how they're measured in RAGAs: 
- Faithfulness (i.e. groundedness, avoiding hallucinations): This is calculated by extracting statements (short assertions) from the generated answer, and asking an LLM if it is plausible that the answer was generated given the context. The final faithfulness score is computed as # of supported statements divided by total number of statements,
- Answer Relevance (the generated answer should be related to the given query): the metric penalizes incomplete or redundant information. An LLM is asked to generate potential questions based on the generated answer. For each question, the cosine similarity score of the generated question in comparison to the actual query is computed. This metric evaluates how closely the generated answer aligns with initial question or instruction.
- Context Relevance (the generated content should be relevant to the query, must contain as little irrelevant info as possible): Penalises the inclusion of redundant information. Basically asks an LLM to pick out the sentences in the generated answer that are necessary to answer the query, and divides the # of relevant sentences with total number of sentences in the generated answer.

What about Hallucination?:

Faithfulness: Can be measured with DeepEval's Faithfulness score, which outputs a score and a reasoning. To be used, it requires input, actual output, and retrieval context. It it calculated as the ratio of number of truthful claims divided by total number of claims. A claim is considered truthful if it doesn't contradict any facts presented in the retrieval context. This is basically the same as RAGAs.
DeepEval also checks for Answer Relevancy, which is also already measured by RAGAs.
