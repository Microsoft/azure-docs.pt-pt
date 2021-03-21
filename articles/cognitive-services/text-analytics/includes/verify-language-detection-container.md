---
title: Verifique a instância do recipiente de deteção de idiomas
titleSuffix: Azure Cognitive Services
description: Saiba como verificar a instância do recipiente de deteção de idiomas.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 543a4d85982adadc86435819679351c8ffaa9814
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96009890"
---
### <a name="verify-the-language-detection-container-instance"></a>Verifique a instância do recipiente de deteção de idiomas

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

1. Definir **showStats** para `true` .

1. **Selecione Executar** para determinar o sentimento do texto.

    O modelo que é embalado no recipiente gera uma pontuação que varia de 0 a 1, onde 0 é sentimento negativo e 1 é sentimento positivo.

    A resposta JSON que é devolvida inclui sentimento para a entrada de texto atualizada:

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

Podemos agora correlacionar os documentos dos dados JSON da carga útil de resposta com os documentos de carga útil do pedido original pelos seus correspondentes `id` . Cada documento é tratado independentemente contendo várias estatísticas, tais `characterCount` `transactionCount` como. Além disso, cada documento resultante tem a `detectedLanguages` matriz com o , e para cada `name` `iso6391Name` `score` idioma detetado. Quando várias línguas são detetadas, `score` a é usada para determinar a língua mais provável.