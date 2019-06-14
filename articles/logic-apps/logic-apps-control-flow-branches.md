---
title: Criar ou adicionar ramos paralelos - Azure Logic Apps | Documentos da Microsoft
description: Como criar ou associar ramos paralelos para fluxos de trabalho no Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
ms.date: 10/10/2018
ms.openlocfilehash: 2e1c155a371fa96e4f772f632a9585948b012e54
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60685159"
---
# <a name="create-or-join-parallel-branches-for-workflow-actions-in-azure-logic-apps"></a>Criar ou adicionar ramos paralelos para ações de fluxo de trabalho no Azure Logic Apps

Por predefinição, suas ações em fluxos de trabalho de aplicação de lógica são executados sequencialmente. Para executar ações independentes ao mesmo tempo, pode criar [ramificações em paralelo](#parallel-branches)e, em seguida [associar esses ramos](#join-branches) posterior no seu fluxo. 

> [!TIP] 
> Se tiver um acionador que recebe uma matriz e quer executar um fluxo de trabalho para cada item da matriz, pode *debatch* essa matriz com o [ **SplitOn** acionar propriedade](../logic-apps/logic-apps-workflow-actions-triggers.md#split-on-debatch).

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição, [inscreva-se numa conta do Azure gratuita](https://azure.microsoft.com/free/). 

* Conhecimento básico sobre [como criar aplicações lógicas](../logic-apps/quickstart-create-first-logic-app-workflow.md)

<a name="parallel-branches"></a>

## <a name="add-parallel-branch"></a>Adicionar ramo paralelo

Para executar etapas independentes ao mesmo tempo, pode adicionar ramos paralelos junto a um passo existente. 

![Execute os passos em paralelo](media/logic-apps-control-flow-branches/parallel.png)

A aplicação lógica aguarda todos os ramos concluir antes de continuar o fluxo de trabalho. Em paralelo ramos executados apenas quando seus `runAfter` valores de propriedade corresponde ao estado de um passo concluído principal. Por exemplo, ambos `branchAction1` e `branchAction2` estão definidas para ser executado apenas quando o `parentAction` é concluída com `Succeeded` estado.

> [!NOTE]
> Antes de começar, a aplicação lógica tem de ter um passo onde pode adicionar ramos paralelos.

1. Na <a href="https://portal.azure.com" target="_blank">portal do Azure</a>, abra a aplicação lógica no Estruturador da aplicação lógica.

1. Mova o ponteiro do mouse sobre a seta para os passos acima onde pretende adicionar ramos paralelos. Escolha o **plus** início de sessão ( **+** ) que é apresentada e, em seguida, escolha **adicionar um ramo paralelo**. 

   ![Adicionar ramo paralelo](media/logic-apps-control-flow-branches/add-parallel-branch.png)

1. Na caixa de pesquisa, localize e selecione a ação que pretende.

   ![Localize e selecione a ação que pretende](media/logic-apps-control-flow-branches/find-select-parallel-action.png)

   A ação selecionada agora aparece no ramo paralelo, por exemplo:

   ![Localize e selecione a ação que pretende](media/logic-apps-control-flow-branches/added-parallel-branch.png)

1. Agora, em cada ramo paralelo, adicione os passos que pretender. Para adicionar outra ação para um ramo, mova o ponteiro sob a ação em que pretende adicionar uma ação seqüencial. Escolha o **plus** ( **+** ) início de sessão que aparece e, em seguida, selecione **adicionar uma ação**.

   ![Adicionar ação seqüencial ao ramo paralelo](media/logic-apps-control-flow-branches/add-sequential-action.png)

1. Na caixa de pesquisa, localize e selecione a ação que pretende.

   ![Localize e selecione a ação sequencial](media/logic-apps-control-flow-branches/find-select-sequential-action.png)

   Agora a ação selecionada é apresentada na filial atual, por exemplo:

   ![Localize e selecione a ação sequencial](media/logic-apps-control-flow-branches/added-sequential-action.png)

Para intercalar os ramos de volta em conjunto, [associar seus ramos paralelos](#join-branches). 

<a name="parallel-json"></a>

## <a name="parallel-branch-definition-json"></a>Definição de ramo paralelo (JSON)

Se estiver a trabalhar na vista de código, pode definir a estrutura paralela na definição de JSON da sua aplicação lógica em vez disso, por exemplo:

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

## <a name="join-parallel-branches"></a>Junte-se a ramos paralelos

Para mesclar ramificações paralelas, basta adicione um passo na parte inferior em todos os ramos. Este passo é executado Afinal de contas a conclusão de ramos paralelos em execução.

![Junte-se a ramos paralelos](media/logic-apps-control-flow-branches/join.png)

1. Na [portal do Azure](https://portal.azure.com), encontrar e abrir a aplicação lógica no Estruturador da aplicação lógica. 

1. Sob os ramos paralelos que pretende participar, escolha **novo passo**. 

   ![Adicionar passo para aderir](media/logic-apps-control-flow-branches/add-join-step.png)

1. Na caixa de pesquisa, localize e selecione a ação que pretende que o passo que junta as ramificações.

   ![Localize e selecione a ação que junta ramos paralelos](media/logic-apps-control-flow-branches/join-steps.png)

   Os ramos paralelos são Unidos.

   ![Associado ao ramos](media/logic-apps-control-flow-branches/joined-branches.png)

<a name="join-json"></a>

## <a name="join-definition-json"></a>Junte-se a definição (JSON)

Se estiver a trabalhar na vista de código, pode definir a estrutura de associação na definição de JSON da sua aplicação lógica em vez disso, por exemplo:

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
* Para submeter ou votar em recursos e sugestões, visite o [site de comentários de utilizadores do Azure Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Passos Seguintes

* [Execute os passos com base numa condição (instruções condicionais)](../logic-apps/logic-apps-control-flow-conditional-statement.md)
* [Execute os passos com base nos valores diferentes (declarações do comutador)](../logic-apps/logic-apps-control-flow-switch-statement.md)
* [Executar e repita os passos (loops)](../logic-apps/logic-apps-control-flow-loops.md)
* [Execute os passos com base no estado da ação agrupados (âmbitos)](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)
