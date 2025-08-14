# Chat Completion [more]

## Table of contents
- [Getting structured JSON output](#getting-structured-json-output)
- [OpenAI Function Calling](#openai-function-calling)

## Getting structured JSON output
Generating structured data (like JSON) from a language model is challenging. A simple request like "generate a list of tasks" often returns conversational text, not a format that's easy for a UI to use. By instructing the AI with phrases like "do not add any other text" or "you must respond in JSON," you can get closer to a usable format.

You can also specify the number of tasks, character limits, and other constraints within the prompt to control the output.
The most effective primitive method is to provide the AI with a schema or interface within the system prompt. You can tell the model to "strictly follow this schema" and define the expected JSON structure. For example, you can specify that the output should be an array of objects, with each object having properties like title, description, and difficulty.

The LLM, which has been trained on JSON, can then reliably produce perfectly valid JSON output that can be parsed and used in a front-end application or a database.

Limitations: This method, while functional, is considered "traditional" or "primitive." It relies on the AI correctly interpreting and following the instructions, which isn't always 100% reliable. The text notes that this can be "much, much better" than other methods, but it's not the most robust solution.

My script example (Before using JSON):
```
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
    {
      role: 'system',
      content: 'You are a talented planner. The user will tell you their goal and you will generate a list of tasks for them.'
    },
    {
      role: 'user',
      content: 'Hello, I want to renovate my kitchen.'
    }
  ],
  temperature: 0,
});

console.log(response.choices[0].message);
```
My script example (after using JSON):
```
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
    {
      role: 'system',
      content: `
        You are a talented planner.
        The user will tell you their goal and you will generate a list of tasks for them.
        You must respond in JSON, strictly following this schema:
        {
          tasks: {
            title: string,
            description: string,
            difficulty: "easy" | "medium" | "hard"
          }[]
        }
      `
    },
    {
      role: 'user',
      content: 'Hello, I want to renovate my kitchen.'
    }
  ],
  temperature: 0,
});

console.log(JSON.parse(String(response.choices[0].message.content)));
```

## OpenAI Function Calling
Relying on a system prompt to get a specific JSON structure is unreliable. As prompts become more complex, the AI may fail to follow the schema, producing output that your system can't process. This is a bad outcome for your application and end users.

OpenAI's Function Calling is a feature designed to solve this problem. It allows you to describe functions in your code to the AI.

Instead of giving a text response, the AI will determine which function to call based on the user's input, and it will provide the function name and its arguments in a structured format. This response is guaranteed to follow a structured format, ensuring reliability.

### How It Works: A Calculator example step-by-step
1. Define a Tool: In your API request, you provide an array of objects to the tools parameter. Each object is a description of a function that the AI can use. This description uses JSON Schema to define the function's name, purpose, and required parameters (e.g., add with parameters num1 and num2).
2. User Prompt: The user sends a natural language prompt, like "How much is four plus two?"
3. AI Response: The AI does not answer the question directly. Instead, it returns a structured tool_calls object. This object contains:
   - The name of the function it thinks it should call ("add").
   - The arguments for that function, extracted from the user's prompt (e.g., {"num1": 4, "num2": 2}).
4. Executing the Function: Your code receives this structured response and can then perform one of two actions:
   - Process the Data: Simply use the function name and arguments from the AI's response to process data within your system (e.g., save the parameters to a database).
   - Call a Real Function: You can have a real function in your codebase named add that accepts these arguments. Your code can use the AI's response to dynamically find and execute this function.

My script example:
```
import openai, { AzureOpenAI } from 'openai';
import { endpoint, apiKey, apiVersion, model } from './clientConfig';

// https://platform.openai.com/docs/api-reference/chat/create

const functions: {
  [key:string]: (obj: any) => void
} = {
  add: (obj: { num1: number, num2: number }) => {
    const sum = obj.num1 + obj.num2;
    console.log('Sum:', sum, '\n');
  },
  subtract: (obj: { num1: number, num2: number }) => {
    const diff = obj.num1 - obj.num2;
    console.log('Difference:', diff, '\n');
  }
};

const tools: openai.Chat.Completions.ChatCompletionTool[] = [
  {
    type: 'function',
    function: {
      name: 'add',
      description: 'Add two numbers',
      parameters: {
        type: 'object',
        properties: {
          num1: {
            type: 'number',
            description: 'The first addend'
          },
          num2: {
            type: 'number',
            description: 'The second addend'
          }
        },
        required: ['num1', 'num2']
      }
    }
  },
  {
    type: 'function',
    function: {
      name: 'subtract',
      description: 'Subtract two numbers',
      parameters: {
        type: 'object',
        properties: {
          num1: {
            type: 'number',
            description: 'The minuend'
          },
          num2: {
            type: 'number',
            description: 'The subtrahend'
          }
        },
        required: ['num1', 'num2']
      }
    }
  },
]

const client = new AzureOpenAI({
  endpoint,
  apiKey,
  apiVersion
});

const response = await client.chat.completions.create({
  messages: [
    {
      role: 'system',
      content: 'You are a smart calculator.'
    },
    {
      role: 'user',
      content: 'How much is 100 minus 22?'
    }
  ],
  tools,
  model,
  temperature: 0
});

const message = response.choices[0].message;
console.log('message:', message, '\n');

const fn = message?.tool_calls?.[0]?.function;
console.log('function:', fn, '\n');

if (fn && Object.hasOwn(functions, fn.name)) {
  functions[fn.name](JSON.parse(fn.arguments));
}
```
**Note that, here I use "tools", which is the newer API for functions. Functions is used in the course yet has been deprecated.**

### Real-World use cases
- Email Generator: An AI can be instructed to generate an email based on a user's request. It could then call a function that integrates with an email service like SendGrid or HubSpot to actually send the email.
- Database Operations: The AI can generate structured queries or data to be saved to a database, ensuring the data is always in the correct format.
