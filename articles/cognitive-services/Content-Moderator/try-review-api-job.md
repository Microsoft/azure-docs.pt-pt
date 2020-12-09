---
title: Utilize trabalhos de moderação com a consola REST API - Moderador de Conteúdo
titleSuffix: Azure Cognitive Services
description: Utilize as operações de trabalho da API de revisão para iniciar trabalhos de moderação de conteúdo de ponta a ponta para conteúdos de imagem ou texto no Azure Content Moderador.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: pafarley
ms.openlocfilehash: 924c21037a464770fac13c9b45ddcf261ff5a058
ms.sourcegitcommit: 80c1056113a9d65b6db69c06ca79fa531b9e3a00
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/09/2020
ms.locfileid: "96905181"
---
# <a name="define-and-use-moderation-jobs-api-console"></a>Definir e utilizar trabalhos de moderação (consola API)

Um trabalho de moderação serve como uma espécie de invólucro para a funcionalidade de moderação de conteúdos, fluxos de trabalho e avaliações. Este guia mostra-lhe como utilizar o trabalho REST APIs para iniciar e verificar trabalhos de moderação de conteúdos. Uma vez que você entenda a estrutura das APIs, você pode facilmente levar estas chamadas para qualquer plataforma compatível com REST.

## <a name="prerequisites"></a>Pré-requisitos

