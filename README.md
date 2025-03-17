# RAG pipeline: Grimms' Fairy Tales

This repository contains a **Retrieval-Augmented Generation (RAG) pipeline** built using **BeautifulSoup (bs4)** for information extraction, the **mxbai-embed-large-v1** embedding model, **FAISS** for vector storage, **LangChain** for simplifying NLP tasks, and the **Falcon 3B** model for text generation. The pipeline leverages **cosine similarity** to select relevant documents for context-aware text generation. The main use case is question-answering, where a query is answered based on retrieved information from a set of documents.

# How It Works
## Step 1: Web Scraping with BeautifulSoup
During text selection it was important to me to use widely known and free to use text. Grimms' fairy tales meet both criteria. The text is available online in HTML format thanks to the _**Gutenberg project**_. 
I used _**BeautifulSoup**_ to fetch HTML content from the URL and extract some chapters based on their HTML structure.

## Step 2: Document Embedding with mxbai-embed-large-v1
I split the text into smaller chunks to increase the retrieval efficiency. Then I used the _**mxbai-embed-large-v1**_ model from _**Hugging Face**_ to generate embeddings. These embeddings represent the semantic meaning of the text.

## Step 3: Document Retrieval using FAISS
In order to make the retrieval process more efficient and to avoid regenerating embedding vectors for mutiple user queries, I stored the embeddings in a FAISS vector database. During the retrieval process _**LangChain**_ the most relevant documents are retrieved based on cosine similarity. The cosine similarity metric allows us to select the documents that are semantically most similar to the user’s query.

## Step 4: Text Generation with Falcon 3B
The _**Falcon 3B**_ generative model from Hunnging Face is used to generate coherent and contextually relevant answers based on the retrieved documents. When loading the generative model, I used the bfloat16 data type to reduce memory usage and accelerate computation, while still preserving the accuracy of the generative model. If we need to answer a question based on information found in a text, it is important that the text generation is deterministic, predictable with minimal creativity or randomness. Therefore, I set the temperature value to 0.0. During text generation the most probable token selection (num_beams=1) and avoiding repetitions with the no_repeat_ngram_size=1 setting proved to be the best. 

## Step 5: Generating Answers
The retrieved context is provided as input to the _**Falcon 3B**_ model, which generates the answer to the user’s query, utilizing the context fetched from the document retrieval step. For each user query, I specified separately how many tokens the generated response should consist of. 
