---
title: Definir fluxos de trabalho de moderação com a consola de REST API - Content Moderator
titlesuffix: Azure Cognitive Services
description: Pode utilizar as APIs de revisão do moderador de conteúdo do Azure para definir fluxos de trabalho personalizados e limiares com base em suas diretivas de conteúdo.
services: cognitive-services
author: sanjeev3
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: article
ms.date: 03/14/2019
ms.author: sajagtap
ms.openlocfilehash: e150b1321f2fbd348e737222c752203281503643
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60605818"
---
# <a name="define-and-use-moderation-workflows-rest"></a>Definir e utilizar fluxos de trabalho de moderação (REST)

Fluxos de trabalho são filtros personalizados com base na cloud que pode usar para manipular o conteúdo com mais eficiência. Fluxos de trabalho podem ligar a uma variedade de serviços para filtrar o conteúdo de formas diferentes e, em seguida, execute as ações apropriadas. Este guia mostra-lhe como utilizar o fluxo de trabalho REST APIs, através da consola de API, para criar e utilizar fluxos de trabalho. Assim que compreender a estrutura das APIs, poderá transportar facilmente essas chamadas para qualquer plataforma compatível com REST.

## <a name="prerequisites"></a>Pré-requisitos

- Inicie sessão ou crie uma conta no Content Moderator [ferramenta de revisão](https://contentmoderator.cognitive.microsoft.com/) site.

## <a name="create-a-workflow"></a>Criar um fluxo de trabalho

Para criar ou atualizar um fluxo de trabalho, vá para o **[fluxo de trabalho - criar ou atualizar](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b46b3f9b0711b43c4c59)** API página e selecione o botão para a sua região de chave (pode encontrar estas informações no URL do ponto final no **credenciais**  página do [ferramenta de revisão](https://contentmoderator.cognitive.microsoft.com/)). Esta ação inicia a consola de API, onde pode facilmente criar e executar chamadas de REST API.

![Fluxo de trabalho - seleção de região de página de atualização ou de criar](images/test-drive-region.png)

### <a name="enter-rest-call-parameters"></a>Introduza os parâmetros da chamada REST

Introduza os valores para **equipe**, **workflowname**, e **Ocp-Apim-Subscription-Key**:

- **team**: O ID da equipa que criou quando configurou seu [ferramenta de revisão](https://contentmoderator.cognitive.microsoft.com/) conta (encontrada no **Id** campo no ecrã de credenciais da sua ferramenta de revisão).
- **workflowname**: O nome de um novo fluxo de trabalho para adicionar (ou um nome existente, se quiser atualizar um fluxo de trabalho existente).
- **Ocp-Apim-Subscription-Key**: A chave do Content Moderator. Pode encontrar isto na **definições** separador da [ferramenta de revisão](https://contentmoderator.cognitive.microsoft.com).

![Fluxo de trabalho - criar parâmetros de consulta de consola ou de atualização e cabeçalhos](images/workflow-console-parameters.PNG)

### <a name="enter-a-workflow-definition"></a>Introduza uma definição de fluxo de trabalho

1. Editar a **corpo do pedido** caixa para introduzir o pedido do JSON com detalhes para **Descrição** e **tipo** (ou `Image` ou `Text`).
2. Para **expressão**, copie o fluxo de trabalho padrão expressão de JSON. A cadeia de caracteres do JSON final deve ter este aspeto:

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
> Pode definir expressões simples, complexas e até mesmo aninhadas para fluxos de trabalho com esta API. O [fluxo de trabalho - criar ou atualizar](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b46b3f9b0711b43c4c59) documentação tem exemplos de lógica mais complexa.

### <a name="submit-your-request"></a>Submeter o pedido
  
Selecione **Enviar**. Se a operação for bem-sucedida, o **estado de resposta** é `200 OK`e o **conteúdo da resposta** caixa apresenta `true`.

### <a name="examine-the-new-workflow"></a>Examine o novo fluxo de trabalho

Na [ferramenta de revisão](https://contentmoderator.cognitive.microsoft.com/), selecione **definições** > **fluxos de trabalho**. Seu novo fluxo de trabalho deve aparecer na lista.

![Lista de ferramentas de revisão de fluxos de trabalho](images/workflow-console-new-workflow.PNG)

Selecione o **editar** opção para seu fluxo de trabalho e vá para o **Designer** separador. Aqui, pode ver uma representação intuitiva a lógica de JSON.

![Separador de Designer para um fluxo de trabalho selecionado](images/workflow-console-new-workflow-designer.PNG)

## <a name="get-workflow-details"></a>Obter os detalhes de fluxo de trabalho

Para obter detalhes sobre um fluxo de trabalho existente, vá para o **[Get - fluxo de trabalho](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b44b3f9b0711b43c4c58)** página de referência de API e selecione o botão para a sua região (a região em que a chave é administrada).

![Fluxo de trabalho - seleção de região de Get](images/test-drive-region.png)

Introduza os parâmetros da chamada REST como na seção acima. Certifique-se de que desta vez, **workflowname** é o nome do fluxo de trabalho existente.

![Obter parâmetros de consulta e cabeçalhos](images/workflow-get-default.PNG)

Selecione **Enviar**. Se a operação for bem-sucedida, o **estado de resposta** é `200 OK`e o **conteúdo da resposta** caixa apresenta o fluxo de trabalho no formato JSON, semelhante ao seguinte:

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

## <a name="next-steps"></a>Passos Seguintes

- Saiba como utilizar fluxos de trabalho com [tarefas de moderação de conteúdos](try-review-api-job.md).