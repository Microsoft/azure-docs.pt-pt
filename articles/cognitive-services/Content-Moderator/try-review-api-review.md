---
title: Criar avaliações de moderação com consola REST API - Moderador de Conteúdo
titleSuffix: Azure Cognitive Services
description: Utilize as APIs de Revisão do Moderador de Conteúdo Azure para criar revisões de imagem ou texto para moderação humana.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 03/18/2019
ms.author: pafarley
ms.openlocfilehash: 479c7c455f07d098edd327196803e85df24dfb6d
ms.sourcegitcommit: 80c1056113a9d65b6db69c06ca79fa531b9e3a00
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/09/2020
ms.locfileid: "96905149"
---
# <a name="create-human-reviews-api-console"></a>Criar comentários humanos (consola API)

[As avaliações](./review-api.md#reviews) armazenam e exibem conteúdo para moderadores humanos avaliarem. Quando um utilizador completa uma revisão, os resultados são enviados para um ponto final de retorno especificado. Neste guia, você vai aprender a configurar comentários usando o review REST APIs através da consola API. Uma vez que você entenda a estrutura das APIs, você pode facilmente levar estas chamadas para qualquer plataforma compatível com REST.

## <a name="prerequisites"></a>Pré-requisitos

- Iniciar sessão ou criar uma conta no site da [ferramenta De Revisão](https://contentmoderator.cognitive.microsoft.com/) de Moderadores de Conteúdo.

## <a name="create-a-review"></a>Criar uma análise

Para criar uma revisão, vá à página de referência da **[API e](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c4)** selecione o botão para a sua região-chave (pode encontrá-lo no URL endpoint na página **de Credenciais** da [ferramenta 'Revisão').](https://contentmoderator.cognitive.microsoft.com/) Isto inicia a consola API, onde pode facilmente construir e executar chamadas REST API.

![Comentário - Obtenha a seleção da região](images/test-drive-region.png)

### <a name="enter-rest-call-parameters"></a>Insira os parâmetros de chamada REST

Introduza valores para **o nome de equipa** e o **Ocp-Apim-Subscription-Key**:

- **nome de equipa**: O ID da equipa que criou quando criou a sua conta [de ferramenta Review](https://contentmoderator.cognitive.microsoft.com/) (encontrada no campo **Id** no ecrã de Credenciais da sua ferramenta Review).
- **Chave Ocp-Apim-Subscrição :** A tecla do Moderador de Conteúdo. Pode encontrar isto no **separador Definições** da [ferramenta 'Rever'.](https://contentmoderator.cognitive.microsoft.com)

### <a name="enter-a-review-definition"></a>Introduza uma definição de revisão

Editar a caixa **corporal Request** para introduzir o pedido JSON com os seguintes campos:

- **Metadados**: Pares de valor-chave personalizados para serem devolvidos ao seu ponto final de retorno. Se a chave for um código curto que é definido na [ferramenta 'Rever',](https://contentmoderator.cognitive.microsoft.com)aparece como uma etiqueta.
- **Conteúdo**: No caso do conteúdo de Imagem e Vídeo, trata-se de uma cadeia URL que aponta para o conteúdo. Para o conteúdo de texto, esta é a cadeia de texto real.
- **ContentId**: Uma cadeia de identificação personalizada. Esta corda é passada para a API e devolvida através da chamada. É útil para associar identificadores internos ou metadados com os resultados de um trabalho de moderação.
- **CallbackEndpoint**: (Opcional) O URL para receber informações de retorno quando a revisão estiver concluída.

O organismo de pedidos predefinidos apresenta exemplos dos diferentes tipos de avaliações que pode criar:

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
  
Selecione **Enviar**. Se a operação for bem sucedida, o **estado de Resposta** é , e a caixa de conteúdo `200 OK` **response** apresenta um ID para a revisão. Copie este ID para utilizar nos seguintes passos.

![Comentário - Criar caixa de conteúdo de resposta a consolas exibe o ID de revisão](images/test-drive-review-2.PNG)

### <a name="examine-the-new-review"></a>Examine a nova revisão

Na [ferramenta 'Rever'](https://contentmoderator.cognitive.microsoft.com)(rever o vídeo de texto de imagem de **Review**  >  **Image** / **Text** / **Video** revisão ( dependendo do conteúdo utilizado). O conteúdo que fez o upload deve aparecer, pronto para revisão humana.

![Rever imagem de ferramenta de uma bola de futebol](images/test-drive-review-5.PNG)

## <a name="get-review-details"></a>Obter detalhes da avaliação

Para obter detalhes sobre uma revisão existente, vá à página de referência [da API e](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c2) selecione o botão para a sua região (a região em que a sua chave é administrada).

![Workflow - Obtenha a seleção da região](images/test-drive-region.png)

Introduza os parâmetros de chamada REST como na secção acima. Para este passo, **reviewId** é a cadeia de identificação única que recebeu quando criou a revisão.

![Comentário - Criar consola Obtenha resultados](images/test-drive-review-3.PNG)
  
Selecione **Enviar**. Se a operação for bem sucedida, o **estado de Resposta** é , e a caixa de conteúdo `200 OK` **response** apresenta os detalhes da revisão no formato JSON, como o seguinte:

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
- **revisorResultTags**: Isto aparece se alguma etiqueta tiver sido adicionada manualmente pela equipa de revisão humana (mostrado o campo **createdBy).**
- **Metadados**: Isto mostra as etiquetas que foram inicialmente adicionadas na revisão, antes da equipa de revisão humana ter feito alterações.

## <a name="next-steps"></a>Passos seguintes

Neste guia, aprendeu a criar avaliações de moderação de conteúdos utilizando a API REST. Em seguida, integrar as análises num cenário de moderação de ponta a ponta, como o tutorial de moderação do [e-commerce.](./ecommerce-retail-catalog-moderation.md)