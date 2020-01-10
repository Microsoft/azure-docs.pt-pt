---
title: Criar ou unir ramificações paralelas para ações em fluxos de trabalho
description: Saiba como criar ou mesclar branches em execução paralela para ações de fluxo de trabalho independentes em aplicativos lógicos do Azure
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 10/10/2018
ms.openlocfilehash: c0b1519992ba930382a1987aed185ef3c92eded4
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75453434"
---
# <a name="create-or-join-parallel-branches-for-workflow-actions-in-azure-logic-apps"></a>Criar ou unir ramificações paralelas para ações de fluxo de trabalho em aplicativos lógicos do Azure

Por padrão, suas ações em fluxos de trabalho de aplicativo lógico são executadas em sequência. Para executar ações independentes ao mesmo tempo, você pode criar [ramificações paralelas](#parallel-branches)e, em seguida, [unir essas ramificações](#join-branches) posteriormente em seu fluxo. 

> [!TIP] 
> Se você tiver um gatilho que recebe uma matriz e quiser executar um fluxo de trabalho para cada item de matriz, poderá desvincular *essa matriz* com a propriedade de gatilho de [ **divisão** ](../logic-apps/logic-apps-workflow-actions-triggers.md#split-on-debatch).

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição, [inscreva-se numa conta do Azure gratuita](https://azure.microsoft.com/free/). 

* Conhecimento básico sobre [como criar aplicativos lógicos](../logic-apps/quickstart-create-first-logic-app-workflow.md)

<a name="parallel-branches"></a>

## <a name="add-parallel-branch"></a>Adicionar ramificação paralela

Para executar as etapas independentes ao mesmo tempo, você pode adicionar ramificações paralelas ao lado de uma etapa existente. 

![Executar etapas em paralelo](media/logic-apps-control-flow-branches/parallel.png)

Seu aplicativo lógico aguarda que todas as ramificações sejam concluídas antes de continuar o fluxo de trabalho. Branches paralelos são executados somente quando seus `runAfter` valores de propriedade correspondem ao status concluído da etapa pai. Por exemplo, `branchAction1` e `branchAction2` são definidos para execução somente quando a `parentAction` é concluída com `Succeeded` status.

> [!NOTE]
> Antes de começar, seu aplicativo lógico já deve ter uma etapa onde você pode adicionar ramificações paralelas.

1. No <a href="https://portal.azure.com" target="_blank">portal do Azure</a>, abra seu aplicativo lógico no designer de aplicativo lógico.

1. Mova o ponteiro sobre a seta acima da etapa em que você deseja adicionar ramificações paralelas. Escolha o sinal de **adição** ( **+** ) que aparece e, em seguida, escolha **Adicionar uma ramificação paralela**. 

   ![Adicionar ramificação paralela](media/logic-apps-control-flow-branches/add-parallel-branch.png)

1. Na caixa de pesquisa, localize e selecione a ação desejada.

   ![Localize e selecione a ação desejada](media/logic-apps-control-flow-branches/find-select-parallel-action.png)

   A ação selecionada agora aparece na ramificação paralela, por exemplo:

   ![Localize e selecione a ação desejada](media/logic-apps-control-flow-branches/added-parallel-branch.png)

1. Agora, em cada ramificação paralela, adicione as etapas desejadas. Para adicionar outra ação a uma ramificação, mova o ponteiro sob a ação em que você deseja adicionar uma ação sequencial. Escolha o sinal de **mais** ( **+** ) que aparece e, em seguida, selecione **Adicionar uma ação**.

   ![Adicionar ação sequencial à ramificação paralela](media/logic-apps-control-flow-branches/add-sequential-action.png)

1. Na caixa de pesquisa, localize e selecione a ação desejada.

   ![Localizar e Selecionar ação sequencial](media/logic-apps-control-flow-branches/find-select-sequential-action.png)

   A ação selecionada agora aparece dentro do Branch atual, por exemplo:

   ![Localizar e Selecionar ação sequencial](media/logic-apps-control-flow-branches/added-sequential-action.png)

Para mesclar branches de volta juntos, junte-se [a seus branches paralelos](#join-branches). 

<a name="parallel-json"></a>

## <a name="parallel-branch-definition-json"></a>Definição de ramificação paralela (JSON)

Se você estiver trabalhando no modo de exibição de código, poderá definir a estrutura paralela na definição JSON do aplicativo lógico, por exemplo:

``` json
{
  "triggers": {
    "myTrigger": {}
  },
  "actions": {
    "parentAction": {
      "type": "<action-type>",
      "inputs": {},
      "runAfter": {}
    },
    "branchAction1": {
      "type": "<action-type>",
      "inputs": {},
      "runAfter": {
        "parentAction": [
          "Succeeded"
        ]
      }
    },
    "branchAction2": {
      "type": "<action-type>",
      "inputs": {},
      "runAfter": {
        "parentAction": [
          "Succeeded"
        ]
      }
    }
  },
  "outputs": {}
}
```

<a name="join-branches"></a>

## <a name="join-parallel-branches"></a>Unir branches paralelos

Para mesclar ramificações paralelas juntas, basta adicionar uma etapa na parte inferior em todas as ramificações. Esta etapa é executada Depois que todas as ramificações paralelas terminam de ser executadas.

![Unir branches paralelos](media/logic-apps-control-flow-branches/join.png)

1. Na [portal do Azure](https://portal.azure.com), localize e abra seu aplicativo lógico no designer de aplicativo lógico. 

1. Nas ramificações paralelas que você deseja ingressar, escolha **nova etapa**. 

   ![Adicionar etapa à junção](media/logic-apps-control-flow-branches/add-join-step.png)

1. Na caixa de pesquisa, localize e selecione a ação desejada como a etapa que une as ramificações.

   ![Localizar e selecionar a ação que une ramificações paralelas](media/logic-apps-control-flow-branches/join-steps.png)

   Seus branches paralelos agora são mesclados.

   ![Ramificações Unidas](media/logic-apps-control-flow-branches/joined-branches.png)

<a name="join-json"></a>

## <a name="join-definition-json"></a>Definição de junção (JSON)

Se você estiver trabalhando no modo de exibição de código, poderá definir a estrutura de junção na definição JSON do aplicativo lógico, por exemplo:

``` json
{
  "triggers": {
    "myTrigger": { }
  },
  "actions": {
    "parentAction": {
      "type": "<action-type>",
      "inputs": { },
      "runAfter": {}
    },
    "branchAction1": {
      "type": "<action-type>",
      "inputs": { },
      "runAfter": {
        "parentAction": [
          "Succeeded"
        ]
      }
    },
    "branchAction2": {
      "type": "<action-type>",
      "inputs": { },
      "runAfter": {
        "parentAction": [
          "Succeeded"
        ]
      }
    },
    "joinAction": {
      "type": "<action-type>",
      "inputs": { },
      "runAfter": {
        "branchAction1": [
          "Succeeded"
        ],
        "branchAction2": [
          "Succeeded"
        ]
      }
    }
  },
  "outputs": {}
}
```

## <a name="get-support"></a>Obter suporte

* Relativamente a dúvidas, visite o [fórum do Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Para enviar ou votar em recursos e sugestões, visite o [site de comentários do usuário dos aplicativos lógicos do Azure](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Passos seguintes

* [Executar etapas com base em uma condição (instruções condicionais)](../logic-apps/logic-apps-control-flow-conditional-statement.md)
* [Executar etapas com base em valores diferentes (instruções switch)](../logic-apps/logic-apps-control-flow-switch-statement.md)
* [Executar e repetir etapas (loops)](../logic-apps/logic-apps-control-flow-loops.md)
* [Executar etapas com base no status da ação agrupada (escopos)](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)
