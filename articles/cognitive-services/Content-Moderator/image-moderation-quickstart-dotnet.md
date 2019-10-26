---
title: 'Início rápido: analisar imagens para conteúdo C# questionável Content moderator'
titleSuffix: Azure Cognitive Services
description: Como analisar o conteúdo da imagem para vários materiais censuráveis usando o SDK do Content Moderator para .NET
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: quickstart
ms.date: 10/24/2019
ms.author: pafarley
ms.openlocfilehash: 13b0952f38fb0c8c922be415f782b3a0a0861729
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/25/2019
ms.locfileid: "72931744"
---
# <a name="quickstart-analyze-images-for-objectionable-content-in-c"></a>Início rápido: analisar imagens de conteúdo censurável emC#

Este artigo apresenta informações e exemplos de código para o ajudara começar a utilizar o [SDK Content Moderator para .NET](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/). Você aprenderá a digitalizar conteúdo adulto ou erótico, texto extraído e faces humanas com o objetivo de moderar material potencialmente censurável.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar. 

## <a name="prerequisites"></a>Pré-requisitos

- A chave de uma subscrição do Content Moderator. Siga as instruções em [criar uma conta de serviços cognitivas](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) para assinar Content Moderator. Em seguida, [crie variáveis de ambiente](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) para a URL de chave e ponto de extremidade, denominada `CONTENT_MODERATOR_SUBSCRIPTION_KEY` e `CONTENT_MODERATOR_ENDPOINT`, respectivamente.
- Qualquer edição do [Visual Studio 2015 ou 2017](https://www.visualstudio.com/downloads/)


> [!NOTE]
> Este guia utiliza uma subscrição do Content Moderator no escalão gratuito. Para obter informações sobre o que cada escalão de subscrição disponibiliza, veja a página [Preços e limites](https://azure.microsoft.com/pricing/details/cognitive-services/content-moderator/).

## <a name="create-the-visual-studio-project"></a>Criar o projeto do Visual Studio

1. No Visual Studio, crie um projeto **Aplicação de Consola (.NET Framework)** novo e dê ao mesmo o nome **ImageModeration**. 
1. Se houver outros projetos na sua solução, selecione esta como o único projeto de arranque.
1. Obtenha os pacotes NuGet necessários. Clique com o botão direito do rato no seu projeto no Explorador de Soluções e selecione **Manage NuGet Packages** (Gerir Pacotes NuGet); em seguida, localize e instale os pacotes seguintes:
    - Microsoft.Azure.CognitiveServices.ContentModerator
    - Microsoft.Rest.ClientRuntime
    - Newtonsoft.Json

## <a name="add-image-moderation-code"></a>Adicionar o código da moderação de imagens

Depois, vai copiar e colar o código neste guia no projeto, para implementar um cenário de moderação de conteúdos simples.

### <a name="include-namespaces"></a>Incluir espaços de nomes

Adicione as declarações `using` seguintes à parte superior do ficheiro *Program.cs*.

[!code-csharp[](~/cognitive-services-content-moderator-samples/documentation-samples/csharp/image-moderation-quickstart-dotnet.cs?name=snippet_using)]

### <a name="create-the-content-moderator-client"></a>Criar o cliente do Content Moderator

Adicione o código seguinte ao ficheiro *Program.cs* para criar um fornecedor de cliente do Content Moderator para a sua subscrição. Adicione a classe juntamente com a classe **Program** , no mesmo namespace. Você precisará atualizar os campos **AzureBaseURL** e **CMSubscriptionKey** com os valores da URL do ponto de extremidade e da chave de assinatura. Você pode encontrá-los na guia **início rápido** do recurso na portal do Azure.

[!code-csharp[](~/cognitive-services-content-moderator-samples/documentation-samples/csharp/image-moderation-quickstart-dotnet.cs?name=snippet_client)]


### <a name="set-up-input-and-output-targets"></a>Configurar destinos de entrada e de saída

Adicione os seguintes campos estáticos à classe **Programa** em _Program.cs_. Esses campos especificam os arquivos para conteúdo de imagem de entrada e conteúdo JSON de saída.

[!code-csharp[](~/cognitive-services-content-moderator-samples/documentation-samples/csharp/image-moderation-quickstart-dotnet.cs?name=snippet_fields)]

Será necessário criar o arquivo de entrada *ImageFiles. txt* e atualizar seu caminho de acordo (caminhos relativos são relativos ao diretório de execução). Abra _ImageFiles.txt_ e adicione os URLs das imagens que vão ser moderadas. Este início rápido utiliza os URLs seguintes como o exemplo de entrada.

```
https://moderatorsampleimages.blob.core.windows.net/samples/sample2.jpg
https://moderatorsampleimages.blob.core.windows.net/samples/sample5.png
```

### <a name="create-a-class-to-handle-results"></a>Criar uma classe para processar os resultados

Adicione o código seguinte a *Program.cs*, junto à classe **Program**, no mesmo espaço de nomes. Utilize uma instância desta classe para registar os resultados de moderação de cada uma das imagens revistas.

[!code-csharp[](~/cognitive-services-content-moderator-samples/documentation-samples/csharp/image-moderation-quickstart-dotnet.cs?name=snippet_dataclass)]


### <a name="define-the-image-evaluation-method"></a>Definir o método de avaliação das imagens

Adicione o seguinte método à classe **Programa**. Este método avalia uma imagem individual de três formas diferentes e devolve os resultados da avaliação. Se quiser saber mais sobre o que cada operação individual faz, siga a ligação na secção [Passos seguintes](#next-steps).

[!code-csharp[](~/cognitive-services-content-moderator-samples/documentation-samples/csharp/image-moderation-quickstart-dotnet.cs?name=snippet_evaluate)]

### <a name="load-the-input-images"></a>Carregar as imagens de entrada

Adicione o seguinte código ao método **Main** na classe **Program**. Esse código configura o programa para recuperar dados de avaliação para cada URL de imagem no arquivo de entrada. Em seguida, escreve esses dados num ficheiro de saída individual.

[!code-csharp[](~/cognitive-services-content-moderator-samples/documentation-samples/csharp/image-moderation-quickstart-dotnet.cs?name=snippet_main)]

## <a name="run-the-program"></a>Execute o programa

O programa escreve os dados da cadeia JSON no ficheiro _ModerationOutput.json_. Os exemplos de imagens utilizados neste início rápido originam a saída abaixo. Cada imagem tem seções diferentes para `ImageModeration`, `FaceDetection` e `TextDetection`, que correspondem às três chamadas à API em seu método **EvaluateImage** .

```json
[{
  "ImageUrl": "https://moderatorsampleimages.blob.core.windows.net/samples/sample2.jpg",
  "ImageModeration": {
    "cacheID": "7733c303-3b95-4710-a41e-7a322ae81a15_636488005858745661",
    "result": false,
    "trackingId": "WE_e1f20803b4ed471fb5de7df551f5bd9f_ContentModerator.Preview_687c356d-0f00-4aeb-ae5f-c7555af80247",
    "adultClassificationScore": 0.019196987152099609,
    "isImageAdultClassified": false,
    "racyClassificationScore": 0.032390203326940536,
    "isImageRacyClassified": false,
    "advancedInfo": [
      {
        "key": "ImageDownloadTimeInMs",
        "value": "116"
      },
      {
        "key": "ImageSizeInBytes",
        "value": "273405"
      }
    ],
    "status": {
      "code": 3000.0,
      "description": "OK",
      "exception": null
    }
  },
  "TextDetection": {
    "status": {
      "code": 3000.0,
      "description": "OK",
      "exception": null
    },
    "metadata": [
      {
        "key": "ImageDownloadTimeInMs",
        "value": "12"
      },
      {
        "key": "ImageSizeInBytes",
        "value": "273405"
      }
    ],
    "trackingId": "WE_e1f20803b4ed471fb5de7df551f5bd9f_ContentModerator.Preview_814fa162-c5d6-4ca6-997b-30ed0686ca83",
    "cacheId": "3fb69496-c64b-4de9-affd-6dd6d23f3e78_636488005876558920",
    "language": "eng",
    "text": "IF WE DID \r\nALL \r\nTHE THINGS \r\nWE ARE \r\nCAPABLE \r\nOF DOING, \r\nWE WOULD \r\nLITERALLY \r\nASTOUND \r\nOURSELVE \r\n",
    "candidates": []
  },
  "FaceDetection": {
    "status": {
      "code": 3000.0,
      "description": "OK",
      "exception": null
    },
    "trackingId": "WE_e1f20803b4ed471fb5de7df551f5bd9f_ContentModerator.Preview_a2c40dbe-609d-4eb8-b01c-9988388804ea",
    "cacheId": "e4c0b500-ea8e-4a31-8fb3-35f98c4fbd65_636488005889528303",
    "result": false,
    "count": 0,
    "advancedInfo": [
      {
        "key": "ImageDownloadTimeInMs",
        "value": "11"
      },
      {
        "key": "ImageSizeInBytes",
        "value": "273405"
      }
    ],
    "faces": []
  }
},
{
  "ImageUrl": "https://moderatorsampleimages.blob.core.windows.net/samples/sample5.png",
  "ImageModeration": {
    "cacheID": "b4866aa2-5e69-44ed-806a-f9a5d618c8ae_636488005930693926",
    "result": false,
    "trackingId": "WE_e1f20803b4ed471fb5de7df551f5bd9f_ContentModerator.Preview_fdce5510-f689-4791-b081-c2ad54dcfe78",
    "adultClassificationScore": 0.0035635426174849272,
    "isImageAdultClassified": false,
    "racyClassificationScore": 0.021369094029068947,
    "isImageRacyClassified": false,
    "advancedInfo": [
      {
        "key": "ImageDownloadTimeInMs",
        "value": "109"
      },
      {
        "key": "ImageSizeInBytes",
        "value": "2278902"
      }
    ],
    "status": {
      "code": 3000.0,
      "description": "OK",
      "exception": null
    }
  },
  "TextDetection": {
    "status": {
      "code": 3000.0,
      "description": "OK",
      "exception": null
    },
    "metadata": [
      {
        "key": "ImageDownloadTimeInMs",
        "value": "46"
      },
      {
        "key": "ImageSizeInBytes",
        "value": "2278902"
      }
    ],
    "trackingId": "WE_e1f20803b4ed471fb5de7df551f5bd9f_ContentModerator.Preview_08a4bc19-6010-41bb-a440-a77278e167d8",
    "cacheId": "28b37471-41b3-4f79-bd23-965498bcff51_636488005950851288",
    "language": "eng",
    "text": "",
    "candidates": []
  },
  "FaceDetection": {
    "status": {
      "code": 3000.0,
      "description": "OK",
      "exception": null
    },
    "trackingId": "WE_e1f20803b4ed471fb5de7df551f5bd9f_ContentModerator.Preview_40f2ce07-14ba-47cd-ba09-58b557a89854",
    "cacheId": "ec9c1be3-99b7-4bd9-8bc4-dc958c74459f_636488005964914299",
    "result": true,
    "count": 6,
    "advancedInfo": [
      {
        "key": "ImageDownloadTimeInMs",
        "value": "60"
      },
      {
        "key": "ImageSizeInBytes",
        "value": "2278902"
      }
    ],
    "faces": [
      {
        "bottom": 598,
        "left": 44,
        "right": 268,
        "top": 374
      },
      {
        "bottom": 620,
        "left": 308,
        "right": 532,
        "top": 396
      },
      {
        "bottom": 575,
        "left": 594,
        "right": 773,
        "top": 396
      },
      {
        "bottom": 563,
        "left": 812,
        "right": 955,
        "top": 420
      },
      {
        "bottom": 611,
        "left": 972,
        "right": 1151,
        "top": 432
      },
      {
        "bottom": 510,
        "left": 1232,
        "right": 1456,
        "top": 286
      }
    ]
  }
}]
```

## <a name="next-steps"></a>Passos seguintes

Neste início rápido, desenvolveu uma aplicação .NET simples que utiliza o serviço Content Moderator para devolver informações relevantes sobre um determinado exemplo de imagem. Em seguida, saiba o que querem dizer os diferentes sinalizadores e as diferentes classificações, para determinar de que dados precisa e como é que a sua aplicação os deve processar.

> [!div class="nextstepaction"]
> [Image moderation guide](image-moderation-api.md) (Guia de moderação de imagens)
