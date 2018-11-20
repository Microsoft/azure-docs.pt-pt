---
title: incluir ficheiro
description: incluir ficheiro
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: include
ms.custom: include file
ms.date: 11/12/2018
ms.author: diberry
ms.openlocfilehash: 21b695c58f65bcf01500b89bd5456f4b52b813a5
ms.sourcegitcommit: 8314421d78cd83b2e7d86f128bde94857134d8e1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/19/2018
ms.locfileid: "51977478"
---
## <a name="request-and-response-json"></a>Pedido e resposta JSON

Uma exemplo formatado em JSON pergunta para a API REST:

```json
{
    "question": "Is the QnA Maker Service free?",
    "top": 3
}
```

A pergunta inclui uma propriedade para retornar as respostas de três principais. 

A resposta é devolvida num objeto JSON:

```json
{
  "answers": [
    {
      "questions": [
        "How do I embed the QnA Maker service in my website?"
      ],
      "answer": "Follow these steps to embed the QnA Maker service as a web-chat control in your website:\n\n\n1.  Create your FAQ bot by following the instructions [here](https://docs.microsoft.com/azure/cognitive-services/qnamaker/tutorials/create-qna-bot).\n2.  Enable the web chat by following the steps [here](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-webchat)",
      "score": 70.95,
      "id": 16,
      "source": "https://docs.microsoft.com/azure/cognitive-services/qnamaker/faqs",
      "metadata": []
    },
    {
      "questions": [
        "Do I need to use Bot Framework in order to use QnA Maker?"
      ],
      "answer": "No, you do not need to use the Bot Framework with QnA Maker. However, QnA Maker is offered as one of several templates in Azure Bot Service. Bot Service enables rapid intelligent bot development through Microsoft Bot Framework, and it runs in a server-less environment.",
      "score": 46.94,
      "id": 14,
      "source": "https://docs.microsoft.com/azure/cognitive-services/qnamaker/faqs",
      "metadata": []
    },
    {
      "questions": [
        "How can I create a bot with QnA Maker?"
      ],
      "answer": "Follow the instructions in [this](https://docs.microsoft.com/azure/cognitive-services/qnamaker/tutorials/create-qna-bot)documentation to create your Bot with Azure Bot Service.",
      "score": 43.25,
      "id": 15,
      "source": "https://docs.microsoft.com/azure/cognitive-services/qnamaker/faqs",
      "metadata": []
    }
  ]
}
```