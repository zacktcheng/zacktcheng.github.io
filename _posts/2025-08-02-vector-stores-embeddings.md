# Vector Stores and Embeddings

## Table of contents
- [Storing PDF in a vector store](#how-does-traditional-ai-work)
- [Querying the vector store](#querying-the-vector-store)

## Storing PDF in a vector store
We often need to build a chatbot that can answer questions based on a large document (e.g., a 52-page thesis) or a website's data. This information is too large to fit into a large language model's (LLM) token limit. Using vector stores and embeddings involves converting the large text into smaller, manageable chunks, and then turning those chunks into numerical representations (embeddings) that can be easily searched.

### Step-by-Step Implementation
1. Dependencies:
   - `llama-index`: A data framework for ingesting and structuring data for LLM applications.
   - `@cyber2024/pdf-parse-fixed`: A utility to read and parse PDF files.
2. Read the Document:
   - Use Node.js's fs module to read the PDF file into a buffer.
   - Use the pdf-parse package to extract the text content from the PDF buffer.
3. Process with LlamaIndex:
   - Create a `ServiceContext`: This object holds default configurations for LlamaIndex.
   - Create a `Document`: Wrap the extracted text content in a Document object that LlamaIndex can work with.
   - Create a `VectorStoreIndex`: This is the core step. LlamaIndex takes your large document, breaks it down into smaller, equal-sized "chunks" (called nodes), and creates an embedding for each chunk.
   - Store the Index: To avoid recreating the index every time the script runs, use LlamaIndex's StorageContext. This saves the embeddings to a local folder, making subsequent script runs much faster. You provide a directory for persistence.

My working example code:
```
import { AzureOpenAI } from "openai";
import {
  endpoint,
  apiKey,
  apiVersion,
  model,
  embedding,
  embeddingApiVersion
} from './clientConfig';
import { Settings, storageContextFromDefaults, TextNode, VectorStoreIndex } from 'llamaindex';
import { SimpleDirectoryReader } from "@llamaindex/readers/directory";
import { AzureOpenAIEmbedding } from "@llamaindex/azure";

Settings.embedModel = new AzureOpenAIEmbedding({
  endpoint,
  model: embedding,
  apiKey,
  apiVersion: embeddingApiVersion
});
Settings.chunkSize = 4e3; // Rounghly 1000 tokens
Settings.chunkOverlap = 5e2;

const reader = new SimpleDirectoryReader();
const documents = await reader.loadData('./pdf');
const storageContext = await storageContextFromDefaults({
  persistDir: './storage'
});
const index = await VectorStoreIndex.fromDocuments(documents, { storageContext });
const query = 'Which technologies can be used to saolve congestion at airports?';
const retriever = index.asRetriever();
const matchedNodes = await retriever.retrieve(query);
const knowledge = matchedNodes.map(node => {
  const textNode = node.node as TextNode;
  return textNode.text.replace(/\n/g, '');
}).join('\n\n');

const client = new AzureOpenAI({
  endpoint,
  apiKey,
  apiVersion,
});

const response = await client.chat.completions.create({
  model,
  messages: [
    {
      role: 'system',
      content: `You are an aviation expert. Here is your knowledge to answer the user's question: ${knowledge}.`
    },
    {
      role: 'user',
      content: query
    }
  ],
  temperature: 0,
});

console.log(response.choices[0].message)
```
**Note that I didn't use** `ServiceContext`. **This is because it is deprecated in the later version.**

## Querying the vector store

### Chunking Strategy

LlamaIndex breaks large documents into smaller "chunks" (nodes). To optimize this, you can customize the chunking rules in the ServiceContext object.

Chunk Size: The default chunk size can be too large. It's recommended to set it to a smaller size, like 4,000 characters (roughly 1,000 tokens), to ensure that the chunks fit within the LLM's context window.

Chunk Overlap: To prevent context from being cut between chunks, you can set a chunkOverlap value (e.g., 500 characters). This ensures that each new chunk starts with some text from the end of the previous one.

### Retrieve Relevant Chunks

The user's query is also converted into a vector embedding.

A retriever is created from the LlamaIndex index.

The retriever performs a similarity search (using a k-nearest neighbor algorithm) to find the top k most relevant chunks from the vector store based on the user's query. By default, k is 2, meaning it returns the two most similar chunks.


- Code Generation
- Autonomous Agents
