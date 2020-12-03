---
title: 'Quickstart: Computer Vision REST API'
titleSuffix: Azure Cognitive Services
description: Neste arranque rápido, começa com a API de Visão De Computador.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 11/30/2020
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: db33a4c7ef80efecccdec9de48f52e864ace02df
ms.sourcegitcommit: 5b93010b69895f146b5afd637a42f17d780c165b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/02/2020
ms.locfileid: "96533353"
---
Utilize a API de Visão De Computador para:

* Analise uma imagem para tags, descrição de texto, rostos, conteúdo adulto, e muito mais.
* Leia texto impresso e manuscrito com a API de Leitura.
* Gere uma miniatura com corte inteligente



## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure - [Crie uma gratuitamente](https://azure.microsoft.com/free/cognitive-services/) 
* Assim que tiver a subscrição do Azure, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision"  title=" crie um recurso de Visão de Computador crie um recurso de "  target="_blank"> Visão De Computador no portal <span class="docon docon-navigate-external x-hidden-focus"></span> </a> Azure para obter a sua chave e ponto final. Depois de implementar, clique em **Ir para o recurso**.
  * Necessitará da chave e ponto final do recurso que criar para ligar a sua aplicação ao serviço de Visão De Computador. Colará a chave e o ponto final no código abaixo mais tarde no arranque rápido.
  * Pode utilizar o nível de preços gratuitos `F0` para experimentar o serviço e fazer upgrade mais tarde para um nível pago para produção.
