---
title: 'Início rápido: Detectar faces em uma imagem usando a API REST e o JavaScript'
titleSuffix: Azure Cognitive Services
description: Neste guia de início rápido, irá detetar rostos de uma imagem através da API Face com o JavaScript nos Serviços Cognitivos.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: quickstart
ms.date: 09/06/2019
ms.author: pafarley
ms.openlocfilehash: bc8d20abcc7bc66d319874978e134c5c86c86e1c
ms.sourcegitcommit: 65131f6188a02efe1704d92f0fd473b21c760d08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/10/2019
ms.locfileid: "70859041"
---
# <a name="quickstart-detect-faces-in-an-image-using-the-rest-api-and-javascript"></a>Início rápido: Detectar faces em uma imagem usando a API REST e o JavaScript

Neste guia de início rápido, você usará a API REST do Azure face com JavaScript para detectar faces humanas em uma imagem.

## <a name="prerequisites"></a>Pré-requisitos

- Uma chave de assinatura da Face API. Pode obter uma chave de subscrição de avaliação gratuita de [experimentar os serviços cognitivos](https://azure.microsoft.com/try/cognitive-services/?api=face-api). Em alternativa, siga as instruções em [criar uma conta dos serviços cognitivos](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) para subscrever o serviço de API de rostos e obtenha a chave.
- Um editor de códigos como [Visual Studio Code](https://code.visualstudio.com/download)

## <a name="initialize-the-html-file"></a>Inicializar o arquivo HTML

Crie um novo arquivo HTML, *detectFaces. html*, e adicione o código a seguir.

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

Em seguida, adicione o seguinte código `body` dentro do elemento do documento. Esse código configura uma interface do usuário básica com um campo de URL, um botão **analisar face** , um painel de resposta e um painel de exibição de imagem.

```html
<h1>Detect Faces:</h1>
Enter the URL to an image that includes a face or faces, then click
the <strong>Analyze face</strong> button.<br><br>
Image to analyze: <input type="text" name="inputImage" id="inputImage"
    value="https://upload.wikimedia.org/wikipedia/commons/c/c3/RH_Louise_Lillian_Gish.jpg" />
<button onclick="processImage()">Analyze face</button><br><br>
<div id="wrapper" style="width:1020px; display:table;">
    <div id="jsonOutput" style="width:600px; display:table-cell;">
        Response:<br><br>
        <textarea id="responseTextArea" class="UIInput"
            style="width:580px; height:400px;"></textarea>
    </div>
    <div id="imageDiv" style="width:420px; display:table-cell;">
        Source image:<br><br>
        <img id="sourceImage" width="400" />
    </div>
</div>
```

## <a name="write-the-javascript-script"></a>Gravar o script JavaScript

Adicione o código a seguir imediatamente acima `h1` do elemento em seu documento. Esse código configura o código JavaScript que chama o API de Detecção Facial.

```html
<script type="text/javascript">
    function processImage() {
        // Replace <Subscription Key> with your valid subscription key.
        var subscriptionKey = "<Subscription Key>";
    
        var uriBase =
            "https://<My Endpoint String>.com/face/v1.0/detect";
    
        // Request parameters.
        var params = {
            "returnFaceId": "true",
            "returnFaceLandmarks": "false",
            "returnFaceAttributes":
                "age,gender,headPose,smile,facialHair,glasses,emotion," +
                "hair,makeup,occlusion,accessories,blur,exposure,noise"
        };
    
        // Display the image.
        var sourceImageUrl = document.getElementById("inputImage").value;
        document.querySelector("#sourceImage").src = sourceImageUrl;
    
        // Perform the REST API call.
        $.ajax({
            url: uriBase + "?" + $.param(params),
    
            // Request headers.
            beforeSend: function(xhrObj){
                xhrObj.setRequestHeader("Content-Type","application/json");
                xhrObj.setRequestHeader("Ocp-Apim-Subscription-Key", subscriptionKey);
            },
    
            type: "POST",
    
            // Request body.
            data: '{"url": ' + '"' + sourceImageUrl + '"}',
        })
    
        .done(function(data) {
            // Show formatted JSON on webpage.
            $("#responseTextArea").val(JSON.stringify(data, null, 2));
        })
    
        .fail(function(jqXHR, textStatus, errorThrown) {
            // Display error message.
            var errorString = (errorThrown === "") ?
                "Error. " : errorThrown + " (" + jqXHR.status + "): ";
            errorString += (jqXHR.responseText === "") ?
                "" : (jQuery.parseJSON(jqXHR.responseText).message) ?
                    jQuery.parseJSON(jqXHR.responseText).message :
                        jQuery.parseJSON(jqXHR.responseText).error.message;
            alert(errorString);
        });
    };
</script>
```

Você precisará atualizar o `subscriptionKey` campo com o valor de sua chave de assinatura e precisará alterar a cadeia de `uriBase` caracteres para que ela contenha a cadeia de caracteres de ponto de extremidade correta. O `returnFaceAttributes` campo especifica quais atributos de face devem ser recuperados; talvez você queira alterar essa cadeia de caracteres dependendo do uso pretendido.

[!INCLUDE [subdomains-note](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="run-the-script"></a>Executar o script

Abra *detectFaces. html* no navegador. Quando você clica no botão **analisar face** , o aplicativo deve exibir a imagem da URL fornecida e imprimir uma cadeia de caracteres JSON de dados de face.

![GettingStartCSharpScreenshot](../Images/face-detect-javascript.png)

O texto a seguir é um exemplo de uma resposta JSON bem-sucedida.

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

## <a name="next-steps"></a>Passos Seguintes

Neste guia de início rápido, você escreveu um script JavaScript que chama o API de Detecção Facial do Azure para detectar rostos em uma imagem e retornar seus atributos. Em seguida, explore a documentação de referência do API de Detecção Facial para saber mais.

> [!div class="nextstepaction"]
> [API Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)
