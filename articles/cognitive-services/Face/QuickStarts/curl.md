---
title: 'Quickstart: Detete rostos numa imagem com a API e cURL do Rest Azure'
titleSuffix: Azure Cognitive Services
description: Neste arranque rápido, utilizará a API De REPOUSO Face Azure com cURL para detetar rostos numa imagem.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: quickstart
ms.date: 04/14/2020
ms.author: pafarley
ms.openlocfilehash: 7e7eb9ba942cd29db824388c02b2933c533c4b45
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81403024"
---
# <a name="quickstart-detect-faces-in-an-image-using-the-face-rest-api-and-curl"></a>Quickstart: Detete rostos numa imagem utilizando a Face REST API e cURL

Neste arranque rápido, você usará a API De REPOUSO Face Azure com cURL para detetar rostos humanos numa imagem.

Se não tiver uma subscrição Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar. 

## <a name="prerequisites"></a>Pré-requisitos

- Uma chave de subscrição Face. Você pode obter uma chave de subscrição de teste gratuito da [Try Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=face-api). Ou, siga as instruções na [Conta Criar uma Conta de Serviços Cognitivos](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) para subscrever o serviço Face e obter a sua chave.

## <a name="write-the-command"></a>Escreva o comando
 
Você usará um comando como o seguinte para ligar para a API facial e obter dados de atributos faciais a partir de uma imagem. Primeiro, copie o código&mdash;num editor de texto que terá de fazer alterações em certas partes do comando antes de o poder executar.

```shell
curl -H "Ocp-Apim-Subscription-Key: <Subscription Key>" "https://<My Endpoint String>.com/face/v1.0/detect?returnFaceId=true&returnFaceLandmarks=false&returnFaceAttributes=age,gender,headPose,smile,facialHair,glasses,emotion,hair,makeup,occlusion,accessories,blur,exposure,noise" -H "Content-Type: application/json" --data-ascii "{\"url\":\"https://upload.wikimedia.org/wikipedia/commons/c/c3/RH_Louise_Lillian_Gish.jpg\"}"
```

### <a name="subscription-key"></a>Chave de subscrição
Substitua-a `<Subscription Key>` com a sua chave de subscrição Face válida.

### <a name="face-endpoint-url"></a>URL de ponto final facial

O `https://<My Endpoint String>.com/face/v1.0/detect` URL indica o ponto final do Face Azure para consulta. Pode ser necessário alterar a primeira parte deste URL para corresponder ao ponto final que corresponde à sua chave de subscrição.

[!INCLUDE [subdomains-note](../../../../includes/cognitive-services-custom-subdomains-note.md)]

### <a name="url-query-string"></a>Cadeia de consulta url

A cadeia de consulta do URL do ponto final do face especifica quais os atributos faciais para recuperar. Pode querer alterar esta cadeia dependendo da utilização pretendida.

```
?returnFaceId=true&returnFaceLandmarks=false&returnFaceAttributes=age,gender,headPose,smile,facialHair,glasses,emotion,hair,makeup,occlusion,accessories,blur,exposure,noise
```

### <a name="image-source-url"></a>URL de fonte de imagem
O URL de origem indica a imagem a utilizar como entrada. Pode alterar isto para apontar para qualquer imagem que queira analisar.

```
https://upload.wikimedia.org/wikipedia/commons/c/c3/RH_Louise_Lillian_Gish.jpg
``` 

## <a name="run-the-command"></a>Execute o comando

Assim que fizeres as tuas alterações, abra um pedido de comando e entre no novo comando. Deve ver as informações faciais apresentadas como dados JSON na janela da consola. Por exemplo:

```json
[
  {
    "faceId": "49d55c17-e018-4a42-ba7b-8cbbdfae7c6f",
    "faceRectangle": {
      "top": 131,
      "left": 177,
      "width": 162,
      "height": 162
    },
    "faceAttributes": {
      "smile": 0,
      "headPose": {
        "pitch": 0,
        "roll": 0.1,
        "yaw": -32.9
      },
      "gender": "female",
      "age": 22.9,
      "facialHair": {
        "moustache": 0,
        "beard": 0,
        "sideburns": 0
      },
      "glasses": "NoGlasses",
      "emotion": {
        "anger": 0,
        "contempt": 0,
        "disgust": 0,
        "fear": 0,
        "happiness": 0,
        "neutral": 0.986,
        "sadness": 0.009,
        "surprise": 0.005
      },
      "blur": {
        "blurLevel": "low",
        "value": 0.06
      },
      "exposure": {
        "exposureLevel": "goodExposure",
        "value": 0.67
      },
      "noise": {
        "noiseLevel": "low",
        "value": 0
      },
      "makeup": {
        "eyeMakeup": true,
        "lipMakeup": true
      },
      "accessories": [],
      "occlusion": {
        "foreheadOccluded": false,
        "eyeOccluded": false,
        "mouthOccluded": false
      },
      "hair": {
        "bald": 0,
        "invisible": false,
        "hairColor": [
          {
            "color": "brown",
            "confidence": 1
          },
          {
            "color": "black",
            "confidence": 0.87
          },
          {
            "color": "other",
            "confidence": 0.51
          },
          {
            "color": "blond",
            "confidence": 0.08
          },
          {
            "color": "red",
            "confidence": 0.08
          },
          {
            "color": "gray",
            "confidence": 0.02
          }
        ]
      }
    }
  }
]
```

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, escreveu um comando cURL que chama o serviço Azure Face para detetar rostos numa imagem e devolver os seus atributos. Em seguida, explore a documentação de referência da API face para saber mais.

> [!div class="nextstepaction"]
> [API Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)
