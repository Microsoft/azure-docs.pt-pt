---
title: Criar ou unir ramos paralelos para ações em fluxos de trabalho
description: Saiba como criar ou fundir balcões paralelos para ações de fluxo de trabalho independentes em Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 10/10/2018
ms.openlocfilehash: 3514ce966e1de9af1741de6b966964aca2599610
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "91269238"
---
# <a name="create-or-join-parallel-branches-for-workflow-actions-in-azure-logic-apps"></a>Criar ou juntar ramos paralelos para ações de fluxo de trabalho em Azure Logic Apps

Por padrão, as suas ações em fluxos de trabalho de aplicações lógicas são executadas sequencialmente. Para realizar ações independentes ao mesmo tempo, pode criar [ramos paralelos,](#parallel-branches)e depois [juntar esses ramos](#join-branches) mais tarde no seu fluxo. 

> [!TIP] 
> Se tiver um gatilho que receba uma matriz e queira executar um fluxo de trabalho para cada item de matriz, pode *debaste* essa matriz com a propriedade do gatilho [ **SplitOn**](../logic-apps/logic-apps-workflow-actions-triggers.md#split-on-debatch).

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição, [inscreva-se numa conta do Azure gratuita](https://azure.microsoft.com/free/). 

* Conhecimento básico sobre [como criar aplicativos lógicos](../logic-apps/quickstart-create-first-logic-app-workflow.md)

<a name="parallel-branches"></a>

## <a name="add-parallel-branch"></a>Adicionar ramo paralelo

Para executar passos independentes ao mesmo tempo, pode adicionar ramos paralelos ao lado de um passo existente. 

![Passos de execução em paralelo](media/logic-apps-control-flow-branches/parallel.png)

A sua aplicação lógica espera que todos os ramos terminem antes de continuar o fluxo de trabalho. Os ramos paralelos funcionam apenas quando os seus `runAfter` valores de propriedade correspondem ao estatuto do passo dos pais acabados. Por exemplo, ambos `branchAction1` e `branchAction2` são definidos para funcionar apenas quando o `parentAction` completo com o `Succeeded` estado.

> [!NOTE]
> Antes de começar, a sua aplicação lógica já deve ter um passo em que pode adicionar ramos paralelos.

1. No <a href="https://portal.azure.com" target="_blank">portal Azure,</a>abra a sua aplicação lógica no Logic App Designer.

1. Desloque o ponteiro sobre a seta acima do degrau onde pretende adicionar ramos paralelos. Escolha o sinal **de mais** **+** () que aparece e, em seguida, escolha **Adicionar um ramo paralelo**. 

   ![Adicionar ramo paralelo](media/logic-apps-control-flow-branches/add-parallel-branch.png)

1. Na caixa de pesquisa, encontre e selecione a ação desejada.

   ![Screenshot que mostra a janela "Escolha uma ação" no Logic App Designer.](media/logic-apps-control-flow-branches/find-select-parallel-action.png)

   A sua ação selecionada aparece agora no ramo paralelo, por exemplo:

   ![Encontre e selecione a ação que deseja](media/logic-apps-control-flow-branches/added-parallel-branch.png)

1. Agora, em cada ramo paralelo, adicione os passos que quiser. Para adicionar outra ação a um ramo, mova o ponteiro sob a ação onde pretende adicionar uma ação sequencial. Escolha o sinal **mais** **+** () que aparece e, em seguida, **selecione Adicione uma ação**.

   ![Adicionar ação sequencial ao ramo paralelo](media/logic-apps-control-flow-branches/add-sequential-action.png)

1. Na caixa de pesquisa, encontre e selecione a ação desejada.

   ![Screenshot que mostra a janela e caixa de pesquisa "Escolha uma ação" no Logic App Designer.](media/logic-apps-control-flow-branches/find-select-sequential-action.png)

   A sua ação selecionada aparece agora dentro do ramo atual, por exemplo:

   ![Encontrar e selecionar ação sequencial](media/logic-apps-control-flow-branches/added-sequential-action.png)

Para fundir os ramos de novo, [junte os seus ramos paralelos.](#join-branches) 

<a name="parallel-json"></a>

## <a name="parallel-branch-definition-json"></a>Definição de ramo paralelo (JSON)

Se estiver a trabalhar na visão de código, pode definir a estrutura paralela na definição JSON da sua aplicação lógica, por exemplo:

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

## <a name="join-parallel-branches"></a>Junte ramos paralelos

Para fundir ramos paralelos, basta adicionar um passo na parte inferior sob todos os ramos. Este passo corre depois de todos os ramos paralelos terminarem de correr.

![Junte ramos paralelos](media/logic-apps-control-flow-branches/join.png)

1. No [portal Azure,](https://portal.azure.com)encontre e abra a sua aplicação lógica no Logic App Designer. 

1. Sob os ramos paralelos que pretende aderir, escolha **Novo passo.** 

   ![Adicione passo para se juntar](media/logic-apps-control-flow-branches/add-join-step.png)

1. Na caixa de pesquisa, encontre e selecione a ação que deseja como o passo que une os ramos.

   ![Encontre e selecione a ação que une ramos paralelos](media/logic-apps-control-flow-branches/join-steps.png)

   Os seus ramos paralelos estão agora fundidos.

   ![Ramos unidos](media/logic-apps-control-flow-branches/joined-branches.png)

<a name="join-json"></a>

## <a name="join-definition-json"></a>Junte-se à definição (JSON)

Se estiver a trabalhar na visão de código, pode definir a estrutura de junção na definição JSON da sua aplicação lógica, por exemplo:

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

* Para dúvidas, visite a página de perguntas do [Microsoft Q&Uma página de perguntas para aplicações Azure Logic](/answers/topics/azure-logic-apps.html).
* Para submeter ou votar em funcionalidades e sugestões, visite o site de feedback do [utilizador do Azure Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Passos seguintes

* [Passos de execução com base numa condição (declarações condicionais)](../logic-apps/logic-apps-control-flow-conditional-statement.md)
* [Passos de execução com base em diferentes valores (declarações de switch)](../logic-apps/logic-apps-control-flow-switch-statement.md)
* [Executar e repetir passos (laços)](../logic-apps/logic-apps-control-flow-loops.md)
* [Passos de execução baseados no estado de ação agrupado (âmbitos)](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)
