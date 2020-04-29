---
title: Criar comentários de moderação com consola REST API - Moderador de Conteúdo
titleSuffix: Azure Cognitive Services
description: Utilize as APIs de revisão de moderador de conteúdo azure para criar análises de imagem ou texto para moderação humana.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 03/18/2019
ms.author: pafarley
ms.openlocfilehash: a9726e41a84926d00d48b51e31f534a3d8c2fe0c
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "72757146"
---
# <a name="create-human-reviews-rest"></a>Criar avaliações humanas (REST)

[Comentários](./review-api.md#reviews) armazenam e exibem conteúdo para moderadores humanos para avaliar. Quando um utilizador completa uma revisão, os resultados são enviados para um ponto final de chamada especificado. Neste guia, aprenderás a configurar comentários utilizando a análise REST APIs através da consola API. Assim que compreender a estrutura das APIs, pode facilmente fazer com que estas chamadas possam fazer qualquer plataforma compatível com REST.

## <a name="prerequisites"></a>Pré-requisitos

- Inscreva-se ou crie uma conta no site da ferramenta Content Moderator [Review.](https://contentmoderator.cognitive.microsoft.com/)

## <a name="create-a-review"></a>Criar uma revisão

Para criar uma revisão, vá à página de referência **['Rever - Criar](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c4)** API' e selecionar o botão para a sua região-chave (pode encontrá-lo no URL endpoint na página **de Credenciais** da [ferramenta 'Revisão).](https://contentmoderator.cognitive.microsoft.com/) Isto inicia a consola API, onde podes facilmente construir e executar chamadas REST API.

![Comentário - Obtenha a seleção da região](images/test-drive-region.png)

### <a name="enter-rest-call-parameters"></a>Insira os parâmetros de chamada REST

Introduza valores para **nome de equipa**e Chave de Assinatura **Ocp-Apim:**

- **nome de equipa**: O ID da equipa que criou quando configura a sua conta de [ferramentas 'Rever'](https://contentmoderator.cognitive.microsoft.com/) (encontrada no campo **Id** no ecrã de Credenciais da ferramenta De revisão).
- **Chave de subscrição Ocp-Apim:** A sua tecla moderadora de conteúdo. Pode encontrá-lo no separador **Definições** da [ferramenta Rever](https://contentmoderator.cognitive.microsoft.com).

### <a name="enter-a-review-definition"></a>Insira uma definição de revisão

Editar a caixa **de corpo de Pedido** para introduzir o pedido JSON com os seguintes campos:

- **Metadados**: Pares de valor de chave personalizados para serem devolvidos ao seu ponto final de chamada. Se a chave for um código curto que é definido na [ferramenta 'Revisão',](https://contentmoderator.cognitive.microsoft.com)aparece como uma etiqueta.
- **Conteúdo**: No caso do conteúdo de Imagem e Vídeo, esta é uma cadeia de URL que aponta para o conteúdo. Para o conteúdo de texto, esta é a cadeia de texto real.
- **ContentId**: Uma cadeia de identificador personalizado. Esta corda é passada para a API e devolvida através da chamada. É útil para associar identificadores internos ou metadados com os resultados de um trabalho de moderação.
- **CallbackEndpoint**: (Opcional) O URL para receber informações de chamada quando a revisão estiver concluída.

O organismo de pedido predefinido mostra exemplos dos diferentes tipos de comentários que pode criar:

```json
[Image]
[
  {
    "Metadata": [
      {
        "Key": "string",
        "Value": "string"
      }
    ],
    "Type": "Image",
    "Content": "<Content Url>",
    "ContentId": "<Your identifier for this content>",
    "CallbackEndpoint": "<Url where you would receive callbacks>"
  }
]
[Text]
[
  {
    "Metadata": [
      {
        "Key": "string",
        "Value": "string"
      }
    ],
    "Type": "Text",
    "Content": "<Your Text Content>",
    "ContentId": "<Your identifier for this content>",
    "CallbackEndpoint": "<Url where you would receive callbacks>"
  }
]
[Video]
[
  {
    "VideoFrames":[
      {
          "Id": "<Frame Id>",
          "Timestamp": "<Frame Timestamp",
          "FrameImage":"<Frame Image URL",
          "Metadata": [
            {
              "Key": "<Key>",
              "Value": "<Value"
            }
          ],
          "ReviewerResultTags": [
          ]
    ], 
    "Metadata": [
      {
        "Key": "string",
        "Value": "string"
      },
      //For encrypted Videos
        {
          "Key": "protectedType",
          "Value": "AES or FairPlay or Widevine or Playready"
        },
        {
          "Key": "authenticationToken",
          "Value": "your viewtoken(In case of Video Indexer AES encryption type, this value is viewtoken from breakdown json)"
        },
      //For FairPlay encrypted type video include certificateUrl as well
        {
          "Key": "certificateUrl",
          "Value": "your certificate url"
        }
    ],
    "Type": "Video",
    "Content": "<Stream Url>",
    "ContentId": "<Your identifier for this content>",
    "CallbackEndpoint": "<Url where you would receive callbacks>",
    [Optional]
    "Timescale": "<Timescale of the video>
  }
]
```

### <a name="submit-your-request"></a>Submeta o seu pedido
  
Selecione **Enviar**. Se a operação for bem `200 OK`sucedida, o estado **resposta** é , e a caixa de conteúdo **resposta** apresenta um ID para a revisão. Copie este ID para utilizar nos seguintes passos.

![Review - Create console Response content box displays the review ID](images/test-drive-review-2.PNG)

### <a name="examine-the-new-review"></a>Examinar a nova revisão

Na [ferramenta Rever](https://contentmoderator.cognitive.microsoft.com), selecione **Reveja** > o**Vídeo** **de Texto**/**de Imagem**/(dependendo do conteúdo utilizado). O conteúdo que carregou deve aparecer, pronto para revisão humana.

![Imagem de ferramenta de revisão de uma bola de futebol](images/test-drive-review-5.PNG)

## <a name="get-review-details"></a>Obtenha detalhes da revisão

Para obter detalhes sobre uma revisão existente, vá à página de referência [Review - Obtenha](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c2) API e selecione o botão para a sua região (a região em que a sua chave é administrada).

![Workflow - Obtenha a seleção da região](images/test-drive-region.png)

Introduza os parâmetros de chamada REST como na secção acima. Para este passo, **o reviewId** é o único fio de identificação que recebeu quando criou a revisão.

![Comentário - Criar resultados de consola](images/test-drive-review-3.PNG)
  
Selecione **Enviar**. Se a operação for bem `200 OK`sucedida, o estado **resposta** é , e a caixa de conteúdo **resposta** apresenta os detalhes de revisão no formato JSON, como o seguinte:

```json
{  
  "reviewId":"201712i46950138c61a4740b118a43cac33f434",
  "subTeam":"public",
  "status":"Complete",
  "reviewerResultTags":[  
    {  
      "key":"a",
      "value":"False"
    },
    {  
      "key":"r",
      "value":"True"
    },
    {  
      "key":"sc",
      "value":"True"
    }
  ],
  "createdBy":"<teamname>",
  "metadata":[  
    {  
      "key":"sc",
      "value":"true"
    }
  ],
  "type":"Image",
  "content":"https://reviewcontentprod.blob.core.windows.net/<teamname>/IMG_201712i46950138c61a4740b118a43cac33f434",
  "contentId":"0",
  "callbackEndpoint":"<callbackUrl>"
}
```

Tome nota dos seguintes campos na resposta:

- **estado**
- **revisorResultTags**: Isto aparece se alguma tag tiver sido adicionada manualmente pela equipa de revisão humana (mostrada o campo **criadoPor).**
- **metadados**: Isto mostra as etiquetas que foram inicialmente adicionadas na revisão, antes da equipa de revisão humana fazer alterações.

## <a name="next-steps"></a>Passos seguintes

Neste guia, aprendeu a criar comentários de moderação de conteúdos utilizando a API REST. Em seguida, integre as críticas num cenário de moderação de ponta a ponta, como o tutorial de moderação do [e-commerce.](./ecommerce-retail-catalog-moderation.md)