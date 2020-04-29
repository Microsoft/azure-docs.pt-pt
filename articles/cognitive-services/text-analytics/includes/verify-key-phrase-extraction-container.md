---
title: Verifique a instância do recipiente de extração de frases-chave
titleSuffix: Azure Cognitive Services
description: Aprenda a verificar a instância do recipiente de extração de frases-chave.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 5c177517ec18d7526b1cc09da74e35cb5434766d
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "80876454"
---
### <a name="verify-the-key-phrase-extraction-container-instance"></a>Verifique a instância do recipiente de extração de frases-chave

1. Selecione o separador **'Visão Geral'** e copie o endereço IP.
1. Abra um novo separador de navegador e introduza o endereço IP. Por exemplo, `http://<IP-address>:5000 (http://55.55.55.55:5000`introduza). A página inicial do recipiente é exibida, o que lhe permite saber que o recipiente está em funcionamento.

    ![Veja a página inicial do contentor para verificar se está a funcionar](../media/how-tos/container-instance/swagger-docs-on-container.png)

1. Selecione o link descrição da **API** de serviço para ir à página Swagger do contentor.

1. Escolha qualquer uma das APIs **POST** e selecione **Experimente- a**. Os parâmetros são apresentados, o que inclui esta entrada de exemplo:

    ```json
    {
      "documents": [
        {
          "id": "1",
          "text": "Hello world"
        },
        {
          "id": "2",
          "text": "Bonjour tout le monde"
        },
        {
          "id": "3",
          "text": "La carretera estaba atascada. Había mucho tráfico el día de ayer."
        },
        {
          "id": "4",
          "text": ":) :( :D"
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

    O modelo que está embalado no recipiente gera uma pontuação que varia de 0 a 1, onde 0 é negativo e 1 é positivo.

    A resposta da JSON que foi devolvida inclui o sentimento para a entrada de texto atualizada:

    ```json
    {
      "documents": [
        {
          "id": "7",
          "keyPhrases": [
            "Great people",
            "great sessions",
            "KubeCon Conference",
            "Barcelona",
            "best conferences"
          ],
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

Podemos agora correlacionar `id` o documento dos dados JSON da carga de `id`resposta ao documento original de carga útil do pedido. O documento resultante `keyPhrases` tem uma matriz, que contém a lista de frases-chave que foram extraídas do documento de entrada correspondente. Além disso, existem `characterCount` várias estatísticas, tais como e `transactionCount` para cada documento resultante.