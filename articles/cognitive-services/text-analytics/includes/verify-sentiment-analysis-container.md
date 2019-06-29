---
title: Suporte de contentor
titleSuffix: Azure Cognitive Services
description: Saiba como verificar a instância de contentor de análise de sentimentos.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 06/26/2019
ms.author: dapine
ms.openlocfilehash: f68d9c7098f2b1ca782e2522c632c2e267b35336
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67455089"
---
## <a name="verify-the-sentiment-analysis-container-instance"></a>Certifique-se a instância de contentor de análise de sentimentos

1. Selecione o **descrição geral** separador e copie o endereço IP.
1. Abra um novo separador do browser e utilizar o endereço IP, por exemplo, `http://<IP-address>:5000 (http://55.55.55.55:5000`). Home page do contentor é apresentada, permitindo-lhe saber o contentor está em execução.

    ![Ver a home page do contentor para verificar se está em execução](../media/how-tos/container-instance/swagger-docs-on-container.png)

1. Selecione o **descrição do serviço de API** ligação para navegar para a página do swagger de contentores.

1. Escolher qualquer uma da **POST** APIs e selecione **experimentá-lo**.  Os parâmetros são apresentados, incluindo a entrada de exemplo:

    ```json
    {
      "documents": [
        {
          "language": "en",
          "id": "1",
          "text": "Hello world. This is some input text that I love."
        },
        {
          "language": "fr",
          "id": "2",
          "text": "Bonjour tout le monde"
        },
        {
          "language": "es",
          "id": "3",
          "text": "La carretera estaba atascada. Había mucho tráfico el día de ayer."
        }
      ]
    }
    ```

1. Substitua a entrada com o seguinte JSON:

    ```json
    {
      "documents": [
        {
          "language": "en",
          "id": "7",
          "text": "I was fortunate to attend the KubeCon Conference in Barcelona, it is one of the best conferences I have ever attended. Great people, great sessions and I thoroughly enjoyed it!"
        }
      ]
    }
    ```

1. Definir **showStats** como true.

1. Selecione **Execute** para determinar os sentimentos do texto.

    O modelo empacotado no contentor gera uma pontuação entre 0 e 1, em que 0 é negativo e 1 é positivo.

    A resposta JSON devolvida inclui o sentimento para entrada de texto atualizado:

    ```json
    {
      "documents": [
      {
        "id": "7",
        "score": 0.9826303720474243,
        "statistics": {
          "charactersCount": 176,
            "transactionsCount": 1
          }
        }
      ],
      "errors": [],
      "statistics": {
        "documentsCount": 1,
        "validDocumentsCount": 1,
        "erroneousDocumentsCount": 0,
        "transactionsCount": 1
      }
    }
    ```

Vamos agora pode correlacionar o documento `id` payloads de resposta JSON para o documento de payload do pedido original `id`e ver o que não havia uma pontuação igual ao longo do `.98` que indicam um sentimento positivo muito.