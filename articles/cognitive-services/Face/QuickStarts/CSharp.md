---
title: 'Quickstart: Detetar rostos numa imagem com a API E C de Azure REST #'
titleSuffix: Azure Cognitive Services
description: Neste arranque rápido, utilizará a API AZure Face REST com C# para detetar rostos numa imagem.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: quickstart
ms.date: 11/23/2020
ms.author: pafarley
ms.openlocfilehash: f15fbc340aafa325f772a1fd50a2129fa5044c47
ms.sourcegitcommit: 1bf144dc5d7c496c4abeb95fc2f473cfa0bbed43
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/24/2020
ms.locfileid: "96020879"
---
# <a name="quickstart-detect-faces-in-an-image-using-the-face-rest-api-and-c"></a>Quickstart: Detetar rostos numa imagem utilizando a API face REST e C #

Neste arranque rápido, você usará a API AZure Face REST com C# para detetar rostos humanos numa imagem.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/cognitive-services/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

* Subscrição Azure - [Crie uma gratuitamente](https://azure.microsoft.com/free/cognitive-services/)
* Assim que tiver a subscrição do Azure, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesFace"  title=" crie um recurso Face crie um recurso Face no portal "  target="_blank"> <span class="docon docon-navigate-external x-hidden-focus"></span> </a> Azure para obter a sua chave e ponto final. Depois de implementar, clique em **Ir para o recurso**.
    * Necessitará da chave e ponto final do recurso que criar para ligar a sua aplicação à API face. Colará a chave e o ponto final no código abaixo mais tarde no arranque rápido.
    * Pode utilizar o nível de preços gratuitos `F0` para experimentar o serviço e fazer upgrade mais tarde para um nível pago para produção.
- Qualquer edição do [Visual Studio.](https://www.visualstudio.com/downloads/)

## <a name="create-the-visual-studio-project"></a>Criar o projeto do Visual Studio

1. No Visual Studio, crie um novo projeto **de aplicação para consolas (.NET Framework)** e nomeie-o **FaceDetection**.
1. Se houver outros projetos na sua solução, selecione esta como o único projeto de arranque.

## <a name="add-face-detection-code"></a>Adicionar código de deteção facial

Abra o arquivo *Program.cs* do novo projeto. Aqui, irá adicionar o código necessário para carregar imagens e detetar rostos.

### <a name="include-namespaces"></a>Incluir espaços de nomes

Adicione as declarações `using` seguintes à parte superior do ficheiro *Program.cs*.

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/Face/rest/detect.cs" id="dependencies":::

### <a name="add-essential-fields"></a>Adicionar campos essenciais

Adicione a classe **Programa** que contém os seguintes campos. Estes dados especificam como ligar ao serviço Face e onde obter os dados de entrada. Terá de atualizar o `subscriptionKey` campo com o valor da sua chave de subscrição, e poderá ter de alterar a cadeia para que contenha a sua cadeia de `uriBase` pontos finais de recursos.

[!INCLUDE [subdomains-note](../../../../includes/cognitive-services-custom-subdomains-note.md)]

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/Face/rest/detect.cs" id="environment":::

### <a name="receive-image-input"></a>Receber entrada de imagem

Adicione o seguinte código ao método **principal** da classe **Programa.** Este código escreve uma solicitação à consola pedindo ao utilizador que introduza o seu caminho de ficheiro de imagem local. Em seguida, chama outro método, **MakeAnalysisRequest,** para processar a imagem naquele local.

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/Face/rest/detect.cs" id="main":::

### <a name="call-the-face-detection-rest-api"></a>Ligue para a API de deteção facial REST

Adicione o seguinte método à classe **Programa**. Constrói uma chamada REST para a API face para detetar informações faciais na imagem remota (a `requestParameters` cadeia especifica quais os atributos faciais para recuperar). Em seguida, escreve os dados de saída para uma cadeia JSON.

Definirá os métodos de ajuda nos seguintes passos.

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/Face/rest/detect.cs" id="request":::

### <a name="process-the-input-image-data"></a>Processar os dados de imagem de entrada

Adicione o seguinte método à classe **Programa**. Este método converte a imagem no caminho do ficheiro especificado numa matriz byte.

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/Face/rest/detect.cs" id="getimage":::

### <a name="parse-the-json-response"></a>Parse a resposta JSON

Adicione o seguinte método à classe **Programa**. Este método formata a entrada JSON para ser mais facilmente legível. A sua aplicação escreverá estes dados de cadeia para a consola. Em seguida, pode fechar a classe e o espaço de nome.

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/Face/rest/detect.cs" id="print":::

## <a name="run-the-app"></a>Executar a aplicação

Uma resposta bem sucedida apresentará dados face em formato JSON facilmente legível. Por exemplo:

```json
[
   {
      "faceId": "f7eda569-4603-44b4-8add-cd73c6dec644",
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

```csharp
string requestParameters = "detectionModel=detection_01&returnFaceId=true&returnFaceLandmarks=false&returnFaceAttributes=age,gender,headPose,smile,facialHair,glasses,emotion,hair,makeup,occlusion,accessories,blur,exposure,noise";
```

A resposta agora inclui atributos faciais. Por exemplo:

```json
[
   {
      "faceId": "f7eda569-4603-44b4-8add-cd73c6dec644",
      "faceRectangle": {
         "top": 131,
         "left": 177,
         "width": 162,
         "height": 162
      },
      "faceAttributes": {
         "smile": 0.0,
         "headPose": {
            "pitch": 0.0,
            "roll": 0.1,
            "yaw": -32.9
         },
         "gender": "female",
         "age": 22.9,
         "facialHair": {
            "moustache": 0.0,
            "beard": 0.0,
            "sideburns": 0.0
         },
         "glasses": "NoGlasses",
         "emotion": {
            "anger": 0.0,
            "contempt": 0.0,
            "disgust": 0.0,
            "fear": 0.0,
            "happiness": 0.0,
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
            "value": 0.0
         },
         "makeup": {
            "eyeMakeup": true,
            "lipMakeup": true
         },
         "accessories": [

         ],
         "occlusion": {
            "foreheadOccluded": false,
            "eyeOccluded": false,
            "mouthOccluded": false
         },
         "hair": {
            "bald": 0.0,
            "invisible": false,
            "hairColor": [
               {
                  "color": "brown",
                  "confidence": 1.0
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

Neste arranque rápido, criou uma aplicação simples de consola .NET que utiliza chamadas REST com o serviço Azure Face para detetar rostos numa imagem e devolver os seus atributos. Em seguida, explore a documentação de referência da Face API para saber mais sobre os cenários suportados.

> [!div class="nextstepaction"]
> [API Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)
