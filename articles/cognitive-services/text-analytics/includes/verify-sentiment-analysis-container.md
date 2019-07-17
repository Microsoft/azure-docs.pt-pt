---
title: Certifique-se a instância de contentor de análise de sentimentos
titleSuffix: Azure Cognitive Services
description: Saiba como verificar a instância de contentor de análise de sentimentos.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 06/26/2019
ms.author: dapine
ms.openlocfilehash: f69d573e9e70a505018e94cca354f363097cc1b8
ms.sourcegitcommit: 6b41522dae07961f141b0a6a5d46fd1a0c43e6b2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/15/2019
ms.locfileid: "68229187"
---
## <a name="verify-the-sentiment-analysis-container-instance"></a>Certifique-se a instância de contentor de análise de sentimentos

1. Selecione o **descrição geral** separador e copie o endereço IP.
1. Abra um novo separador do browser e introduza o endereço IP. Por exemplo, introduza `http://<IP-address>:5000 (http://55.55.55.55:5000`). É apresentada a página de página inicial do contentor, permitindo-lhe saber o contentor está em execução.

    ![Ver a home page do contentor para verificar que está em execução](../media/how-tos/container-instance/swagger-docs-on-container.png).

1. Selecione o **descrição do serviço de API** ligação para aceder à página de swagger do contentor.

1. Escolher qualquer uma da **POST** APIs e selecione **experimentá-lo**.  Os parâmetros são apresentados, incluindo esta entrada de exemplo:

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

1. Substitua a entrada com o seguinte conteúdo JSON:

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

    O modelo que é empacotado no contentor gera uma pontuação entre 0 e 1, em que 0 é negativo e 1 é positivo.

    A resposta JSON devolvido inclui o sentimento para entrada de texto atualizado:

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

Vamos agora pode correlacionar o documento `id` dos dados JSON do payload de resposta para o documento de payload do pedido original `id`. Vemos uma pontuação igual a mais do que `.98`, que indicam um sentimento positivo rigidez.