- Iniciar sessão ou criar uma conta no site da [ferramenta De Revisão](https://contentmoderator.cognitive.microsoft.com/) de Moderadores de Conteúdo.
- (Opcional) [Defina um fluxo de trabalho personalizado](./Review-Tool-User-Guide/Workflows.md) para utilizar com o seu trabalho; também pode utilizar o fluxo de trabalho predefinido.

## <a name="create-a-job"></a>Criar uma tarefa

Para criar um trabalho de moderação, vá à página de referência [Job - Create](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c5) API e selecione o botão para a sua região de subscrição. Pode encontrar a sua região no URL endpoint na página **de Credenciais** da [ferramenta Review](https://contentmoderator.cognitive.microsoft.com/). Isto inicia a consola API, onde pode facilmente construir e executar chamadas REST API.

![Job - Criar seleção da região da página](images/test-drive-job-1.png)

### <a name="enter-rest-call-parameters"></a>Insira os parâmetros de chamada REST

Introduza os seguintes valores para construir a chamada REST:

- **nome de equipa**: O ID da equipa que criou quando criou a sua conta [de ferramenta Review](https://contentmoderator.cognitive.microsoft.com/) (encontrada no campo **Id** no ecrã de Credenciais da sua ferramenta Review).
- **ConteúdoType**: Isto pode ser "Imagem", "Texto" ou "Vídeo".
- **ContentId**: Uma cadeia de identificação personalizada. Esta corda é passada para a API e devolvida através da chamada. É útil para associar identificadores internos ou metadados com os resultados de um trabalho de moderação.
- **Workflowne**: O nome do fluxo de trabalho que criou anteriormente (ou "predefinido" para o fluxo de trabalho predefinido).
- **CallbackEndpoint**: (Opcional) O URL para receber informações de retorno quando a revisão estiver concluída.
- **Chave Ocp-Apim-Subscrição :** A tecla do Moderador de Conteúdo. Pode encontrar esta chave no **separador Definições** da [ferramenta 'Rever'.](https://contentmoderator.cognitive.microsoft.com)

### <a name="fill-in-the-request-body"></a>Preencha o corpo de pedido

O corpo da sua chamada REST contém um campo, **ContentValue**. Cole no conteúdo de texto em bruto se estiver a moderar texto, ou introduzir um URL de imagem ou vídeo se estiver a moderar uma imagem ou vídeo. Pode utilizar o seguinte URL de imagem de amostra: [https://moderatorsampleimages.blob.core.windows.net/samples/sample2.jpg](https://moderatorsampleimages.blob.core.windows.net/samples/sample2.jpg)

![Job - Criar parâmetros de consulta de consola, cabeçalhos e caixa corporal Request](images/job-api-console-inputs.PNG)

### <a name="submit-your-request"></a>Submeter o pedido

Selecione **Enviar**. Se a operação for bem sucedida, o **estado de Resposta** é , e a caixa de conteúdo `200 OK` **response** apresenta um ID para o trabalho. Copie este ID para utilizar nos seguintes passos.

![Comentário - Criar caixa de conteúdo de resposta a consolas exibe o ID de revisão](images/test-drive-job-3.PNG)

## <a name="get-job-status"></a>Obter estatuto de emprego

Para obter o estado e detalhes de um trabalho em execução ou concluído, vá à página de referência [Job - Get](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c3) API e selecione o botão para a sua região (a região em que a sua chave é administrada).

![Job - Obter seleção da região](images/test-drive-region.png)

Introduza os parâmetros de chamada REST como na secção acima. Para este passo, **jobId** é a cadeia de identificação única que recebeu quando criou o trabalho. Selecione **Enviar**. Se a operação for bem sucedida, o **estado de Resposta** é , e a caixa de conteúdo `200 OK` **response** apresenta o trabalho no formato JSON, como o seguinte:

```json
{  
  "Id":"2018014caceddebfe9446fab29056fd8d31ffe",
  "TeamName":"some team name",
  "Status":"Complete",
  "WorkflowId":"OCR",
  "Type":"Image",
  "CallBackEndpoint":"",
  "ReviewId":"201801i28fc0f7cbf424447846e509af853ea54",
  "ResultMetaData":[  
    {  
      "Key":"hasText",
      "Value":"True"
    },
    {  
      "Key":"ocrText",
      "Value":"IF WE DID \r\nALL \r\nTHE THINGS \r\nWE ARE \r\nCAPABLE \r\nOF DOING, \r\nWE WOULD \r\nLITERALLY \r\nASTOUND \r\nOURSELVE \r\n"
    }
  ],
  "JobExecutionReport":[  
    {  
      "Ts":"2018-01-07T00:38:29.3238715",
      "Msg":"Posted results to the Callbackendpoint: https://requestb.in/vxke1mvx"
    },
    {  
      "Ts":"2018-01-07T00:38:29.2928416",
      "Msg":"Job marked completed and job content has been removed"
    },
    {  
      "Ts":"2018-01-07T00:38:29.0856472",
      "Msg":"Execution Complete"
    },
    {  
      "Ts":"2018-01-07T00:38:26.7714671",
      "Msg":"Successfully got hasText response from Moderator"
    },
    {  
      "Ts":"2018-01-07T00:38:26.4181346",
      "Msg":"Getting hasText from Moderator"
    },
    {  
      "Ts":"2018-01-07T00:38:25.5122828",
      "Msg":"Starting Execution - Try 1"
    }
  ]
}
```

![Job - Get REST call response](images/test-drive-job-5.png)

### <a name="examine-the-new-reviews"></a>Examinar a nova revisão(s)

Se o seu trabalho de conteúdo resultou na criação de uma revisão, pode vê-lo na [ferramenta 'Revisão'.](https://contentmoderator.cognitive.microsoft.com) Selecione **Rever**  >  **o** Vídeo de Texto de Imagem / **Text** / **Video** (dependendo do conteúdo utilizado). O conteúdo deve aparecer, pronto para revisão humana. Após um moderador humano rever as etiquetas autoatribuídas e os dados de previsão e submeter uma decisão final de moderação, a API de empregos submete toda esta informação ao ponto final designado de retorno de chamada.

## <a name="next-steps"></a>Passos seguintes

Neste guia, aprendeu a criar e consultar trabalhos de moderação de conteúdos utilizando a API REST. Em seguida, integrar os postos de trabalho num cenário de moderação de ponta a ponta, como o tutorial de moderação do [comércio eletrónico.](./ecommerce-retail-catalog-moderation.md)