---
title: Verifique a instância do recipiente de deteção de idiomas
titleSuffix: Azure Cognitive Services
description: Saiba como verificar a instância do recipiente de deteção de idiomas.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: dc52586550f89ddae147d79458584331ed984eea
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/08/2020
ms.locfileid: "80876456"
---
### <a name="verify-the-language-detection-container-instance"></a>Verifique a instância do recipiente de deteção de idiomas

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

1. Definir **showStats** para `true`.

1. Selecione **Executar** para determinar o sentimento do texto.

    O modelo que está embalado no recipiente gera uma pontuação que varia de 0 a 1, onde 0 é sentimento negativo e 1 é sentimento positivo.

    A resposta da JSON que foi devolvida inclui o sentimento para a entrada de texto atualizada:

    ```json
    {
      "documents": [
        {
          "id": "1",
          "detectedLanguages": [
            {
              "name": "English",
              "iso6391Name": "en",
              "score": 1
            }
          ],
          "statistics": {
            "charactersCount": 11,
            "transactionsCount": 1
          }
        },
        {
          "id": "2",
          "detectedLanguages": [
            {
              "name": "French",
              "iso6391Name": "fr",
              "score": 1
            }
          ],
          "statistics": {
            "charactersCount": 21,
            "transactionsCount": 1
          }
        },
        {
          "id": "3",
          "detectedLanguages": [
            {
              "name": "Spanish",
              "iso6391Name": "es",
              "score": 1
            }
          ],
          "statistics": {
            "charactersCount": 65,
            "transactionsCount": 1
          }
        },
        {
          "id": "4",
          "detectedLanguages": [
            {
              "name": "French",
              "iso6391Name": "fr",
              "score": 1
            }
          ],
          "statistics": {
            "charactersCount": 8,
            "transactionsCount": 1
          }
        }
      ],
      "errors": [],
      "statistics": {
        "documentsCount": 4,
        "validDocumentsCount": 4,
        "erroneousDocumentsCount": 0,
        "transactionsCount": 4
      }
    }
    ```

Podemos agora correlacionar os documentos dos dados jSON da carga de resposta `id`com os documentos de carga útil do pedido original pelo correspondente . Cada documento é tratado de `characterCount` forma `transactionCount`independente contendo várias estatísticas, tais como e . Adicionalmente, cada documento `detectedLanguages` resultante tem `name` `iso6391Name`a `score` matriz com o , e para cada idioma detetado. Quando várias línguas são `score` detetadas, a é usada para determinar a língua mais provável.