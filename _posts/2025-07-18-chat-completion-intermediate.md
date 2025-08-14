# Chat Completion Intermediate

## Table of contents
- [System messages](#system-messages)
- [Providing knowledge to the LLM](#providing-knowledge-to-the-llm)

## System messages
### System Prompts
System prompts are a way to dictate the behavior of a large language model (LLM) without having to retrain or fine-tune it. They act as a set of guidelines or instructions for the AI to follow during a conversation.
### How LLMs Work
LLMs are trained on massive datasets, which gives them a general understanding of language and conversation patterns. When you give the AI a prompt like "Greet me," its response is based on this pre-existing knowledge from its training data.
### Changing AI Behavior
You can change how an AI responds by using a system message！ This is an alternative to more complex methods like fine-tuning, which involves retraining the model with new data.
Structure of a System Prompt:
- A system prompt is a message object with the role set to system.
- It is placed in the messages array before any user messages.
- The content of the system message contains the instructions for the AI.

My script example:
```
/**
 * A system message can dictate how AI respond to the prompt
 */
import { AzureOpenAI } from "openai";
import { endpoint, apiKey, apiVersion, model } from './clientConfig';

const client = new AzureOpenAI({
  endpoint,
  apiKey,
  apiVersion,
});

const response = await client.chat.completions.create({
  model,
  messages: [
    // {
    //   role: 'system',
    //   content: 'You are a grumpy chat bot who responds in uppercase letters and is very rude.'
    // },
    {
      role: 'system',
      content: 'You are a sophisticated chat bot who responds in morse code.'
    },
    {
      role: 'user',
      content: 'Hello, my name is Kevin, greet me.'
    }
  ],
  temperature: 0,
});

console.log(response.choices[0].message);
```

## Providing knowledge to the LLM
Large Language Models (LLMs) lack real-time information, such as current stock availability for an e-commerce website. The traditional strategies to deal with the problem is through fine-tuning or retraining a model to improve its behavior, but this doesn't solve the problem of real-time data access.

Here comes the system prompts to rescue: You can use a system prompt to provide the LLM with the necessary real-time knowledge for the current conversation. This allows the bot to answer specific questions based on the provided data.

### How to Implement
1. Create a knowledge object: Store the real-time information (e.g., shoe stock levels in different sizes and locations) in a JavaScript object.
2. Stringify the object: Convert the JavaScript object into a JSON string using JSON.stringify().
3. Insert into System Prompt: Add the stringified data to the system message's content. You can use a template literal (backticks) to format the prompt and interpolate the knowledge string.

### How it Works
The LLM's training includes an understanding of JSON and how to parse objects. When given this structured data in the system prompt, it can use that information to answer the user's questions accurately, such as confirming stock availability.

Here is my example script:
```
import { AzureOpenAI } from "openai";
import { endpoint, apiKey, apiVersion, model } from './clientConfig';

const client = new AzureOpenAI({
  endpoint,
  apiKey,
  apiVersion,
});

const knowledge = {
  'Los Angeles': {
    stock: {
      'Nike SB': {
        'size 6': 1,
        'size 7': 2,
        'size 8': 0,
        'size 9': 10,
        'size 10': 43,
        'size 11': 25
      }
    }
  }
};

const response = await client.chat.completions.create({
  model,
  messages: [
    {
      role: 'system',
      content: `
        You are the BestShoes chat bot.
        Here is your entire knowledge. You know nothing but this ${JSON.stringify(knowledge)}.
      `
    },
    {
      role: 'user',
      content: 'Hello, I would like to buy Nike SB shoes size 19. Do you have them in stock in Los Angeles?'
    }
  ],
  temperature: 0,
});

console.log(response.choices[0].message);
```
