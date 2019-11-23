---
title: What is QnA Maker service?
titleSuffix: Azure Cognitive Services
description: QnA Maker is a cloud-based NLP service that easily creates a natural conversational layer over your data. It can be used to find the most appropriate answer for any given natural language input, from your custom knowledge base (KB) of information.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: overview
ms.date: 11/22/2019
ms.author: diberry
ms.openlocfilehash: 944ddb7f83a4d10861e5a16dbc69b8f9e4dabfe0
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/23/2019
ms.locfileid: "74422682"
---
# <a name="what-is-the-qna-maker-service"></a>What is the QnA Maker service?

QnA Maker is a cloud-based Natural Language Processing (NLP) service that easily creates a natural conversational layer over your data. It can be used to find the most appropriate answer for any given natural language input, from your custom knowledge base (KB) of information.

A client application for QnA Maker is any conversational application that communicates with a user in natural language to answer a question. Alguns exemplos de aplicações cliente incluem aplicações de redes sociais, bots de chat e aplicações para ambiente de trabalho com fala ativada.

## <a name="when-to-use-qna-maker"></a>When to use QnA Maker

* **When you have static information** - Use QnA Maker when you have static information in your knowledge base of answers. This knowledge base is custom to your needs, which you've built with documents such as [PDFs and URLs](../concepts/data-sources-supported.md).
* **When you want to provide the same answer to a request, question, or command** - when different users submit the same question, the same answer is returned. 
* **When you want to filter static information based on meta-information** - add [metadata](../how-to/metadata-generateanswer-usage.md) tags to provide additional filtering options relevant to your client application's users and the information. Common metadata information includes [chit-chat](../how-to/chit-chat-knowledge-base.md), content type or format, content purpose, and content freshness.
* **When you want to manage a bot conversation that includes static information** - your knowledge base takes a user's conversational text or command and answers it. If the answer is part of a pre-determined conversation flow, represented in your knowledge base with [multi-turn context](../how-to/multiturn-conversation.md), the bot can easily provide this flow.  

## <a name="use-qna-maker-knowledge-base-in-a-chat-bot"></a>Use QnA Maker knowledge base in a chat bot

Once a QnA Maker knowledge base is published, a client application sends a question to your knowledge base endpoint and receives the results as a JSON response. A common client application for QnA Maker is a chat bot.

![Ask a bot a question and get answer from knowledge base content](../media/qnamaker-overview-learnabout/bot-chat-with-qnamaker.png)

|Passo|Ação|
|:--|:--|
|1|The client application sends the user's _question_ (text in their own words), "How do I programmatically update my Knowledge Base?" to your knowledge base endpoint.|
|2|QnA Maker uses the trained knowledge base to provide the correct answer and any follow-up prompts that can be used to refine the search for the best answer. QnA Maker returns a JSON-formatted response.|
|3|The client application uses the JSON response to make decisions about how to continue the conversation. These decisions can include showing the top answer and presenting more choices to refine the search for the best answer. |
|||

## <a name="what-is-a-knowledge-base"></a>What is a knowledge base? 

QnA Maker [imports your content](../concepts/data-sources-supported.md) into a knowledge base of question and answer sets. The import process extracts information about the relationship between the parts of your structured and semi-structured content to imply relationships between the question and answer sets. You can edit these question and answer sets or add new sets.  

The content of the question and answer set includes:
* All the alternate forms of the question
* Metadata tags used to filter answer choices during the search
* Follow-up prompts to continue the search refinement

![Example question and answer with metadata](../media/qnamaker-overview-learnabout/example-question-and-answer-with-metadata.png)

After you publish your knowledge base, a client application sends a user's question to your endpoint. Your QnA Maker service processes the question and responds with the best answer. 

## <a name="create-manage-and-publish-to-a-bot-without-code"></a>Create, manage, and publish to a bot without code

The QnA Maker portal provides the complete knowledge base authoring experience. You can import documents, in their current form, to your knowledge base. These documents (such as an FAQ, product manual, spreadsheet, or web page) are converted into question and answer sets. Each set is analyzed for follow-up prompts and connected to other sets. The final _markdown_ format supports rich presentation including images and links. 

Once your knowledge base is edited, publish the knowledge base to a working [Azure Web App bot](https://azure.microsoft.com/services/bot-service/) without writing any code. Test your bot in the [Azure portal](https://portal.azure.com) or download and continue development. 

## <a name="search-quality-and-ranking-provides-the-best-possible-answer"></a>Search quality and ranking provides the best possible answer

QnA Maker's system is a layered ranking approach. The data is stored in Azure search, which also serves as the first ranking layer. The top results from Azure search are then passed through QnA Maker's NLP re-ranking model to produce the final results and confidence score.

## <a name="qna-maker-improves-the-conversation-process"></a>QnA Maker improves the conversation process

QnA Maker provides multi-turn prompts and active learning to help you improve your basic question and answer sets. 

**Multi-turn prompts** give you the opportunity to connect question and answer pairs. This connection allows the client application to provide a top answer and provides more questions to refine the search for a final answer. 

After the knowledge base receives questions from users at the published endpoint, QnA Maker applies **active learning** to these real-world questions to suggest changes to your knowledge base to improve the quality. 

## <a name="development-lifecycle"></a>Ciclo de vida de desenvolvimento

QnA Maker provides authoring, training, and publishing along with collaboration permissions to integrate into the full development life cycle. 

## <a name="how-do-i-start"></a>O que devo fazer para começar?

**Step 1**: Create a QnA Maker resource in the [Azure portal](https://portal.azure.com). 

**Step 2**: Create a knowledge base in the [QnA Maker](https://www.qnamaker.ai) portal. Add [files and URLs](../concepts/data-sources-supported.md) to create the knowledge base.  

**Step 3**: Publish your knowledge base and test from your custom endpoint using [cURL or Postman](../Quickstarts/get-answer-from-knowledge-base-using-url-tool.md). 

**Step 4**: From your client application, programmatically call your knowledge base's endpoint. The client application processes the JSON response to show the best answer to the user.  

## <a name="next-steps"></a>Passos seguintes
QnA Maker provides everything you need to build, manage, and deploy your custom knowledge base. 

> [!div class="nextstepaction"]
> [Review the latest changes](../whats-new.md)
