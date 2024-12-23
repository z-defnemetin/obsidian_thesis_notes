#### Current situation:
RAG uses cosine similarities to search for the semantically related parts of the document to the query -it is the assumption that the most similar chunks are the most related ones. This makes the model more fallible for nuances in language and increases the possibility of the model retrieving irrelevant or opposing information. The fact that most embedding algorithms don't have domain-specific info introduces more errors.

Basic chunking and similarity search strategies work well with singular resources (a book or article) but suffer when info from multiple documents need to be combined. This could lead to the retrieval of wrong or outdated info. Additionally, traditional retrieval algorithms don't do well with higher level question answering, since they just do a similarity search based on the query.  "The query itself might not have all the information needed to indicate to the model where the most relevant chunk is, but rather additional information and logical steps would be required."

**Retriever Type**: Categorised into Sparse and Dense methods, based on the method used for the information encoding. Sparse retrieval is word-based and mostly used in text retrieval. On the other hand, dense retrieval embeds the query and external documents into vector spaces and can be applied to a variety of data formats. Sparse vectors cannot be trained, therefore the retrieval quality heavily depends on the quality of the database and query. It is also mostly only suitable for similarity-based retrieval, and has limited adaptability for other retrieval criteria such as diversity.
Dense retrieval embeds the query and documents into a continuous vector space with certain criteria, and it is trainable -hence has more flexibility and scalability. A common method is to use a double BERT structure, one for the query and one for the documents. Early-stage RAG methods tend to (partially) freeze the parameters of the retriever to perform general-level relevant knowledge extraction and focus more on the generator fine-tuning and knowledge leveraging. 
On the other hand, for more knowledge-intensive tasks, Dense Passage Retriever (DPR) method is used, which employs two BERT-based models to encode the query and documents. Through fine-tuning, the alignment of the embeddings between queries and relevant documents could be enhanced.

Retriever Granularity: determines the retrieval unit  in which the documents/corpus is indexed. It could significantly impact the overall performance of the model in terms of computational costs, efficiency, effectiveness and storing costs. Early RAG models retrieve whole pieces of documents, and then apply a text comprehension model to detect the answer spans in the returned documents. In generative models, chunk retrieval is used to retrieve only passages of documents. Token retrieval can also be done with faster searching, but is costly in terms of the database saving. Text Chunk method is the mainstream for its lack of redundancy and irrelevancy. Another thing is Entity Retrieval, which represents knowledge with entity memory.

#### Techniques to Improve RAG:

**Chunking Techniques:**
Important since it determines the nature of the context that'll be returned. Bad strategies lead to incomplete context, or too much irrelevant and unneeded information.
- Recursive: Adaptable chunking strategy that uses punctuation (and other semantic/syntactic/grammatical rules) to dynamically adjust chunking strategy/size.
- Element-based: used for large documents like financial reports. Preserves the context of the article/document. If a title or table is found, starts a new chunk, preserving the completeness.

**Pre/Post-Retrieval Enhancement:**

Query Expansion:
Change the original query to retrieve more relevant information from the documents. Queries don't usually contain all information necessary to locate the relevant bit, and could sometimes even be misleading. Query2doc approach generates pseudo-documents by few-shot prompting LLMs and expands the query with the relevant information in pseudo-documents to improve query disambiguity.
- HyDE (Hypothetical Document Embeddings): uses an LLM to generate an answer (based on eg. ChatGPT), and does the similarity search with both the original query and the generated text. This removes the need to use custom embedding algorithms but might lead to incorrect results since it's dependent on an LLM.

Metadata Annotations and Indexing:
Chatting with multiple documents require the use of separate vector databases so that the RAG pipeline doesn't confuse the chunks. Metadata annotations add extra context.

Re-Ranking Algorithms:
Standard RAG pipelines return the most similar k results when queried, however similarity doesn't imply relevance. Re-ranking algorithms prioritise relevance over similarity, and re-rank the top k most similar chunks to base the returned answer on.

Fine-tuning the Embedding Algorithms:
Embedding algorithms can be fine-tuned to a specific knowledge domain to include domain-specific embedding that enhance retrieval. OpenAI's embeddings are dynamic, which means that it is based on the context, and change. It is mentioned that at the time of publishing (2022) this model has the highest score in the BM25 benchmark when finetuned.

[Multilingual Embeddings](https://www.semanticscholar.org/reader/35c2340aacb51fb9283c59bb916c860f77bc68b6):
This research that focused on exploring the most fitting semantic embedding models for Arabic language showed that Microsoft's E5 model, though operating with 1024-dimensional vector embeddings, it performed better than the Ada model of OpenAI.

RAG 2.0:
Combines several advanced retrieval mechanisms:
- Embeddings: Traditional RAG systems use static embeddings, RAG 2.0 uses contextual embeddings that are dynamically generated based on the current query, input documents and conversation history. This leads to a more context-aware retrieval of information.
- Multi-Query Retrieval:
- Iterative Retrieval: system performs multiple rounds of retrieval, basically like Adaptive Boosting. Allows for better multi-step reasoning tasks.
- Adaptive Retrieval: The system adapts its retrieval strategy based on the task and query.
- Hierarchical Retrieval: This is good for handling large-scale knowledge bases -the system first identifies broad relevant topics and then drills down to specific details.
- Cross-Document Retrieval: System can retrieve and synthesise information from multiple sources.
- Relevance Feedback (?) (Linkedin Link): 
	- Re-Ranking (previously mentioned)
	- [FLARE](https://blog.lancedb.com/better-rag-with-active-retrieval-augmented-generation-flare-3b66646e2a9f/): Dynamically queries the knowledge base (or the internet) when the confidence level of a generated segment falls below a specified threshold.
	- [HyDE](https://blog.lancedb.com/advanced-rag-precise-zero-shot-dense-retrieval-with-hyde-0946c54dfdcb/) (previously mentioned)
- Dynamic Knowledge Integration: Dynamically integrates new information to its knowledge base, doesn't require full training for an update (take that fine-tuning).