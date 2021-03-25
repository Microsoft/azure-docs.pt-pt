---
title: Início rápido da API face REST
description: Utilize a API Face REST com cURL para detetar rostos, encontrar semelhantes (procura facial por imagem), identificar rostos (pesquisa de reconhecimento facial) e migrar os dados do seu rosto.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: include
ms.date: 12/06/2020
ms.author: pafarley
ms.openlocfilehash: 1a360a19da8006a8ab6d918560f1b1d566fdd66d
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2021
ms.locfileid: "105104023"
---
Começa com o reconhecimento facial utilizando a API Face REST. O serviço Face fornece-lhe acesso a algoritmos avançados para detetar e reconhecer rostos humanos em imagens.

Utilize a API Face REST para:

* [Detetar rostos numa imagem](#detect-faces-in-an-image)
* [Encontre rostos semelhantes](#find-similar-faces)

> [!NOTE]
> Este quickstart utiliza comandos cURL para chamar a API REST. Também pode ligar para a API REST utilizando uma linguagem de programação. Veja as amostras do GitHub por exemplo em [C#](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/dotnet/Face/rest), [Python,](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/python/Face/rest) [Java,](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/java/Face/rest) [JavaScript](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/javascript/Face/rest)e [Go](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/go/Face/rest).

## <a name="prerequisites"></a>Pré-requisitos

* Subscrição Azure - [Crie uma gratuitamente](https://azure.microsoft.com/free/cognitive-services/)
* Assim que tiver a subscrição do Azure, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesFace"  title=" crie um recurso Face crie um recurso Face no portal "  target="_blank"> </a> Azure para obter a sua chave e ponto final. Depois de implementar, clique em **Ir para o recurso**.
    * Você precisará da chave e ponto final do recurso que cria para ligar a sua aplicação à API Face. Colará a chave e o ponto final no código abaixo mais tarde no arranque rápido.
    * Pode utilizar o nível de preços gratuitos `F0` para experimentar o serviço e fazer upgrade mais tarde para um nível pago para produção.
* [Versão PowerShell 6.0+](/powershell/scripting/install/installing-powershell-core-on-windows)ou uma aplicação de linha de comando semelhante.


## <a name="detect-faces-in-an-image"></a>Detetar rostos numa imagem

Você usará um comando como o seguinte para ligar para a API face e obter dados de atributos faciais a partir de uma imagem. Em primeiro lugar, copie o código num editor de texto &mdash; que terá de fazer alterações em certas partes do comando antes de o poder executar.

:::code language="shell" source="~/cognitive-services-quickstart-code/curl/face/detect.sh" ID="detection_model_2":::

Efetue as seguintes alterações:
1. Atribua `Ocp-Apim-Subscription-Key` à sua chave de subscrição Face válida.
1. Altere a primeira parte do URL de consulta para corresponder ao ponto final que corresponde à sua chave de subscrição.
   [!INCLUDE [subdomains-note](../../../../../includes/cognitive-services-custom-subdomains-note.md)]
1. Altere opcionalmente o URL no corpo do pedido para apontar para uma imagem diferente.

Depois de fazer as alterações, abra um pedido de comando e introduza o novo comando. 

### <a name="examine-the-results"></a>Examinar os resultados

Deve ver as informações do rosto apresentadas como dados JSON na janela da consola. Por exemplo:

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

### <a name="get-face-attributes"></a>Obtenha atributos faciais
 
Para extrair atributos faciais, ligue novamente para a API detete, mas definida `detectionModel` para `detection_01` . Adicione o `returnFaceAttributes` parâmetro de consulta também. O comando deve agora parecer o seguinte. Como antes, insira a chave de subscrição Face e o ponto final.

:::code language="shell" source="~/cognitive-services-quickstart-code/curl/face/detect.sh" ID="detection_model_1":::

### <a name="examine-the-results"></a>Examinar os resultados

A informação de rosto devolvida agora inclui atributos faciais. Por exemplo:

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

## <a name="find-similar-faces"></a>Encontrar rostos semelhantes

Esta operação requer um único rosto detetado (fonte) e procura um conjunto de outras faces (alvo) para encontrar fósforos (procura facial por imagem). Quando encontra uma correspondência, imprime o ID da face compatível com a consola.

### <a name="detect-faces-for-comparison"></a>Detetar rostos para comparação

Primeiro, tens de detetar rostos nas imagens antes de as poderes comparar. Executar este comando como fez na secção [Faces Detect.](#detect-faces-in-an-image) Este método de deteção está otimizado para operações de comparação. Não extrai atributos faciais detalhados como na secção acima, e usa um modelo de deteção diferente.

:::code language="shell" source="~/cognitive-services-quickstart-code/curl/face/detect.sh" ID="detect_for_similar":::

Encontre o `"faceId"` valor na resposta JSON e guarde-o para um local temporário. Em seguida, chame o comando acima novamente para estes urLs de imagem, e salve as suas identificações faciais também. Usarás estas identificações como o grupo alvo de rostos para encontrar um rosto semelhante.

:::code source="~/cognitive-services-quickstart-code/curl/face/detect.sh" ID="similar_group":::

Finalmente, detete a única face de origem que usará para combinar e guarde a sua identificação. Mantenha esta identificação separada dos outros.

:::code source="~/cognitive-services-quickstart-code/curl/face/detect.sh" ID="similar_matcher":::

### <a name="find-matches"></a>Encontrar fósforos

Copie o seguinte comando a um editor de texto.

:::code language="shell" source="~/cognitive-services-quickstart-code/curl/face/detect.sh" ID="similar":::

Em seguida, es faça as seguintes alterações:
1. Atribua `Ocp-Apim-Subscription-Key` à sua chave de subscrição Face válida.
1. Altere a primeira parte do URL de consulta para corresponder ao ponto final que corresponde à sua chave de subscrição.

Utilize o seguinte conteúdo JSON para o `body` valor:

:::code language="JSON" source="~/cognitive-services-quickstart-code/curl/face/detect.sh" ID="similar_body":::

1. Utilize o ID do rosto da fonte para `"faceId"` .
1. Cole as outras identidades faciais como termos na `"faceIds"` matriz.

### <a name="examine-the-results"></a>Examinar os resultados

Receberá uma resposta JSON que lista as identificações dos rostos que combinam com a sua face de consulta.

```json
[
    {
        "persistedFaceId" : "015839fb-fbd9-4f79-ace9-7675fc2f1dd9",
        "confidence" : 0.82
    },
    ...
] 
```

## <a name="clean-up-resources"></a>Limpar os recursos

Se pretender limpar e remover uma subscrição dos Serviços Cognitivos, pode eliminar o grupo de recursos ou recursos. A eliminação do grupo de recursos também elimina quaisquer outros recursos que lhe sejam associados.

* [Portal](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [CLI do Azure](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, aprendeu a usar a API Face REST para fazer tarefas básicas de reconhecimento facial. Em seguida, explore a documentação de referência para saber mais sobre a biblioteca.

> [!div class="nextstepaction"]
> [Referência API face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)

* [O que é o serviço Face?](../../overview.md)