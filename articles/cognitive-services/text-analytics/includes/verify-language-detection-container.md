---
title: Verificar a instância de contêiner de Detecção de Idioma
titleSuffix: Azure Cognitive Services
description: Saiba como verificar a instância de contêiner de Detecção de Idioma.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 08/21/2019
ms.author: dapine
ms.openlocfilehash: 74d95d70438431495c9072e115854b1ccfa05bcb
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/27/2019
ms.locfileid: "70051911"
---
## <a name="verify-the-language-detection-container-instance"></a>Verificar a instância de contêiner de Detecção de Idioma

1. Selecione a guia **visão geral** e copie o endereço IP.
1. Abra uma nova guia do navegador e insira o endereço IP. Por exemplo, digite `http://<IP-address>:5000 (http://55.55.55.55:5000`). O home page do contêiner é exibido, o que permite que você saiba que o contêiner está em execução.

    ![Exibir o home page do contêiner para verificar se ele está em execução](../media/how-tos/container-instance/swagger-docs-on-container.png)

1. Selecione o link de **Descrição da API de serviço** para ir para a página do Swagger do contêiner.

1. Escolha qualquer uma das APIs **post** e selecione **experimentar**. Os parâmetros são exibidos, o que inclui esta entrada de exemplo:

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

1. Defina o de status `true`como.

1. Selecione **executar** para determinar a suopinião do texto.

    O modelo que é empacotado no contêiner gera uma pontuação que varia de 0 a 1, em que 0 é uma opinião negativa e 1 é uma opinião positiva.

    A resposta JSON retornada inclui um sentimentos para a entrada de texto atualizada:

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

Agora podemos correlacionar os documentos dos dados JSON da carga de resposta aos documentos de carga de solicitação originais por seus `id`correspondentes. Cada documento é tratado de `characterCount` forma independente, contendo várias estatísticas, como e. `transactionCount` Além disso, cada documento resultante tem `detectedLanguages` a matriz com `name`o `iso6391Name`, e `score` para cada idioma detectado. Quando vários idiomas são detectados, `score` o é usado para determinar a linguagem mais provável.