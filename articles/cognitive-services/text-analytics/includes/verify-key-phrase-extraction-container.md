---
title: Verifique a instância do recipiente de extração de frase-chave
titleSuffix: Azure Cognitive Services
description: Saiba como verificar a instância do recipiente de extração de frases-chave.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 5dca4828a5c1127133461ddf9fc06099fc176b68
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96009908"
---
### <a name="verify-the-key-phrase-extraction-container-instance"></a>Verifique a instância do recipiente de extração de frase-chave

1. Selecione o **separador Visão Geral** e copie o endereço IP.
1. Abra um novo separador de navegador e insira o endereço IP. Por exemplo, `http://<IP-address>:5000 (http://55.55.55.55:5000` insira). A página inicial do recipiente é apresentada, o que lhe permite saber se o recipiente está em funcionamento.

    ![Veja a página inicial do contentor para verificar se está em funcionamento](../media/how-tos/container-instance/swagger-docs-on-container.png)

1. Selecione o link de descrição da **API de serviço** para ir à página swagger do recipiente.

1. Escolha qualquer uma das APIs **post** e selecione **Experimentá-lo**. Os parâmetros são apresentados, que inclui esta entrada de exemplo:

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

1. Definir **showStats** para `true` .

1. **Selecione Executar** para determinar o sentimento do texto.

    O modelo que é embalado no recipiente gera uma pontuação que varia de 0 a 1, onde 0 é negativo e 1 é positivo.

    A resposta JSON que é devolvida inclui sentimento para a entrada de texto atualizada:

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

Podemos agora correlacionar o documento `id` dos dados JSON da carga útil de resposta com o documento de carga útil do pedido `id` original. O documento resultante tem um `keyPhrases` conjunto, que contém a lista de frases-chave que foram extraídas do documento de entrada correspondente. Além disso, existem várias estatísticas, tais como `characterCount` e para cada documento `transactionCount` resultante.