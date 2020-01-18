---
title: 'Início rápido: detectar faces em uma imagem com a API REST do Azure e a ondulação'
titleSuffix: Azure Cognitive Services
description: Neste guia de início rápido, você usará a API REST do Azure face com a rotação para detectar rostos em uma imagem.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: quickstart
ms.date: 12/05/2019
ms.author: pafarley
ms.openlocfilehash: 6a1a6d1fdce4853a2ac73f10eb4cf0a0505fa4c7
ms.sourcegitcommit: d29e7d0235dc9650ac2b6f2ff78a3625c491bbbf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/17/2020
ms.locfileid: "76165897"
---
# <a name="quickstart-detect-faces-in-an-image-using-the-face-rest-api-and-curl"></a>Início rápido: detectar faces em uma imagem usando a API REST de face e a ondulação

Neste guia de início rápido, você usará a API REST de face do Azure com ondulação para detectar faces humanas em uma imagem.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar. 

## <a name="prerequisites"></a>Pré-requisitos

- Uma chave de assinatura facial. Pode obter uma chave de subscrição de avaliação gratuita de [experimentar os serviços cognitivos](https://azure.microsoft.com/try/cognitive-services/?api=face-api). Ou siga as instruções em [criar uma conta de serviços cognitivas](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) para assinar o serviço de face e obter sua chave.

## <a name="write-the-command"></a>Gravar o comando
 
Você usará um comando como o seguinte para chamar o API de Detecção Facial e obter dados de atributo facial de uma imagem. Primeiro, copie o código em um editor de texto&mdash;você precisará fazer alterações em determinadas partes do comando antes de poder executá-lo.

```shell
curl -H "Ocp-Apim-Subscription-Key: <Subscription Key>" "https://<My Endpoint String>.com/face/v1.0/detect?returnFaceId=true&returnFaceLandmarks=false&returnFaceAttributes=age,gender,headPose,smile,facialHair,glasses,emotion,hair,makeup,occlusion,accessories,blur,exposure,noise" -H "Content-Type: application/json" --data-ascii "{\"url\":\"https://upload.wikimedia.org/wikipedia/commons/c/c3/RH_Louise_Lillian_Gish.jpg\"}"
```

### <a name="subscription-key"></a>Chave de subscrição
Substitua `<Subscription Key>` pela chave de assinatura de face válida.

### <a name="face-endpoint-url"></a>URL do ponto de extremidade facial

A URL `https://<My Endpoint String>.com/face/v1.0/detect` indica o ponto de extremidade de face do Azure a ser consultado. Talvez seja necessário alterar a primeira parte dessa URL para corresponder ao ponto de extremidade que corresponde à sua chave de assinatura.

[!INCLUDE [subdomains-note](../../../../includes/cognitive-services-custom-subdomains-note.md)]

### <a name="url-query-string"></a>Cadeia de caracteres de consulta de URL

A cadeia de caracteres de consulta da URL do ponto de extremidade de face especifica quais atributos de face recuperar. Talvez você queira alterar essa cadeia de caracteres dependendo do uso pretendido.

```
?returnFaceId=true&returnFaceLandmarks=false&returnFaceAttributes=age,gender,headPose,smile,facialHair,glasses,emotion,hair,makeup,occlusion,accessories,blur,exposure,noise
```

### <a name="image-source-url"></a>URL de origem da imagem
A URL de origem indica a imagem a ser usada como entrada. Você pode alterar isso para apontar para qualquer imagem que deseja analisar.

```
https://upload.wikimedia.org/wikipedia/commons/c/c3/RH_Louise_Lillian_Gish.jpg
``` 

## <a name="run-the-command"></a>Execute o comando

Depois de fazer as alterações, abra um prompt de comando e insira o novo comando. Você deve ver as informações de face exibidas como dados JSON na janela do console. Por exemplo:

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

Neste guia de início rápido, você escreveu um comando de ondulação que chama o serviço de face do Azure para detectar rostos em uma imagem e retornar seus atributos. Em seguida, explore a documentação de referência do API de Detecção Facial para saber mais.

> [!div class="nextstepaction"]
> [API Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)
