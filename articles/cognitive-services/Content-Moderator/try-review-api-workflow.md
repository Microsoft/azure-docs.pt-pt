---
title: Defina fluxos de trabalho de moderação com a consola REST API - Moderador de Conteúdo
titleSuffix: Azure Cognitive Services
description: Pode utilizar as APIs de Revisão de Moderador de Conteúdo Azure para definir fluxos de trabalho e limiares personalizados com base nas suas políticas de conteúdo.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 03/14/2019
ms.author: pafarley
ms.openlocfilehash: 3e58be4b94457d95d28cf6528b9151e4be1802cf
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "72754188"
---
# <a name="define-and-use-moderation-workflows-rest"></a>Definir e utilizar fluxos de trabalho de moderação (REST)

Os fluxos de trabalho são filtros personalizados baseados na nuvem que pode usar para lidar com o conteúdo de forma mais eficiente. Os fluxos de trabalho podem ligar-se a uma variedade de serviços para filtrar o conteúdo de diferentes maneiras e, em seguida, tomar as medidas adequadas. Este guia mostra-lhe como utilizar as APIs REST REST, através da consola API, para criar e utilizar fluxos de trabalho. Assim que compreender a estrutura das APIs, pode facilmente fazer com que estas chamadas possam fazer qualquer plataforma compatível com REST.

## <a name="prerequisites"></a>Pré-requisitos

- Inscreva-se ou crie uma conta no site da ferramenta Content Moderator [Review.](https://contentmoderator.cognitive.microsoft.com/)

## <a name="create-a-workflow"></a>Criar um fluxo de trabalho

Para criar ou atualizar um fluxo de trabalho, vá à página de referência **[Workflow - Create Or Update](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b46b3f9b0711b43c4c59)** API e selecione o botão para a sua região-chave (pode encontrá-lo no URL endpoint na página de **Credenciais** da [ferramenta 'Revisão).](https://contentmoderator.cognitive.microsoft.com/) Isto inicia a consola API, onde podes facilmente construir e executar chamadas REST API.

![Workflow - Criar ou Atualizar a seleção da região da página](images/test-drive-region.png)

### <a name="enter-rest-call-parameters"></a>Insira os parâmetros de chamada REST

Introduza valores para **equipa,** **workflowname**e **Ocp-Apim-Subscription-Key:**

- **equipa**: O ID da equipa que criou quando configura a sua conta de [ferramentas De Revisão](https://contentmoderator.cognitive.microsoft.com/) (encontrada no campo **Id** no ecrã de Credenciais da ferramenta Review).
- **nome**de fluxo de trabalho : O nome de um novo fluxo de trabalho a adicionar (ou um nome existente, se pretender atualizar um fluxo de trabalho existente).
- **Chave de subscrição Ocp-Apim:** A sua tecla moderadora de conteúdo. Pode encontrá-lo no separador **Definições** da [ferramenta Rever](https://contentmoderator.cognitive.microsoft.com).

![Workflow - Criar ou atualizar parâmetros de consulta de consola e cabeçalhos](images/workflow-console-parameters.PNG)

### <a name="enter-a-workflow-definition"></a>Introduza uma definição de fluxo de trabalho

1. Editar a caixa **de corpo de Pedido** para introduzir o pedido JSON com detalhes para **Descrição** e **Tipo** (ou). `Image` `Text`
2. Para **expressão**, copie a expressão padrão de fluxo de trabalho JSON. A sua última corda JSON deve ser assim:

```json
{
  "Description":"<A description for the Workflow>",
  "Type":"Text",
  "Expression":{
    "Type":"Logic",
    "If":{
      "ConnectorName":"moderator",
      "OutputName":"isAdult",
      "Operator":"eq",
      "Value":"true",
      "Type":"Condition"
    },
    "Then":{
      "Perform":[
        {
          "Name":"createreview",
          "CallbackEndpoint":null,
          "Tags":[

          ]
        }
      ],
      "Type":"Actions"
    }
  }
}
```

> [!NOTE]
> Pode definir expressões simples, complexas e até aninhadas para os seus fluxos de trabalho usando esta API. O [Workflow - Criar ou Atualizar](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b46b3f9b0711b43c4c59) documentação tem exemplos de lógica mais complexa.

### <a name="submit-your-request"></a>Submeta o seu pedido
  
Selecione **Enviar**. Se a operação for bem sucedida, o estado `true` **resposta** é `200 OK`, e a caixa de conteúdo **resposta** exibe .

### <a name="examine-the-new-workflow"></a>Examinar o novo fluxo de trabalho

Na [ferramenta Rever,](https://contentmoderator.cognitive.microsoft.com/)selecione **Definições** > **Fluxos de trabalho**. O seu novo fluxo de trabalho deve aparecer na lista.

![Lista de ferramentas de revisão de fluxos de trabalho](images/workflow-console-new-workflow.PNG)

Selecione a opção **Editar** para o seu fluxo de trabalho e vá ao separador **Designer.** Aqui, pode ver-se uma representação intuitiva da lógica da JSON.

![Separador de designer para um fluxo de trabalho selecionado](images/workflow-console-new-workflow-designer.PNG)

## <a name="get-workflow-details"></a>Obtenha detalhes do fluxo de trabalho

Para obter detalhes sobre um fluxo de trabalho existente, vá à página de referência **[Workflow - Obtenha](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b44b3f9b0711b43c4c58)** API e selecione o botão para a sua região (a região em que a sua chave é administrada).

![Workflow - Obtenha a seleção da região](images/test-drive-region.png)

Introduza os parâmetros de chamada REST como na secção acima. Certifique-se de que, desta vez, o nome de **fluxo de trabalho** é o nome de um fluxo de trabalho existente.

![Obtenha parâmetros de consulta e cabeçalhos](images/workflow-get-default.PNG)

Selecione **Enviar**. Se a operação for bem `200 OK`sucedida, o estado **resposta** é , e a caixa de conteúdo **resposta** apresenta o fluxo de trabalho no formato JSON, como o seguinte:

```json
{
  "Name":"default",
  "Description":"Default",
  "Type":"Image",
  "Expression":{
    "If":{
      "ConnectorName":"moderator",
      "OutputName":"isadult",
      "Operator":"eq",
      "Value":"true",
      "AlternateInput":null,
      "Type":"Condition"
    },
    "Then":{
      "Perform":[
        {
          "Name":"createreview",
          "Subteam":null,
          "CallbackEndpoint":null,
          "Tags":[

          ]
        }
      ],
      "Type":"Actions"
    },
    "Else":null,
    "Type":"Logic"
  }
}
```

## <a name="next-steps"></a>Passos seguintes

- Aprenda a utilizar fluxos de trabalho com empregos de moderação de [conteúdos.](try-review-api-job.md)