 How should the data be structured? Q&A format - every model has its own detailed requirements about labeling.
 
 How much data is required? about 1000 examples for starters.

Best performance: 1000 examples of Q&A pairs. The training data could be crafted in a 'text-generation' style. This includes the compiling of a dictionary of question-answer pairs labeled as 'Human' and 'Assistant'. This dataset could be generated through the utilisation of OpenAI's language models that simulate a dialogue with the GPT model, which generates conversational data from the document text. This methodology transfors the static text into a dynamic conversational Q&A format.

---------
**Meeting 25.11.2024 - Hongchao, Johan and Edwin**

What sorts of documentation will be available? Is confidentiality going to be a problem?
ascii doc - can transform into pdf or html similar to md
about do
azuredevops
manual qa pairs available

Are there specific topics, departments, or services that should be prioritized for the chatbot?
developmental systems clusters (?)

Are there specific metrics that you want to focus on? In the last meeting you had mentioned tone and answer quality, should we proceed with that?
counter hallucination (!)

How often do the documents get updated, or new ones get added? (this is important for the scalability of the model)

Do you (or the primary users of ChatNS) have any specific complaints that this project could focus on improving, or should the project just go about with fine-tuning (and possibly a combination of RAG and fine-tuning)?

Rag improvement is more relevant
retrieved results arent always relevant
use of different buckets for different topics: more general (centralised solution?)
metadata that descripbes the aim of the bucket
automate bucket selection process
twocalling (?) availebl
add document possible