# AI Chatbot Development Preparation

## Table of contents
- [OpenAI GPT3.5](#openai-gpt-3.5)
- [Node.js](#node.js)
- [Visual Studio Code](#visual-studio-code)

## OpenAI GPT3.5
The lecturer uses the GPT-3.5-turbo model, which is the most affordable, with free credits which should be enough to complete the course material. For me, I use Azure OpenAI GPT4.1.  
The steps to acquire the API Key varies depending on the AI provider, and here is the steps to get the GPT-3.5 API key:

1. Navigate to the Platform: Open a web browser and go to platform.openai.com.
2. Sign In or Sign Up: Either log into an existing account or create a new one.
3. Access API Keys: Once you are signed in, click on your name in the upper right-hand corner of the page.
4. Create a New Key: Select **View API keys** from the drop-down menu, and then click on **Create new secret key**.
5. Name and Copy the Key: Give the key a name (e.g., "a course") and then copy the key immediately. It is important to save this key in a safe place, as you will not be able to view it again.

## Node.js
Since our chatbot will be built on Next.js, it's neccessary to install node.js. Node.js is an open-source, cross-platform JavaScript runtime environment that lets you execute JavaScript code outside of a web browser.

Node.js is essential for Next.js because it provides the runtime environment that allows Next.js to run on the server. Next.js requires a server-side environment to handle its most powerful features. Next.js uses server-side rendering (SSR) to generate HTML on the server before sending it to the client's browser. This process is powered by Node.js, which executes the React components and their data-fetching logic on the server.

As a side note, it's better to install more up-to-date version since most of the AI related libraries I've ever used require later versions.

After the node.js install, simply use `node -v` or `node --version` to varify the installation. In addition, if we already have node.js installed but the version is not desired, we can install another 
package nvm to manage version control, it allows us to switch between node versions eligantly.

## Visual Studio Code
This is totally optional, there's a planty of IDEs that can run JavaScript/TypeScript.
