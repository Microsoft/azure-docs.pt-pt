---
title: Verifique a instância do recipiente de análise de sentimentos
titleSuffix: Azure Cognitive Services
description: Saiba como verificar a instância do recipiente de análise de sentimentos.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: c9b5411c044bb45d284cac0d30705c2b3d40ccd0
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/08/2020
ms.locfileid: "80876453"
---
### <a name="verify-the-sentiment-analysis-container-instance"></a>Verifique a instância do recipiente de análise de sentimentos

1. Selecione o separador **'Visão Geral'** e copie o endereço IP.
1. Abra um novo separador de navegador e introduza o endereço IP. Por exemplo, `http://<IP-address>:5000 (http://55.55.55.55:5000`introduza). A página inicial do recipiente é exibida, o que lhe permite saber que o recipiente está em funcionamento.

    ![Veja a página inicial do contentor para verificar se está a funcionar](../media/how-tos/container-instance/swagger-docs-on-container.png)

1. Selecione o link descrição da **API** de serviço para ir à página Swagger do contentor.

1. Escolha qualquer uma das APIs **POST** e selecione **Experimente- a**. Os parâmetros são apresentados, o que inclui esta entrada de exemplo:

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

1. Definir **showStats** para `true`.

1. Selecione **Executar** para determinar o sentimento do texto.

    O modelo que está embalado no recipiente gera uma pontuação que varia de 0 a 1, onde 0 é sentimento negativo e 1 é sentimento positivo.

    A resposta da JSON que foi devolvida inclui o sentimento para a entrada de texto atualizada:

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

Podemos agora correlacionar `id` o documento dos dados JSON da carga de `id`resposta ao documento original de carga útil do pedido. A pontuação `0.98` de mais do que indica um sentimento muito positivo.