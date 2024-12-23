Fine-Tuning: 
LLMs 
LoRa: a method of Parameter-Efficient Fine-Tuning (PEFT)

Retrieval Augmented Generation:

RAG retrieves information from external documents through semantic similarity calculations, hence reducing the possibility of hallucinations through referencing external knowledge. 

Pre-trained models such as ChatGPT rely only on the pre-training data for information, and lack the knowledge of correct recent events. 

Naive RAG: follows the process of 'indexing', 'retrieval' and 'generation'.
* Indexing: extract and clean raw data from various formats. Segment the text into smaller chunks, and encode into vector representations through an embedding model, and stored in database. 
* Retrieval: Transform the query into a vector representation through the same embedding model. Compute the similarity scores between the indexed chunks and the query, and return the top K chunks that have the greatest similarity. 
* Generation: an LLM is used to synthesise the selected documents into a coherent response.
Naive RAG challenges: 
* struggles with precision and recall, could select misaligned or irrelevant chunks or miss crucial info. 
* still struggles with hallucination due to the non-deterministic use of LLMs.
* might produce redundant answers, or 'echo back' the answers from documents without additional insights or synthesised info

Advanced RAG: improves on the Naive RAG by using sliding windows for indexing, incorporation of metadata. Uses optimisation methods to streamline the retrieval process. Uses pre- and post-retrieval processes:
* Pre-retrieval Process: optimize the indexing structure and the original query. Query optimisation aims to make the question clearer and more suitable for the retrieval task through: rewriting, transformation, expansion, etc.
* Post-retrieval Process: Improve the integration of relevant context with the query through shortening the context, emphasising critical sections and select essential information. Re-ranking chunks to re-locate the most relevant content to the edges of the prompt, and context compressing to prevent the dilution of key information with irrelevant context gathered from many documents. 

Modular RAG: introduces specialised components to enhance retrieval.
* 'Search' module adapts to specific scenarios, and enables users to search across different data sources (including search engines).
* 'Fusion' expands queries into different perspectives, utilising  parallel vector searches to gather both explicit and transformative knowledge.
* Memory module: guides retrieval through RAG's memory, selects the optimal pathway for a query.
* Predict module: reduces redundancy and noise by generating context through the LLM, ensuring relevance and accuracy.
* Task Adapter: 

RAG vs Fine-Tuning: RAG is sometimes compared with Fine Tuning (FT) and prompt engineering. 
RAG is useful in dynamic environments where the knowledge can update frequently, but comes with higher latency and ethical considerations (?). FT is more static, requires retraining for updates but enables customisation of the output behaviour and style. While they are competing techniques, their use doesn't have to be mutually exclusive. They can be combined for optimal performance, though it requires a bit more training time. 

RETRIEVAL:
Data Structure:
Unstructured Data: such as text.
Semi-structured Data: data that contains a combination of text and table info.
Structured Data: Knowledge graphs, are verified and can provide more precise information. G-retriever integrates Graph NNs with LLMs and RAG to enhance the graph comprehension of the model.

B - Indexing Optimisation: the documents are processed, segmented and tranformed into embeddings to be stored at the database. The quality of the index construction is crucial for obtaining the correct context from the database. 
* Chunking Strategy: Bigger chunks can capture more context, but they generate more noise -smaller chunks have the opposite problem. Chunks lead to truncation of sentences and splitting of context. Use of sliding window methods and recursive splits improve retrieval process by merging globally related information through different processes. Methods like Small2Big use the sentence as the retrieval unit, and the previous and afterwards sentences are used as context.
* Metadata Attachments: chunks can be enriched with metadata information such as page number, file name, author, etc. for filtering and limiting the scope. Summaries and hypothetical questions could be introduced to to artificially construct metadata.

C - Query Optimisation: Improves the direct-reliance of Naive RAG on the given query.
Some techniques to get around this are: Query Expansion (generate multiple queries to address the problem of lack of specific nuances), Multi-Query (expand queries via LLMs via prompt engineering)

D - Embedding:
E - Adapter