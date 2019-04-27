---
title: Utilizar tarefas de moderação com a consola de REST API - Content Moderator
titlesuffix: Azure Cognitive Services
description: Utilize operações de tarefa a API de revisão para iniciar tarefas de moderação de conteúdos do ponto-a-ponto para o conteúdo de imagem ou texto no Azure Content Moderator.
services: cognitive-services
author: sanjeev3
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: article
ms.date: 03/18/2019
ms.author: sajagtap
ms.openlocfilehash: 7827cee2af2dfc0c1fddc407c1d146dc9a66c514
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60607597"
---
# <a name="define-and-use-moderation-jobs-rest"></a>Definir e utilizar tarefas de moderação (REST)

Uma tarefa de moderação serve como uma espécie de invólucro para a funcionalidade de moderação de conteúdos, os fluxos de trabalho e revisões. Este guia mostra-lhe como utilizar a tarefa de REST APIs para iniciar e verificar as tarefas de moderação de conteúdos. Assim que compreender a estrutura das APIs, poderá transportar facilmente essas chamadas para qualquer plataforma compatível com REST.

## <a name="prerequisites"></a>Pré-requisitos

- Inicie sessão ou crie uma conta no Content Moderator [ferramenta de revisão](https://contentmoderator.cognitive.microsoft.com/) site.
- (Opcional) [Definir um fluxo de trabalho personalizado](./Review-Tool-User-Guide/Workflows.md) para utilizar com o seu trabalho; também pode utilizar o fluxo de trabalho predefinida.

## <a name="create-a-job"></a>Criar uma tarefa

Para criar uma tarefa de moderação, vá para o [da tarefa - criar](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c5) API de página e selecione o botão para a sua região de chave (pode encontrar estas informações no URL do ponto final no **credenciais** página do [revisão ferramenta](https://contentmoderator.cognitive.microsoft.com/)). Esta ação inicia a consola de API, onde pode facilmente criar e executar chamadas de REST API.

![Tarefa - criar a seleção de região de página](images/test-drive-job-1.png)

### <a name="enter-rest-call-parameters"></a>Introduza os parâmetros da chamada REST

Introduza os seguintes valores para construir a chamada REST:

- **teamName**: O ID da equipa que criou quando configurou seu [ferramenta de revisão](https://contentmoderator.cognitive.microsoft.com/) conta (encontrada no **Id** campo no ecrã de credenciais da sua ferramenta de revisão).
- **ContentType**: Isso pode ser "Image", "Text" ou "Video".
- **ContentId**: Uma cadeia de caracteres de identificador personalizado. Essa cadeia de caracteres é passada para a API e devolvida pelo retorno de chamada. É útil para associar os resultados de uma tarefa de moderação identificadores internos ou de metadados.
- **workflowname**: O nome do fluxo de trabalho que criou anteriormente (ou "predefinição" para o fluxo de trabalho padrão).
- **CallbackEndpoint**: (Opcional) O URL para receber informações de retorno de chamada quando a revisão é concluída.
- **Ocp-Apim-Subscription-Key**: A chave do Content Moderator. Pode encontrar isto na **definições** separador da [ferramenta de revisão](https://contentmoderator.cognitive.microsoft.com).

### <a name="fill-in-the-request-body"></a>Preencher o corpo do pedido

O corpo da sua chamada REST contém um campo, **ContentValue**. Colar o conteúdo de texto não processado se é moderadores texto ou introduza um URL do vídeo ou imagem, se estiver moderadores imagem e vídeo. Pode utilizar o URL de imagem de exemplo seguinte: [https://moderatorsampleimages.blob.core.windows.net/samples/sample2.jpg](https://moderatorsampleimages.blob.core.windows.net/samples/sample2.jpg)

![Tarefa - criar parâmetros de consulta da consola, cabeçalhos e caixa de corpo de pedido](images/job-api-console-inputs.PNG)

### <a name="submit-your-request"></a>Submeter o pedido

Selecione **Enviar**. Se a operação for bem-sucedida, o **estado de resposta** é `200 OK`e o **conteúdo da resposta** caixa apresenta um ID da tarefa. Copie este ID para utilizar nos passos seguintes.

![Examine - criar console caixa apresenta o ID da revisão de conteúdo de resposta](images/test-drive-job-3.PNG)

## <a name="get-job-status"></a>Obter estado da tarefa

Para obter o estado e os detalhes de uma tarefa em execução ou concluída, vá para o [da tarefa - obter](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c3) página de referência de API e selecione o botão para a sua região (a região em que a chave é administrada).

![Tarefa - seleção de região de Get](images/test-drive-region.png)

Introduza os parâmetros da chamada REST como na seção acima. Para este passo, **JobId** é a cadeia de ID exclusiva que recebeu quando criou a tarefa. Selecione **Enviar**. Se a operação for bem-sucedida, o **estado de resposta** é `200 OK`e o **conteúdo da resposta** caixa apresenta a tarefa no formato JSON, semelhante ao seguinte:

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

![Tarefa - obter resposta de chamada REST](images/test-drive-job-5.png)

### <a name="examine-the-new-reviews"></a>Examine o review(s) novo

Se o seu trabalho conteúdo resultou na criação de uma revisão, pode vê-la na [ferramenta de revisão](https://contentmoderator.cognitive.microsoft.com). Selecione **revisão** > **imagem**/**texto**/**vídeo** (consoante o que de conteúdo utilizado). O conteúdo deverá aparecer, pronto para revisão humana. Depois de um moderador humano revisa o atribuída automaticamente etiquetas e dados de predição e envia uma decisão de moderação final, a API de tarefas envia todas essas informações para o ponto de final de ponto de extremidade do retorno de chamada designado.

## <a name="next-steps"></a>Passos Seguintes

Neste guia, aprendeu a criar e consultar tarefas de moderação de conteúdos através da API REST. Em seguida, integrar, tais como tarefas num cenário de moderação de ponto-a-ponto, o [moderação de comércio eletrônico](./ecommerce-retail-catalog-moderation.md) tutorial.