---
title: Criar as revisões de moderação com a consola de REST API - Content Moderator
titlesuffix: Azure Cognitive Services
description: Utilize as APIs de revisão de moderador conteúdos do Azure para criar as revisões de imagem ou texto de moderação humana.
services: cognitive-services
author: sanjeev3
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: article
ms.date: 03/18/2019
ms.author: sajagtap
ms.openlocfilehash: 254269ccedc92b9dfc164cc4665a8a8513682773
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60607498"
---
# <a name="create-human-reviews-rest"></a>Criar revisões realizadas por pessoas (REST)

[Revisões](./review-api.md#reviews) armazenar e exibir o conteúdo de moderadores humanos avaliar. Quando um utilizador é concluída uma revisão, os resultados são enviados para um ponto de extremidade do retorno de chamada especificado. Neste guia, aprenderá como configurar a revisões usando as APIs de REST de revisão através da consola de API. Assim que compreender a estrutura das APIs, poderá transportar facilmente essas chamadas para qualquer plataforma compatível com REST.

## <a name="prerequisites"></a>Pré-requisitos

- Inicie sessão ou crie uma conta no Content Moderator [ferramenta de revisão](https://contentmoderator.cognitive.microsoft.com/) site.

## <a name="create-a-review"></a>Criar uma revisão

Para criar uma revisão, vá para o **[examine - criar](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c4)** API página e selecione o botão para a sua região de chave (pode encontrar estas informações no URL do ponto final no **credenciais** página do [ferramenta de revisão](https://contentmoderator.cognitive.microsoft.com/)). Esta ação inicia a consola de API, onde pode facilmente criar e executar chamadas de REST API.

![Examine - seleção de região de Get](images/test-drive-region.png)

### <a name="enter-rest-call-parameters"></a>Introduza os parâmetros da chamada REST

Introduza os valores para **teamName**, e **Ocp-Apim-Subscription-Key**:

- **teamName**: O ID da equipa que criou quando configurou seu [ferramenta de revisão](https://contentmoderator.cognitive.microsoft.com/) conta (encontrada no **Id** campo no ecrã de credenciais da sua ferramenta de revisão).
- **Ocp-Apim-Subscription-Key**: A chave do Content Moderator. Pode encontrar isto na **definições** separador da [ferramenta de revisão](https://contentmoderator.cognitive.microsoft.com).

### <a name="enter-a-review-definition"></a>Introduza uma definição de revisão

Editar a **corpo do pedido** caixa para introduzir o pedido do JSON com os seguintes campos:

- **Metadados**: Pares chave-valor personalizados a serem retornados para o ponto final de retorno de chamada. Se a chave é um código curto definido na [ferramenta de revisão](https://contentmoderator.cognitive.microsoft.com), é apresentado como uma etiqueta.
- **Conteúdo**: No caso de conteúdo de imagem e vídeo, esta é uma cadeia de URL que aponta para o conteúdo. Para conteúdo de texto, esta é a cadeia de texto real.
- **ContentId**: Uma cadeia de caracteres de identificador personalizado. Essa cadeia de caracteres é passada para a API e devolvida pelo retorno de chamada. É útil para associar os resultados de uma tarefa de moderação identificadores internos ou de metadados.
- **CallbackEndpoint**: (Opcional) O URL para receber informações de retorno de chamada quando a revisão é concluída.

O corpo do pedido de predefinição mostra exemplos dos tipos diferentes de revisões que pode criar:

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

### <a name="submit-your-request"></a>Submeter o pedido
  
Selecione **Enviar**. Se a operação for bem-sucedida, o **estado de resposta** é `200 OK`e o **conteúdo da resposta** caixa apresenta um ID para a revisão. Copie este ID para utilizar nos passos seguintes.

![Examine - criar console caixa apresenta o ID da revisão de conteúdo de resposta](images/test-drive-review-2.PNG)

### <a name="examine-the-new-review"></a>Examine a nova revisão

Na [ferramenta de revisão](https://contentmoderator.cognitive.microsoft.com), selecione **revisão** > **imagem**/**texto** / **Vídeo** (consoante o conteúdo que utilizou). Deve aparecer o conteúdo que carregou, pronta para revisão humana.

![Imagem de ferramenta de revisão de uma bola de futebol](images/test-drive-review-5.PNG)

## <a name="get-review-details"></a>Obter os detalhes de revisão

Para obter detalhes sobre uma revisão de existente, vá para o [examine - obter](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c2) página de referência de API e selecione o botão para a sua região (a região em que a chave é administrada).

![Fluxo de trabalho - seleção de região de Get](images/test-drive-region.png)

Introduza os parâmetros da chamada REST como na seção acima. Para este passo, **reviewId** é a cadeia de ID exclusiva que recebeu quando criou a revisão.

![Examine - criar console resultados de Get](images/test-drive-review-3.PNG)
  
Selecione **Enviar**. Se a operação for bem-sucedida, o **estado de resposta** é `200 OK`e o **conteúdo da resposta** caixa apresenta os detalhes de revisão no formato JSON, semelhante ao seguinte:

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

- **status**
- **reviewerResultTags**: Esta opção é apresentada se as etiquetas que foram adicionadas manualmente pela equipa de revisão humana (mostrada a **createdBy** campo).
- **metadata**: Isso mostra as marcas que foram adicionadas inicialmente da revisão, antes das alterações feita de equipa de revisão humana.

## <a name="next-steps"></a>Passos Seguintes

Neste guia, aprendeu a criar as revisões de moderação de conteúdos através da API REST. Em seguida, integrar, como revisões num cenário de moderação de ponto-a-ponto, o [moderação de comércio eletrônico](./ecommerce-retail-catalog-moderation.md) tutorial.