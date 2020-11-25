---
title: 'Início rápido: detetar rostos numa imagem com a API REST e JavaScript'
titleSuffix: Azure Cognitive Services
description: Neste guia de início rápido, irá detetar rostos de uma imagem através da API Face com o JavaScript nos Serviços Cognitivos.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.custom: devx-track-js
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: quickstart
ms.date: 11/23/2020
ms.author: pafarley
ms.openlocfilehash: f302000529e0dbf7ecce69ac9bebe77af59561ee
ms.sourcegitcommit: 1bf144dc5d7c496c4abeb95fc2f473cfa0bbed43
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/24/2020
ms.locfileid: "96020845"
---
# <a name="quickstart-detect-faces-in-an-image-using-the-rest-api-and-javascript"></a>Início rápido: detetar rostos numa imagem com a API REST e JavaScript

Neste arranque rápido, você usará a API AZure Face REST com JavaScript para detetar rostos humanos numa imagem.

## <a name="prerequisites"></a>Pré-requisitos

* Subscrição Azure - [Crie uma gratuitamente](https://azure.microsoft.com/free/cognitive-services/)
* Assim que tiver a subscrição do Azure, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesFace"  title=" crie um recurso Face crie um recurso Face no portal "  target="_blank"> <span class="docon docon-navigate-external x-hidden-focus"></span> </a> Azure para obter a sua chave e ponto final. Depois de implementar, clique em **Ir para o recurso**.
    * Necessitará da chave e ponto final do recurso que criar para ligar a sua aplicação à API face. Colará a chave e o ponto final no código abaixo mais tarde no arranque rápido.
    * Pode utilizar o nível de preços gratuitos `F0` para experimentar o serviço e fazer upgrade mais tarde para um nível pago para produção.
* Um editor de código como [Visual Studio Code](https://code.visualstudio.com/download)

## <a name="initialize-the-html-file"></a>Inicializar o ficheiro HTML

Crie um novo ficheiro HTML, *detectFaces.html*, e adicione o seguinte código.

```html
<!DOCTYPE html>
<html>
    <head>
        <title>Detect Faces Sample</title>
        <script src="https://ajax.googleapis.com/ajax/libs/jquery/1.9.0/jquery.min.js"></script>
    </head>
    <body></body>
</html>
```

Em seguida, adicione o seguinte código dentro `body` do elemento do documento. Este código configura uma interface de utilizador básica com um campo DE URL, um botão **de rosto de análise,** um painel de resposta e um painel de visualização de imagem.

:::code language="html" source="~/cognitive-services-quickstart-code/javascript/web/face/rest/detect.html" id="html_include":::

## <a name="write-the-javascript-script"></a>Escreva o script JavaScript

Adicione o seguinte código imediatamente acima do `h1` elemento no seu documento. Este código configura o código JavaScript que chama a API face.

:::code language="html" source="~/cognitive-services-quickstart-code/javascript/web/face/rest/detect.html" id="script_include":::

Terá de atualizar o `subscriptionKey` campo com o valor da sua chave de subscrição e precisa de alterar a cadeia para que `uriBase` contenha a cadeia de ponto final correta. O `returnFaceAttributes` campo especifica quais os atributos faciais para recuperar; pode pretender alterar esta cadeia dependendo da utilização pretendida.

[!INCLUDE [subdomains-note](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="run-the-script"></a>Executar o script

Abra *detectFaces.htmi* no seu browser. Quando clicar no botão **de rosto 'Analisar',** a aplicação deve exibir a imagem a partir do URL dado e imprimir uma série de dados faciais JSON.

![GettingStartCSharpScreenshot](../Images/face-detect-javascript.png)

O texto que se segue é um exemplo de uma resposta bem sucedida do JSON.

```json
[
  {
    "faceId": "49d55c17-e018-4a42-ba7b-8cbbdfae7c6f",
    "faceRectangle": {
      "top": 131,
      "left": 177,
      "width": 162,
      "height": 162
    }
  }
]
```

## <a name="extract-face-attributes"></a>Extrair atributos faciais
 
Para extrair atributos faciais, utilize o modelo de deteção 1 e adicione o `returnFaceAttributes` parâmetro de consulta.

```javascript
// Request parameters.
var params = {
    "detectionModel": "detection_01",
    "returnFaceAttributes": "age,gender,headPose,smile,facialHair,glasses,emotion,hair,makeup,occlusion,accessories,blur,exposure,noise",
    "returnFaceId": "true"
};
```

A resposta agora inclui atributos faciais. Por exemplo:

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

Neste arranque rápido, escreveu um script JavaScript que chama o serviço Azure Face para detetar rostos numa imagem e devolver os seus atributos. Em seguida, explore a documentação de referência da Face API para saber mais.

> [!div class="nextstepaction"]
> [API Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)
