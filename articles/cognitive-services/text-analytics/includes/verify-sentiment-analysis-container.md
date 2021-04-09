---
title: Verifique a instância do contentor de Análise de Sentimento
titleSuffix: Azure Cognitive Services
description: Saiba como verificar a instância do recipiente de Análise de Sentimento.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: d0a5e09f1d697c2f5f842edfb06be4b13229d095
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "96009889"
---
### <a name="verify-the-sentiment-analysis-container-instance"></a>Verifique a instância do contentor de Análise de Sentimento

1. Selecione o **separador Visão Geral** e copie o endereço IP.
1. Abra um novo separador de navegador e insira o endereço IP. Por exemplo, `http://<IP-address>:5000 (http://55.55.55.55:5000` insira). A página inicial do recipiente é apresentada, o que lhe permite saber se o recipiente está em funcionamento.

    ![Veja a página inicial do contentor para verificar se está em funcionamento](../media/how-tos/container-instance/swagger-docs-on-container.png)

1. Selecione o link de descrição da **API de serviço** para ir à página swagger do recipiente.

1. Escolha qualquer uma das APIs **post** e selecione **Experimentá-lo**. Os parâmetros são apresentados, que inclui esta entrada de exemplo:

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

1. Substitua a entrada pelo seguinte conteúdo JSON:

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

1. Definir **showStats** para `true` .

1. **Selecione Executar** para determinar o sentimento do texto.

    O modelo que é embalado no recipiente gera uma pontuação que varia de 0 a 1, onde 0 é sentimento negativo e 1 é sentimento positivo.

    A resposta JSON que é devolvida inclui sentimento para a entrada de texto atualizada:

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

Podemos agora correlacionar o documento `id` dos dados JSON da carga útil de resposta com o documento de carga útil do pedido `id` original. A pontuação de mais do que `0.98` indica um sentimento muito positivo.