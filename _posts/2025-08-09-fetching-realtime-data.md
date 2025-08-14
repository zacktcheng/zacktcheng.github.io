# Fetching Realtime Data

The main goal is to build an application that recommends products to a user by combining several concepts: chat completions, embeddings, and function calling; however,
A standard chatbot would not have access to a business's specific product inventory. A real-time product recommendation requires a way to query an internal "database" of product information.
One solution can be using a vector store to find a relevant product and then using function calling to retrieve that product's ID.

## Core Concepts
- Vector Store as a "Database": Instead of a traditional database, an array of documents is used, with each document describing a product (e.g., "Adidas running shoes suitable for short and long distances").
- Metadata: Each document is given metadata, such as a productId, which acts as a unique identifier for the product. This links the descriptive text to the actual product record.
- Similarity Search: When a user asks for a product (e.g., "I'm looking for a pair of running shoes"), the query is used to perform a similarity search against the vector store.
  - This search finds the document whose text description is most similar to the user's query.
  - The similarity_top_k parameter is set to 1 to retrieve only the single most relevant product.
- Function Calling: The next step, which will be covered in the following lecture, is to dynamically call a function (e.g., getProductId) that performs this similarity search and returns the found productId to the user.

## Step-by-step instructions
1. Define a functions object:
   - Create a JavaScript object that maps function names to actual executable functions.
   - In this case, there's a recommendProduct function that accepts a description argument.
2. Describe the function to OpenAI:
   - In the API call, add a functions array that describes the recommendProduct function in JSON Schema format.
   - This schema tells the AI about the function's name, purpose ("returns a recommended product"), and parameters (description as a string).
3. Handle the AI's response:
   - When the user asks for a product (e.g., "I'm looking for a pair of running shoes"), the AI doesn't respond with text. Instead, it returns a function call object.
   - This object specifies the function to call ("recommendProduct") and extracts the relevant arguments from the user's message ("running shoes").
4. Execute the function:
   - Your application code receives the AI's function call object.
   - It then finds the corresponding function in your functions object (functions.recommendProduct).
   - The code executes this function, passing the arguments provided by the AI.
5. Retrieve and return the product ID:
   - The recommendProduct function in this example calls the getProductId function (from the previous lecture).
   - `getProductId` uses a vector store to perform a similarity search on the product descriptions and returns a unique productId.
6. Enrich the response:
   - With the `productId` in hand, your application can now perform other actions, such as fetching full product details from a traditional database or getting a product image.
   - Finally, it can construct a rich response for the user, such as a URL for the specific product.

My script example:
```
import { AzureOpenAIEmbedding } from '@llamaindex/azure';
import dotenv from 'dotenv';
dotenv.config();
import { Document, Settings, VectorStoreIndex } from 'llamaindex';
import {
  endpoint,
  apiKey,
  embedding,
  embeddingApiVersion
} from '../../clientConfig';

Settings.embedModel = new AzureOpenAIEmbedding({
  endpoint,
  model: embedding,
  apiKey,
  apiVersion: embeddingApiVersion
});

const documents = [
  new Document({
    text: "Nike skateboarding shoes, suitable for all agse and all boards",
    metadata: {
      productId: 1,
    },
  }),
  new Document({
    text: "Adidas running shoes, suitable for short and long distances, as well as marathons",
    metadata: {
      productId: 2,
    },
  }),
  new Document({
    text: "Hiking shoes, suitable for dry weather",
    metadata: {
      productId: 3,
    },
  }),
  new Document({
    text: "Hiking shoes, suitable for wet weather",
    metadata: {
      productId: 4,
    },
  }),
];

const index = await VectorStoreIndex.fromDocuments(documents);
const retriever = index.asRetriever({ similarityTopK: 1 });

export async function getProductId (query: string) {
  console.log("Searching for product id", query);
  const matchingNodes = await retriever.retrieve(query);
  const found = matchingNodes[0];
  return found.node.metadata.productId;
}
```
