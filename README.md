# JSON Documents Embedding Tool

An online tool to merge and compare text document items with their vector embeddings in JSON.  
[CHANGELOG](CHANGELOG.md) | [中文说明](README_zh.md)

## Introduction

Embedding and prompt engineering is a low-cost way of adding temporal new knowledge to LLMs like ChatGPT during a chat session.

Consider we are building a FAQ system for a new product that ChatGPT is not familiar with.
We already prepared 300 pages document, but there might be limitations/it costs too much to put them all in the prompt.
What if we can pick only 3 pages that related most to the user's question, then sent them to ChatGPT to understand?

Here is where "embedding" steps in.
With embedding, a certain text string is "paired" with an array of numbers.
Then by calculating "Cosine Similarity", we can find a similarity score(-1 to 1) of any two arrays.
The higher score it gets, indicates the meaning of the two texts are more likely "close" to each other.
In this way, we can find 3 pages out of 300 that related most to the user's input easily.

This tool is helping people who are preparing their documents to get embeddings with an Azure/OpenAI API.
And export as a static JSON file then can be used repeatedly during every chat session.
It can also compare the user's input with documents, and calculate the similarity scores.

Sample JSON:

```jsonc
[{
  "title": "How to Use Embedding",              // optional, the title of a document item
  "text": "Embedding and prompt...",            // required, the main text to be embedded
  "embedding": [-0.013891131,-0.0090339715...], // optional when importing, an array of numbers to present vector embedding
  "lock": false,                                // optional when importing, whether text and embedding are paired
  "extra": false,                               // optional when importing, show the following extra fields
  "link": "https://github.com/horans/",         // optional
  "image": "https://oai.azure.com/favicon.png", // optional
  "video": "",                                  // optional
  "version": "",                                // optional
  "function": ""                                // optional
}]
```

## Features

### Add

You can add a new document item to the current list by clicking the `Add` button.

### Delete

You can remove the current document item from the list by clicking the `Delete` button.

### Edit

You can input all the fields of a document item other than "Embedding".
After you finished editing your "Text" field, click the `Pair` button on the right.
It will request the Azure/OpenAI embedding API and responses with the result.
Then it will put a lock to your "Text" field which means that now the text and embedding are "paired".
You will have to unlock it before you can `Edit` your "Text" again.

You don't have to pair item by item, an automatic pair for all items will be performed when you `Export`.

### Import

You can import a document list from a JSON file by clicking the `Import` button.
The JSON file must follow the format of "Sample JSON" in the introduction section.
A minimum JSON can be:

```json
[
  { "text": "some words" },
  { "text": "other words" }
]
```

### Export

You can export document items in the current list to a JSON file by clicking the `Export` button.
If there are any items without "Text", those items will be skipped.
If there are any items with "Text" but without "Embedding", those items will be paired automatically.

### Reset

You can clear all the document items in the current list by clicking the `Reset` button.

### Config

You will need the following things before you can start embedding:

- Endpoint: `https://{your-resource-name}.openai.azure.com/openai/deployments/{deployment-id}/embeddings?api-version={api-version}`
- API Key: `{your-api-key}`
- Request Path: `input` by default
- Response Path: `data[0].embedding` by default

You can adjust these parameters by clicking the `Config` button.
If you are not using Azure/OpenAI embedding API, you may need to adjust the parameters of `fetch()` in codes.
Reference: <https://learn.microsoft.com/en-us/azure/cognitive-services/openai/reference#embeddings>  

### Compare

By clicking the `Compare` button, you can temporally input some text and compare it to the text in the list.
Once you clicked the `Check` button, a `Cosine Similarity` between input embedding and list item embeddings will be calculated.
The similarity score will be shown on each item after the calculation.
You can adjust a threshold to highlight items above it. (0.7 by default)
This feature will help you to test out if your preset documents can be "picked" by the user's input.

Similarity scores will be cleared during the following scenarios:

- Adding/Deleting/Importing document items
- Pairing any text with embedding

### Remember

Document list and API configs will be stored in `localStorage` of browser.
You can come back any time you want to continue your work.

## Dependencies

- [Vue](https://github.com/vuejs/core)
- [Bootstrap](https://github.com/twbs/bootstrap)
- [You don't (may not) need Lodash/Underscore](https://github.com/you-dont-need/You-Dont-Need-Lodash-Underscore)
- [ChatGPT](https://chat.openai.com/chat)
