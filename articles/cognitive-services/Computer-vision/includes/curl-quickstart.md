---
title: 'Quickstart: Reconhecimento ótico de caracteres REST API'
titleSuffix: Azure Cognitive Services
description: Neste arranque rápido, começa com o reconhecimento ótico de caracteres REST API.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 12/02/2020
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 01149eed5cc4195ca501507e7fe1d66fffecb84d
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107327307"
---
Utilize o reconhecimento de caracteres óticos REST API para ler texto impresso e manuscrito.

> [!NOTE]
> Este quickstart utiliza comandos cURL para chamar a API REST. Também pode ligar para a API REST utilizando uma linguagem de programação. Veja as amostras do GitHub por exemplo em [C#](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/dotnet/ComputerVision/REST), [Python,](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/python/ComputerVision/REST) [Java,](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/java/ComputerVision/REST) [JavaScript](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/javascript/ComputerVision/REST)e [Go](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/go/ComputerVision/REST).

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure - [Crie uma gratuitamente](https://azure.microsoft.com/free/cognitive-services/) 
* Assim que tiver a subscrição do Azure, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision"  title=" crie um recurso de Visão de Computador crie um recurso de "  target="_blank"> Visão De Computador no portal </a> Azure para obter a sua chave e ponto final. Depois de implementar, clique em **Ir para o recurso**.
  * Necessitará da chave e ponto final do recurso que criar para ligar a sua aplicação ao serviço de Visão De Computador. Colará a chave e o ponto final no código abaixo mais tarde no arranque rápido.
  * Pode utilizar o nível de preços gratuitos `F0` para experimentar o serviço e fazer upgrade mais tarde para um nível pago para produção.
* [cURL](https://curl.haxx.se/) instalado



## <a name="read-printed-and-handwritten-text"></a>Ler texto impresso e manuscrito

O serviço OCR pode ler texto visível numa imagem e convertê-lo num fluxo de caracteres. Para obter mais informações sobre o reconhecimento de texto, consulte a visão geral do [reconhecimento de caracteres óticos (OCR).](../overview-ocr.md)

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
curl -v -X POST "https://westcentralus.api.cognitive.microsoft.com/vision/v3.2/read/analyze" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: <subscription key>" --data-ascii "{\"url\":\"https://upload.wikimedia.org/wikipedia/commons/thumb/a/af/Atomist_quote_from_Democritus.png/338px-Atomist_quote_from_Democritus.png\"}"
```

A resposta incluirá um `Operation-Location` cabeçalho, cujo valor é um URL único. Utilize este URL para consultar os resultados da operação Read. A URL expira em 48 horas.

1. Copie o seguinte comando para o seu editor de texto.
1. Substitua o URL pelo `Operation-Location` valor copiado no passo anterior.
1. Faça as alterações seguintes ao comando, se for necessário:
    1. Substitua o valor de `<subscriptionKey>` pela chave de subscrição.
1. Abra uma janela da linha de comandos.
1. Cole o comando a partir do editor de texto na janela da linha de comandos e, em seguida, execute o comando.

```bash
curl -v -X GET "https://westcentralus.api.cognitive.microsoft.com/vision/v3.2/read/analyzeResults/{operationId}" -H "Ocp-Apim-Subscription-Key: {subscription key}" --data-ascii "{body}" 
```

### <a name="examine-the-response"></a>Examinar a resposta

O JSON devolve uma resposta de êxito. A aplicação de exemplo analisa e apresenta uma resposta de êxito na janela da linha de comandos, semelhante ao seguinte exemplo:

```json
{
  "status": "succeeded",
  "createdDateTime": "2021-04-08T21:56:17.6819115+00:00",
  "lastUpdatedDateTime": "2021-04-08T21:56:18.4161316+00:00",
  "analyzeResult": {
    "version": "3.2",
    "readResults": [
      {
        "page": 1,
        "angle": 0,
        "width": 338,
        "height": 479,
        "unit": "pixel",
        "lines": [
          {
            "boundingBox": [
              25,
              14,
              318,
              14,
              318,
              59,
              25,
              59
            ],
            "text": "NOTHING",
            "appearance": {
              "style": {
                "name": "other",
                "confidence": 0.971
              }
            },
            "words": [
              {
                "boundingBox": [
                  27,
                  15,
                  294,
                  15,
                  294,
                  60,
                  27,
                  60
                ],
                "text": "NOTHING",
                "confidence": 0.994
              }
            ]
          }
        ]
      }
    ]
  }
}

```



## <a name="next-steps"></a>Passos seguintes

Explore a API OCR em mais profundidade. Para experimentar rapidamente com a API, experimente a [consola de testes Open API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2/operations/5d986960601faab4bf452005/console).

> [!div class="nextstepaction"]
> [Explore a API OCR](https://centraluseuap.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2/operations/5d986960601faab4bf452005)
