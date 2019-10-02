---
title: 'Início rápido: Use a rotação para obter a resposta da base de dados de conhecimento-QnA Maker'
titleSuffix: Azure Cognitive Services
description: Este guia de início rápido orienta você na obtenção de uma resposta da sua base de dados de conhecimento usando a ondulação.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 10/01/2019
ms.author: diberry
ms.openlocfilehash: b698b40546ee1655ebbef3980692ede6b51fc7f1
ms.sourcegitcommit: 4f3f502447ca8ea9b932b8b7402ce557f21ebe5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/02/2019
ms.locfileid: "71803016"
---
# <a name="quickstart-get-answer-from-knowledge-base-using-curl"></a>Início rápido: Obter resposta da base de dados de conhecimento usando a ondulação

Este guia de início rápido baseado em ondulado orienta você pela obtenção de uma resposta da sua base de dados de conhecimento.

## <a name="prerequisites"></a>Pré-requisitos

* [**Ondulação**](https://curl.haxx.se/)mais recente.
* Você deve ter um [serviço QnA Maker](../How-To/set-up-qnamaker-service-azure.md) e ter uma [base de dados de conhecimento com perguntas e respostas](../Tutorials/create-publish-query-in-portal.md).

## <a name="publish-to-get-endpoint"></a>Publicar para obter ponto de extremidade

Quando você estiver pronto para gerar uma resposta para uma pergunta de sua base de dados de conhecimento, [publique](../Quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base) sua base de dados de conhecimento.

## <a name="use-production-endpoint-with-curl"></a>Usar ponto de extremidade de produção com ondulação

Quando sua base de dados de conhecimento é publicada, a página **publicar** exibe as configurações de solicitação HTTP para gerar uma resposta. A guia **ondulação** mostra as configurações necessárias para gerar uma resposta da ferramenta de linha de comando, à [ondulação](https://www.getpostman.com).

[resultados de @no__t 1Publish](../media/qnamaker-use-to-generate-answer/curl-command-on-publish-page.png)](../media/qnamaker-use-to-generate-answer/curl-command-on-publish-page.png#lightbox)

Para gerar uma resposta com a ONDULAção, conclua as seguintes etapas:

1. Copie o texto na guia ONDULAção. 
1. Abra uma linha de comando ou um terminal e cole o texto.
1. Edite a pergunta para ser relevante para sua base de dados de conhecimento. Tenha cuidado para não remover o JSON que o contém ao redor da pergunta.
1. Insira o comando. 
1. A resposta inclui as informações relevantes sobre a resposta. 

    ```bash
    > curl -X POST https://qnamaker-f0.azurewebsites.net/qnamaker/knowledgebases/1111f8c-d01b-4698-a2de-85b0dbf3358c/generateAnswer -H "Authorization: EndpointKey 111841fb-c208-4a72-9412-03b6f3e55ca1" -H "Content-type: application/json" -d "{'question':'How do I programmatically update my Knowledge Base?'}"
    {
      "answers": [
        {
          "questions": [
            "How do I programmatically update my Knowledge Base?"
          ],
          "answer": "You can use our REST APIs to manage your Knowledge Base. See here for details: https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/update",
          "score": 100.0,
          "id": 18,
          "source": "Custom Editorial",
          "metadata": [
            {
              "name": "category",
              "value": "api"
            }
          ]
        }
      ]
    }
    ```

## <a name="use-staging-endpoint-with-curl"></a>Usar ponto de extremidade de preparo com ondulação

Se você quiser obter uma resposta do ponto de extremidade de preparo, use a propriedade de corpo `isTest`.

```json
isTest:true
```

## <a name="next-steps"></a>Passos seguintes

A página publicar também fornece informações para [gerar uma resposta com o](get-answer-from-kb-using-postman.md) postmaster. 

> [!div class="nextstepaction"]
> [Usar metadados ao gerar uma resposta](../How-to/metadata-generateanswer-usage.md)