* [cURL](https://curl.haxx.se/) instalado

## <a name="analyze-an-image"></a>Analisar uma imagem

Para analisar uma imagem para uma variedade de funcionalidades visuais, faça os seguintes passos:

1. Copie o comando seguinte para um editor de texto.
1. Faça as alterações seguintes ao comando, se for necessário:
    1. Substitua o valor de `<subscriptionKey>` pela chave de subscrição.
    1. Substitua a primeira parte do URL de pedido `westcentralus` () com o texto no seu próprio URL de ponto final.
        [!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]
    1. Opcionalmente, altere o URL da imagem no corpo do pedido (`http://upload.wikimedia.org/wikipedia/commons/3/3c/Shaki_waterfall.jpg\`) pelo URL de uma imagem diferente a ser analisada.
1. Abra uma janela da linha de comandos.
1. Cole o comando a partir do editor de texto na janela da linha de comandos e, em seguida, execute o comando.

```bash
curl -H "Ocp-Apim-Subscription-Key: <subscriptionKey>" -H "Content-Type: application/json" "https://westcentralus.api.cognitive.microsoft.com/vision/v3.1/analyze?visualFeatures=Categories,Description&details=Landmarks" -d "{\"url\":\"http://upload.wikimedia.org/wikipedia/commons/3/3c/Shaki_waterfall.jpg\"}"
```

### <a name="examine-the-response"></a>Examinar a resposta

O JSON devolve uma resposta de êxito. A aplicação de exemplo analisa e apresenta uma resposta de êxito na janela da linha de comandos, semelhante ao seguinte exemplo:

```json
{
  "categories": [
    {
      "name": "outdoor_water",
      "score": 0.9921875,
      "detail": {
        "landmarks": []
      }
    }
  ],
  "description": {
    "tags": [
      "nature",
      "water",
      "waterfall",
      "outdoor",
      "rock",
      "mountain",
      "rocky",
      "grass",
      "hill",
      "covered",
      "hillside",
      "standing",
      "side",
      "group",
      "walking",
      "white",
      "man",
      "large",
      "snow",
      "grazing",
      "forest",
      "slope",
      "herd",
      "river",
      "giraffe",
      "field"
    ],
    "captions": [
      {
        "text": "a large waterfall over a rocky cliff",
        "confidence": 0.916458423253597
      }
    ]
  },
  "requestId": "b6e33879-abb2-43a0-a96e-02cb5ae0b795",
  "metadata": {
    "height": 959,
    "width": 1280,
    "format": "Jpeg"
  }
}
```

## <a name="read-printed-and-handwritten-text"></a>Ler texto impresso e manuscrito

A Visão computacional pode ler texto visível numa imagem e convertê-lo num fluxo de caracteres. Para obter mais informações sobre o reconhecimento de texto, consulte o doc conceptual de [reconhecimento de caracteres óticos (OCR).](../concept-recognizing-text.md#read-api)

Para criar e executar o exemplo, siga os seguintes passos:

1. Copie o comando seguinte para um editor de texto.
1. Faça as alterações seguintes ao comando, se for necessário:
    1. Substitua o valor de `<subscriptionKey>` pela chave de subscrição.
    1. Substitua a primeira parte do URL de pedido `westcentralus` () com o texto no seu próprio URL de ponto final.
        [!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]
    1. Opcionalmente, altere o URL da imagem no corpo do pedido (`https://upload.wikimedia.org/wikipedia/commons/thumb/a/af/Atomist_quote_from_Democritus.png/338px-Atomist_quote_from_Democritus.png\`) pelo URL de uma imagem diferente a ser analisada.
1. Abra uma janela da linha de comandos.
1. Cole o comando a partir do editor de texto na janela da linha de comandos e, em seguida, execute o comando.

```bash
curl -v -X POST "https://westcentralus.api.cognitive.microsoft.com/vision/v3.1/read/analyze?language={string}" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: <subscription key>" --data-ascii "{\"url\":\"https://upload.wikimedia.org/wikipedia/commons/thumb/a/af/Atomist_quote_from_Democritus.png/338px-Atomist_quote_from_Democritus.png\"}"
```

A resposta incluirá um `Operation-Location` cabeçalho, cujo valor é um URL único. Utilize este URL para consultar os resultados da operação Read. A URL expira em 48 horas.

1. Copie o seguinte comando para o seu editor de texto.
1. Substitua o URL pelo `Operation-Location` valor copiado no passo anterior.
1. Faça as alterações seguintes ao comando, se for necessário:
    1. Substitua o valor de `<subscriptionKey>` pela chave de subscrição.
1. Abra uma janela da linha de comandos.
1. Cole o comando a partir do editor de texto na janela da linha de comandos e, em seguida, execute o comando.

```bash
curl -v -X GET "https://westcentralus.api.cognitive.microsoft.com/vision/v3.1/read/analyzeResults/{operationId}" -H "Ocp-Apim-Subscription-Key: {subscription key}" --data-ascii "{body}" 
```

### <a name="examine-the-response"></a>Examinar a resposta

O JSON devolve uma resposta de êxito. A aplicação de exemplo analisa e apresenta uma resposta de êxito na janela da linha de comandos, semelhante ao seguinte exemplo:

```json
{
  "status": "succeeded",
  "createdDateTime": "2019-10-03T14:32:04.236Z",
  "lastUpdatedDateTime": "2019-10-03T14:38:14.852Z",
  "analyzeResult": {
    "version": "v3.0",
    "readResults": [
      {
        "page": 1,
        "language": "en",
        "angle": 49.59,
        "width": 600,
        "height": 400,
        "unit": "pixel",
        "lines": [
          {
            "boundingBox": [
              202,
              618,
              2047,
              643,
              2046,
              840,
              200,
              813
            ],
            "text": "Our greatest glory is not",
            "words": [
              {
                "boundingBox": [
                  204,
                  627,
                  481,
                  628,
                  481,
                  830,
                  204,
                  829
                ],
                "text": "Our",
                "confidence": 0.164
              },
              {
                "boundingBox": [
                  519,
                  628,
                  1057,
                  630,
                  1057,
                  832,
                  518,
                  830
                ],
                "text": "greatest",
                "confidence": 0.164
              },
              {
                "boundingBox": [
                  1114,
                  630,
                  1549,
                  631,
                  1548,
                  833,
                  1114,
                  832
                ],
                "text": "glory",
                "confidence": 0.164
              },
              {
                "boundingBox": [
                  1586,
                  631,
                  1785,
                  632,
                  1784,
                  834,
                  1586,
                  833
                ],
                "text": "is",
                "confidence": 0.164
              },
              {
                "boundingBox": [
                  1822,
                  632,
                  2115,
                  633,
                  2115,
                  835,
                  1822,
                  834
                ],
                "text": "not",
                "confidence": 0.164
              }
            ]
          },
...
        ]
      },
      {
        "page": 2,
        "language": "en",
        "angle": 1.32,
        "width": 600,
        "height": 400,
        "unit": "pixel",
        "lines": [
          {
            "boundingBox": [
              1612,
              903,
              2744,
              935,
              2738,
              1139,
              1607,
              1107
            ],
            "text": "in never failing ,",
            "words": [
              {
                "boundingBox": [
                  1611,
                  934,
                  1707,
                  933,
                  1708,
                  1147,
                  1613,
                  1147
                ],
                "text": "in",
                "confidence": 0.164
              },
              {
                "boundingBox": [
                  1753,
                  933,
                  2132,
                  930,
                  2133,
                  1144,
                  1754,
                  1146
                ],
                "text": "never",
                "confidence": 0.999
              },
              {
                "boundingBox": [
                  2162,
                  930,
                  2673,
                  927,
                  2674,
                  1140,
                  2164,
                  1144
                ],
                "text": "failing",
                "confidence": 0.164
              },
              {
                "boundingBox": [
                  2703,
                  926,
                  2788,
                  926,
                  2790,
                  1139,
                  2705,
                  1140
                ],
                "text": ",",
                "confidence": 0.164
              }
            ]
          }
        ]
      }
    ]
  }
}
```

## <a name="generate-a-thumbnail"></a>Gerar uma miniatura

Pode utilizar a Visão de Computador para gerar uma miniatura com corte inteligente. Especifica a altura e largura desejadas, que podem diferir na relação de aspeto da imagem de entrada. A Computer Vision utiliza a cultura inteligente para identificar inteligentemente a área de interesse e gerar coordenadas de corte em toda a região.
 
Para criar e executar o exemplo, siga os seguintes passos:

1. Copie o comando seguinte para um editor de texto.
1. Faça as alterações seguintes ao comando, se for necessário:
    1. Substitua o valor de `<subscriptionKey>` pela chave de subscrição.
    1. Substitua o valor do `<thumbnailFile>` caminho e nome do ficheiro para guardar a imagem da miniatura devolvida.
    1. Substitua a primeira parte do URL de pedido `westcentralus` () com o texto no seu próprio URL de ponto final.
        [!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]
    1. Opcionalmente, altere o URL da imagem no corpo do pedido (`https://upload.wikimedia.org/wikipedia/commons/thumb/5/56/Shorkie_Poo_Puppy.jpg/1280px-Shorkie_Poo_Puppy.jpg\`) pelo URL de uma imagem diferente a partir da qual pretende gerar uma miniatura.
1. Abra uma janela da linha de comandos.
1. Cole o comando do editor de texto na janela de ordem.
1. Prima para executar o programa.

    ```bash
    curl -H "Ocp-Apim-Subscription-Key: <subscriptionKey>" -o <thumbnailFile> -H "Content-Type: application/json" "https://westus.api.cognitive.microsoft.com/vision/v3.1/generateThumbnail?width=100&height=100&smartCropping=true" -d "{\"url\":\"https://upload.wikimedia.org/wikipedia/commons/thumb/5/56/Shorkie_Poo_Puppy.jpg/1280px-Shorkie_Poo_Puppy.jpg\"}"
    ```

### <a name="examine-the-response"></a>Examinar a resposta

Uma resposta de êxito escreve a imagem em miniatura para o ficheiro especificado em `<thumbnailFile>`. Se o pedido falhar, a resposta contém um código de erro e uma mensagem para ajudar a determinar o que correu mal. Se o pedido parece ter sucesso, mas a miniatura criada não é um ficheiro de imagem válido, pode ser que a sua chave de subscrição não seja válida.


## <a name="next-steps"></a>Passos seguintes

Explore a API de Visão Computacional em maior profundidade. Para experimentar rapidamente a API de Imagem Digitalizada, experimente a [Consola de teste de API aberta](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/56f91f2e778daf14a499f21b/console).

> [!div class="nextstepaction"]
> [Explorar a API de Imagem Digitalizada](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/56f91f2e778daf14a499f21b)
