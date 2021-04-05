---
title: Definir fluxos de trabalho de moderação com a consola REST API - Moderador de Conteúdo
titleSuffix: Azure Cognitive Services
description: Pode utilizar as APIs de Revisão do Moderador de Conteúdo Azure para definir fluxos de trabalho personalizados e limiares baseados nas suas políticas de conteúdo.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 03/14/2019
ms.author: pafarley
ms.openlocfilehash: 79749533d636f4b73ff3bef6b12d9e842ac485ea
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "96905179"
---
# <a name="define-and-use-moderation-workflows-api-console"></a>Definir e utilizar fluxos de trabalho de moderação (consola API)

Os fluxos de trabalho são filtros personalizados baseados na nuvem que pode usar para lidar com o conteúdo de forma mais eficiente. Os fluxos de trabalho podem ligar-se a uma variedade de serviços para filtrar o conteúdo de diferentes maneiras e, em seguida, tomar as medidas adequadas. Este guia mostra-lhe como utilizar o fluxo de trabalho REST APIs, através da consola API, para criar e utilizar fluxos de trabalho. Uma vez que você entenda a estrutura das APIs, você pode facilmente levar estas chamadas para qualquer plataforma compatível com REST.

## <a name="prerequisites"></a>Pré-requisitos

- Iniciar sessão ou criar uma conta no site da [ferramenta De Revisão](https://contentmoderator.cognitive.microsoft.com/) de Moderadores de Conteúdo.

## <a name="create-a-workflow"></a>Criar um fluxo de trabalho

Para criar ou atualizar um fluxo de trabalho, vá ao **[fluxo de trabalho - Crie ou atualize](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b46b3f9b0711b43c4c59)** a página de referência da API e selecione o botão para a sua região-chave. Pode encontrar a sua região no URL endpoint na página **de Credenciais** da [ferramenta Review](https://contentmoderator.cognitive.microsoft.com/). Isto inicia a consola API, onde pode facilmente construir e executar chamadas REST API.

![Fluxo de trabalho - Criar ou atualizar a seleção da região da página](images/test-drive-region.png)

### <a name="enter-rest-call-parameters"></a>Insira os parâmetros de chamada REST

Introduza valores para **equipa,** **workflowname** e **Ocp-Apim-Subscription-Key**:

- **equipa**: O ID da equipa que criou quando criou a sua conta [de ferramenta Review](https://contentmoderator.cognitive.microsoft.com/) (encontrada no campo **Id** no ecrã de Credenciais da sua ferramenta Review).
- **workflowname**: O nome de um novo fluxo de trabalho a adicionar (ou um nome existente, se pretender atualizar um fluxo de trabalho existente).
- **Chave Ocp-Apim-Subscrição :** A tecla do Moderador de Conteúdo. Pode encontrar esta chave no **separador Definições** da [ferramenta 'Rever'.](https://contentmoderator.cognitive.microsoft.com)

![Fluxo de trabalho - Criar ou atualizar parâmetros de consulta de consola e cabeçalhos](images/workflow-console-parameters.PNG)

### <a name="enter-a-workflow-definition"></a>Introduza uma definição de fluxo de trabalho

1. Edite a caixa **de corpo Request** para introduzir o pedido JSON com detalhes para **Descrição** e **Tipo** (ou `Image` ou `Text` ).
2. Para **expressão,** copie a expressão JSON do fluxo de trabalho predefinido. A sua última corda JSON deve ser assim:

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
> Você pode definir expressões simples, complexas e até aninhadas para os seus fluxos de trabalho usando esta API. O [fluxo de trabalho - Criar ou Atualizar](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b46b3f9b0711b43c4c59) documentação tem exemplos de lógica mais complexa.

### <a name="submit-your-request"></a>Submeter o pedido
  
Selecione **Enviar**. Se a operação for bem sucedida, o **estado de Resposta** é , e a caixa de conteúdo `200 OK` **response** aparece `true` .

### <a name="examine-the-new-workflow"></a>Examinar o novo fluxo de trabalho

Na [ferramenta 'Rever' (Revisão),](https://contentmoderator.cognitive.microsoft.com/)selecione **Definições**  >  **Fluxos de trabalho**. O seu novo fluxo de trabalho deve aparecer na lista.

![Rever lista de ferramentas de fluxos de trabalho](images/workflow-console-new-workflow.PNG)

Selecione a opção **Editar** para o seu fluxo de trabalho e vá para o **separador Designer.** Aqui, pode ver uma representação intuitiva da lógica JSON.

![Separador de design para um fluxo de trabalho selecionado](images/workflow-console-new-workflow-designer.PNG)

## <a name="get-workflow-details"></a>Obtenha detalhes do fluxo de trabalho

Para obter detalhes sobre um fluxo de trabalho existente, vá ao **[Workflow - Obtenha](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b44b3f9b0711b43c4c58)** a página de referência da API e selecione o botão para a sua região (a região em que a sua chave é administrada).

![Workflow - Obtenha a seleção da região](images/test-drive-region.png)

Introduza os parâmetros de chamada REST como na secção acima. Certifique-se de que, desta vez, o **trabalho voado** é o nome de um fluxo de trabalho existente.

![Obtenha parâmetros de consulta e cabeçalhos](images/workflow-get-default.PNG)

Selecione **Enviar**. Se a operação for bem sucedida, o **estado de Resposta** é , e a caixa de conteúdo `200 OK` **response** apresenta o fluxo de trabalho no formato JSON, como o exemplo seguinte:

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

- Saiba como utilizar fluxos de trabalho com [trabalhos de moderação de conteúdos.](try-review-api-job.md)