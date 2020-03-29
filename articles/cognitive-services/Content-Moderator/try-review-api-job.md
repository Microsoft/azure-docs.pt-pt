---
title: Utilize trabalhos de moderação com a consola REST API - Moderador de Conteúdo
titleSuffix: Azure Cognitive Services
description: Utilize as operações de trabalho da API de revisão para iniciar trabalhos de moderação de conteúdo extremo-a-extremo para conteúdos de imagem ou texto em Azure Content Moderator.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: pafarley
ms.openlocfilehash: 83ee8e0c0583cba72da8702e196f0f38128f8d8a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "72935961"
---
# <a name="define-and-use-moderation-jobs-rest"></a>Defina e use trabalhos de moderação (REST)

Um trabalho de moderação serve como uma espécie de invólucro para a funcionalidade de moderação de conteúdos, fluxos de trabalho e revisões. Este guia mostra-lhe como usar o trabalho REST APIs para iniciar e verificar os trabalhos de moderação de conteúdos. Assim que compreender a estrutura das APIs, pode facilmente fazer com que estas chamadas possam fazer qualquer plataforma compatível com REST.

## <a name="prerequisites"></a>Pré-requisitos

- Inscreva-se ou crie uma conta no site da ferramenta Content Moderator [Review.](https://contentmoderator.cognitive.microsoft.com/)
- (Opcional) [Defina um fluxo de trabalho personalizado](./Review-Tool-User-Guide/Workflows.md) para usar com o seu trabalho; pode também utilizar o fluxo de trabalho por defeito.

## <a name="create-a-job"></a>Criar uma tarefa

Para criar um trabalho de moderação, vá à página de referência [Job - Create](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c5) API e selecione o botão para a sua região de subscrição (pode encontrá-lo no URL endpoint na página de **Credenciais** da [ferramenta 'Revisão'](https://contentmoderator.cognitive.microsoft.com/)). Isto inicia a consola API, onde podes facilmente construir e executar chamadas REST API.

![Job - Criar a seleção da região de página](images/test-drive-job-1.png)

### <a name="enter-rest-call-parameters"></a>Insira os parâmetros de chamada REST

Introduza os seguintes valores para construir a chamada REST:

- **nome de equipa**: O ID da equipa que criou quando configura a sua conta de [ferramentas 'Rever'](https://contentmoderator.cognitive.microsoft.com/) (encontrada no campo **Id** no ecrã de Credenciais da ferramenta De revisão).
- **ConteúdoType**: Isto pode ser "Imagem", "Texto" ou "Vídeo".
- **ContentId**: Uma cadeia de identificador personalizado. Esta corda é passada para a API e devolvida através da chamada. É útil para associar identificadores internos ou metadados com os resultados de um trabalho de moderação.
- **Nome**de fluxo de trabalho : O nome do fluxo de trabalho que criou anteriormente (ou "padrão" para o fluxo de trabalho predefinido).
- **CallbackEndpoint**: (Opcional) O URL para receber informações de chamada quando a revisão estiver concluída.
- **Chave de subscrição Ocp-Apim:** A sua tecla moderadora de conteúdo. Pode encontrá-lo no separador **Definições** da [ferramenta Rever](https://contentmoderator.cognitive.microsoft.com).

### <a name="fill-in-the-request-body"></a>Preencha o órgão de pedido

O corpo da sua chamada REST contém um campo, **ContentValue**. Colhe no conteúdo de texto bruto se estiver a moderar texto, ou introduza um URL de imagem ou vídeo se estiver a moderar a imagem/vídeo. Pode utilizar o seguinte URL de imagem da amostra:[https://moderatorsampleimages.blob.core.windows.net/samples/sample2.jpg](https://moderatorsampleimages.blob.core.windows.net/samples/sample2.jpg)

![Job - Criar parâmetros de consulta de consola, cabeçalhos e caixa corporal de pedido](images/job-api-console-inputs.PNG)

### <a name="submit-your-request"></a>Submeta o seu pedido

Selecione **Enviar**. Se a operação for bem `200 OK`sucedida, o estado de **Resposta** é , e a caixa de conteúdo **resposta** apresenta um ID para o trabalho. Copie este ID para utilizar nos seguintes passos.

![Review - Create console Response content box displays the review ID](images/test-drive-job-3.PNG)

## <a name="get-job-status"></a>Obter o estatuto de emprego

Para obter o estado e os detalhes de um trabalho em execução ou concluído, vá à página de referência [De Trabalho - Obtenha](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c3) API e selecione o botão para a sua região (a região em que a sua chave é administrada).

![Job - Obtenha a seleção da região](images/test-drive-region.png)

Introduza os parâmetros de chamada REST como na secção acima. Para este passo, **jobId** é a cadeia de identificação única que recebeu quando criou o trabalho. Selecione **Enviar**. Se a operação for bem `200 OK`sucedida, o estado de **Resposta** é , e a caixa de conteúdo **resposta** apresenta o trabalho em formato JSON, como o seguinte:

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

![Job - Obter resposta de chamada REST](images/test-drive-job-5.png)

### <a name="examine-the-new-reviews"></a>Examinar a nova revisão(s)

Se o seu trabalho de conteúdo resultou na criação de uma revisão, pode vê-lo na [ferramenta Review](https://contentmoderator.cognitive.microsoft.com). Selecione **Revê** > **o Vídeo** **de Texto**/de**Imagem**/(dependendo do conteúdo utilizado). O conteúdo deve aparecer, pronto para revisão humana. Depois de um moderador humano rever as etiquetas e dados de previsão atribuídos automaticamente e apresentar uma decisão final de moderação, os postos de trabalho a API submete todas estas informações ao ponto final designado.

## <a name="next-steps"></a>Passos seguintes

Neste guia, aprendeu a criar e consultar trabalhos de moderação de conteúdos utilizando a API REST. Em seguida, integrar os empregos num cenário de moderação de ponta a ponta, como o tutorial de moderação do [comércio eletrónico.](./ecommerce-retail-catalog-moderation.md)