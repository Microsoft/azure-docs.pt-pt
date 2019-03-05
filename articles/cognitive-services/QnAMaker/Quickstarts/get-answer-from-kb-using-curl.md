---
title: 'Início rápido: Utilize o cURL para receber resposta da base de dados de conhecimento - QnA Maker'
titleSuffix: Azure Cognitive Services
description: Este início rápido explica como obter uma resposta de sua base de dados de conhecimento com o cURL.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 03/04/2019
ms.author: diberry
ms.openlocfilehash: 3b728984b2bda836d3d4924b93f1b11a5d05d8bb
ms.sourcegitcommit: 8b41b86841456deea26b0941e8ae3fcdb2d5c1e1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2019
ms.locfileid: "57342467"
---
# <a name="quickstart-get-answer-from-knowledge-base-using-curl"></a>Início rápido: Receber resposta da base de dados de conhecimento com cURL

Este guia de introdução com base em cURL explica como obter uma resposta de sua base de dados de conhecimento.

## <a name="prerequisites"></a>Pré-requisitos

* Mais recente [ **cURL**](https://curl.haxx.se/).
* Tem de ter uma [serviço QnA Maker](../How-To/set-up-qnamaker-service-azure.md) e têm um [base de dados de conhecimento com perguntas e respostas](../Tutorials/create-publish-query-in-portal.md).

## <a name="publish-to-get-endpoint"></a>Publicar para obter o ponto final

Quando estiver pronto para gerar uma resposta a uma pergunta da sua base de dados de conhecimento [publicar](../How-to/publish-knowledge-base.md) sua base de dados de conhecimento.

## <a name="use-production-endpoint-with-curl"></a>Utilizar o ponto final de produção com cURL

Quando é publicado a sua base de dados de conhecimento, o **publicar** página apresenta as definições do pedido HTTP para gerar uma resposta. O **CURL** separador mostra as definições necessárias para gerar uma resposta da ferramenta de linha de comandos [CURL](https://www.getpostman.com).

[![Publicar os resultados](../media/qnamaker-use-to-generate-answer/curl-command-on-publish-page.png)](../media/qnamaker-use-to-generate-answer/curl-command-on-publish-page.png#lightbox)

Para gerar uma resposta com o CURL, conclua os seguintes passos:

1. Copie o texto no separador de CURL. 
1. Abra uma linha de comandos ou terminal e cole o texto.
1. Edite a pergunta para ser relevante para a sua base de dados de conhecimento. Tenha cuidado para não remover o JSON que contêm que envolvem a pergunta.
1. Introduza o comando. 
1. A resposta inclui as informações relevantes sobre a resposta. 

    ```bash
    > curl -X POST https://qnamaker-f0.azurewebsites.net/qnamaker/knowledgebases/1111f8c-d01b-4698-a2de-85b0dbf3358c/generateAnswer -H "Authorization: EndpointKey 111841fb-c208-4a72-9412-03b6f3e55ca1" -H "Content-type: application/json" -d "{'question':'How do I programmatically update my Knowledge Base?'}"
    {
      "answers": [
        {
          "questions": [
            "How do I programmatically update my Knowledge Base?"
          ],
          "answer": "You can use our REST APIs to manage your Knowledge Base. See here for details: https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da7600",
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

## <a name="use-staging-endpoint-with-curl"></a>Utilizar o ponto final de teste com cURL

Se quiser obter uma resposta do ponto de extremidade de teste, utilize o parâmetro booleano da cadeia de consulta `isTest` com o valor do `true`.

`isTest=true`

## <a name="next-steps"></a>Passos Seguintes

Página de publicação também fornece informações que lhe [gerar uma resposta](get-answer-from-kb-using-postman.md) com o Postman. 

> [!div class="nextstepaction"]
> [Metadados de utilização ao gerar uma resposta](../How-to/metadata-generateanswer-usage.md)
