---
title: 'Quickstart: Detetar rostos numa imagem com a API rest AZure e Java'
titleSuffix: Azure Cognitive Services
description: Neste arranque rápido, você usará a API AZure Face REST com Java para detetar rostos numa imagem.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: quickstart
ms.date: 08/05/2020
ms.custom: devx-track-java
ms.author: pafarley
ms.openlocfilehash: fbe62cf00422710e18a6b112adc08f19ea03177b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/08/2020
ms.locfileid: "91858358"
---
# <a name="quickstart-detect-faces-in-an-image-using-the-rest-api-and-java"></a>Início rápido: detetar rostos numa imagem com a API REST e Java

Neste arranque rápido, você usará a API AZure Face REST com Java para detetar rostos humanos numa imagem.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/cognitive-services/) antes de começar. 

## <a name="prerequisites"></a>Pré-requisitos

* Subscrição Azure - [Crie uma gratuitamente](https://azure.microsoft.com/free/cognitive-services/)
* Assim que tiver a subscrição do Azure, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesFace"  title=" crie um recurso Face crie um recurso Face no portal "  target="_blank"> <span class="docon docon-navigate-external x-hidden-focus"></span> </a> Azure para obter a sua chave e ponto final. Depois de implementar, clique em **Ir para o recurso**.
    * Necessitará da chave e ponto final do recurso que criar para ligar a sua aplicação à API face. Colará a chave e o ponto final no código abaixo mais tarde no arranque rápido.
    * Pode utilizar o nível de preços gratuitos `F0` para experimentar o serviço e fazer upgrade mais tarde para um nível pago para produção.
* Qualquer Java IDE à sua escolha.

## <a name="create-the-java-project"></a>Criar o projeto Java

1. Crie uma nova aplicação java de linha de comando no seu IDE e adicione uma classe **Principal** com um método **principal.**
1. Importe as seguintes bibliotecas para o projeto Java. Se estiver a utilizar o Maven, as coordenadas do Maven são fornecidas para cada biblioteca.
   - [Cliente Apache HTTP](https://hc.apache.org/downloads.cgi) (org.apache.httpcomponents:httpclient:4.5.6)
   - [Núcleo APACHE HTTP](https://hc.apache.org/downloads.cgi) (org.apache.httpcomponents:httpcore:4.4.10)
   - [Biblioteca JSON](https://github.com/stleary/JSON-java) (org.json:json:20180130)
   - [Exploração madeireira Apache Commons](https://commons.apache.org/proper/commons-logging/download_logging.cgi) (commons-logging:commons-logging:1.1.2)

## <a name="add-face-detection-code"></a>Adicionar código de deteção facial

Abra a classe principal do seu projeto. Aqui, irá adicionar o código necessário para carregar imagens e detetar rostos.

### <a name="import-packages"></a>Importar pacotes

Adicione as `import` seguintes declarações ao topo do ficheiro.

:::code language="java" source="~/cognitive-services-quickstart-code/java/Face/rest/detect.java" id="dependencies":::

### <a name="add-essential-fields"></a>Adicionar campos essenciais

Substitua a classe **Principal** pelo seguinte código. Estes dados especificam como ligar ao serviço Face e onde obter os dados de entrada. Terá de atualizar o `subscriptionKey` campo com o valor da sua chave de subscrição e alterar a cadeia de modo a conter a cadeia de ponto final `uriBase` correta. Também pode desejar definir o `imageWithFaces` valor para um caminho que aponta para um arquivo de imagem diferente.

[!INCLUDE [subdomains-note](../../../../includes/cognitive-services-custom-subdomains-note.md)]

O `faceAttributes` campo é simplesmente uma lista de certos tipos de atributos. Especificará quais as informações a obter sobre os rostos detetados.

:::code language="java" source="~/cognitive-services-quickstart-code/java/Face/rest/detect.java" id="environment":::

### <a name="call-the-face-detection-rest-api"></a>Ligue para a API de deteção facial REST

Adicione o método **principal** com o seguinte código. Constrói uma chamada REST para a API face para detetar informações faciais na imagem remota (a `faceAttributes` cadeia especifica quais os atributos faciais para recuperar). Em seguida, escreve os dados de saída para uma cadeia JSON.

:::code language="java" source="~/cognitive-services-quickstart-code/java/Face/rest/detect.java" id="main":::

### <a name="parse-the-json-response"></a>Parse a resposta JSON

Diretamente abaixo do código anterior, adicione o seguinte bloco, que converte os dados JSON devolvidos num formato mais facilmente legível antes de imprimi-los à consola. Finalmente, feche o bloco de captura, o método **principal,** e a classe **Principal.**

:::code language="java" source="~/cognitive-services-quickstart-code/java/Face/rest/detect.java" id="print":::

## <a name="run-the-app"></a>Executar a aplicação

Compilar o código e executá-lo. Uma resposta bem sucedida apresentará dados face em formato JSON facilmente legível na janela da consola. Por exemplo:

```json
[{
  "faceRectangle": {
    "top": 131,
    "left": 177,
    "width": 162,
    "height": 162
  }
}]
```

## <a name="extract-face-attributes"></a>Extrair atributos faciais
 
Para extrair atributos faciais, utilize o modelo de deteção 1 e adicione o `returnFaceAttributes` parâmetro de consulta.

```java
builder.setParameter("detectionModel", "detection_01");
builder.setParameter("returnFaceAttributes", "age,gender,headPose,smile,facialHair,glasses,emotion,hair,makeup,occlusion,accessories,blur,exposure,noise");
```

A resposta agora inclui atributos faciais. Por exemplo:

```json
[{
  "faceRectangle": {
    "top": 131,
    "left": 177,
    "width": 162,
    "height": 162
  },
  "faceAttributes": {
    "makeup": {
      "eyeMakeup": true,
      "lipMakeup": true
    },
    "facialHair": {
      "sideburns": 0,
      "beard": 0,
      "moustache": 0
    },
    "gender": "female",
    "accessories": [],
    "blur": {
      "blurLevel": "low",
      "value": 0.06
    },
    "headPose": {
      "roll": 0.1,
      "pitch": 0,
      "yaw": -32.9
    },
    "smile": 0,
    "glasses": "NoGlasses",
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
    },
    "emotion": {
      "contempt": 0,
      "surprise": 0.005,
      "happiness": 0,
      "neutral": 0.986,
      "sadness": 0.009,
      "disgust": 0,
      "anger": 0,
      "fear": 0
    },
    "exposure": {
      "value": 0.67,
      "exposureLevel": "goodExposure"
    },
    "occlusion": {
      "eyeOccluded": false,
      "mouthOccluded": false,
      "foreheadOccluded": false
    },
    "noise": {
      "noiseLevel": "low",
      "value": 0
    },
    "age": 22.9
  },
  "faceId": "49d55c17-e018-4a42-ba7b-8cbbdfae7c6f"
}]
```

## <a name="next-steps"></a>Passos seguintes

Neste quickstart, criou uma aplicação simples para consolas Java que utiliza chamadas REST para a Azure Face API para detetar rostos numa imagem e devolver os seus atributos. Em seguida, explore a documentação de referência da Face API para saber mais sobre os cenários suportados.

> [!div class="nextstepaction"]
> [API Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)
