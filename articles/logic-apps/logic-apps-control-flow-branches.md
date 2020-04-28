---
title: Criar ou juntar ramos paralelos para ações em fluxos de trabalho
description: Saiba como criar ou fundir sucursais paralelas para ações independentes de fluxo de trabalho em Aplicações Lógicas Azure
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 10/10/2018
ms.openlocfilehash: c0b1519992ba930382a1987aed185ef3c92eded4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75453434"
---
# <a name="create-or-join-parallel-branches-for-workflow-actions-in-azure-logic-apps"></a>Criar ou juntar ramos paralelos para ações de fluxo de trabalho em Aplicações Lógicas Azure

Por padrão, as suas ações em fluxos de trabalho de aplicações lógicas são sequencialmente. Para realizar ações independentes ao mesmo tempo, pode criar [ramos paralelos](#parallel-branches)e, em seguida, [juntar-se a esses ramos](#join-branches) mais tarde no seu fluxo. 

> [!TIP] 
> Se tiver um gatilho que receba uma matriz e queira executar um fluxo de trabalho para cada item de matriz, pode *desempantar* essa matriz com a propriedade do gatilho [ **SplitOn** ](../logic-apps/logic-apps-workflow-actions-triggers.md#split-on-debatch).

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição, [inscreva-se numa conta do Azure gratuita](https://azure.microsoft.com/free/). 

* Conhecimento básico sobre [como criar aplicações lógicas](../logic-apps/quickstart-create-first-logic-app-workflow.md)

<a name="parallel-branches"></a>

## <a name="add-parallel-branch"></a>Adicionar ramo paralelo

Para executar passos independentes ao mesmo tempo, pode adicionar ramos paralelos ao lado de um passo existente. 

![Correr passos em paralelo](media/logic-apps-control-flow-branches/parallel.png)

A sua aplicação lógica aguarda que todos os ramos terminem antes de continuar o fluxo de trabalho. Os ramos paralelos `runAfter` só funcionam quando os seus valores de propriedade correspondem ao estado do passo dos pais acabado. Por exemplo, `branchAction1` `branchAction2` ambos e estão `parentAction` definidos `Succeeded` para funcionar apenas quando os completos com estatuto.

> [!NOTE]
> Antes de começar, a sua aplicação lógica já deve ter um passo onde pode adicionar ramos paralelos.

1. No <a href="https://portal.azure.com" target="_blank">portal Azure,</a>abra a sua aplicação lógica no Logic App Designer.

1. Mova o ponteiro sobre a seta acima do degrau onde pretende adicionar ramos paralelos. Escolha o sinal**+** de **mais** ( ) que aparece e, em seguida, escolha Adicionar um **ramo paralelo**. 

   ![Adicionar ramo paralelo](media/logic-apps-control-flow-branches/add-parallel-branch.png)

1. Na caixa de pesquisa, encontre e selecione a ação que deseja.

   ![Encontre e selecione a ação que deseja](media/logic-apps-control-flow-branches/find-select-parallel-action.png)

   A sua ação selecionada aparece agora no ramo paralelo, por exemplo:

   ![Encontre e selecione a ação que deseja](media/logic-apps-control-flow-branches/added-parallel-branch.png)

1. Agora, em cada ramo paralelo, adicione os passos que quiser. Para adicionar outra ação a um ramo, mova o seu ponteiro sob a ação onde pretende adicionar uma ação sequencial. Escolha o**+** sinal de **mais** ( ) que aparece e, em seguida, selecione Adicionar uma **ação**.

   ![Adicione ação sequencial ao ramo paralelo](media/logic-apps-control-flow-branches/add-sequential-action.png)

1. Na caixa de pesquisa, encontre e selecione a ação que deseja.

   ![Localizar e selecionar ação sequencial](media/logic-apps-control-flow-branches/find-select-sequential-action.png)

   A sua ação selecionada aparece agora dentro do ramo atual, por exemplo:

   ![Localizar e selecionar ação sequencial](media/logic-apps-control-flow-branches/added-sequential-action.png)

Para voltar a fundir os ramos, [junte os seus ramos paralelos.](#join-branches) 

<a name="parallel-json"></a>

## <a name="parallel-branch-definition-json"></a>Definição paralela de ramo (JSON)

Se estiver a trabalhar em vista de código, pode definir a estrutura paralela na definição JSON da sua aplicação lógica, por exemplo:

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

Para fundir ramos paralelos, basta adicionar um passo na parte inferior sob todos os ramos. Este passo corre depois de todos os ramos paralelos terminarem de correr.

![Junte-se a ramos paralelos](media/logic-apps-control-flow-branches/join.png)

1. No [portal Azure,](https://portal.azure.com)encontre e abra a sua aplicação lógica no Logic App Designer. 

1. Sob os ramos paralelos que pretende aderir, escolha **novo passo.** 

   ![Adicione passo para aderir](media/logic-apps-control-flow-branches/add-join-step.png)

1. Na caixa de pesquisa, encontre e selecione a ação que deseja como o passo que une os ramos.

   ![Encontre e selecione a ação que une ramos paralelos](media/logic-apps-control-flow-branches/join-steps.png)

   Os seus ramos paralelos estão agora fundidos.

   ![Ramos aderidos](media/logic-apps-control-flow-branches/joined-branches.png)

<a name="join-json"></a>

## <a name="join-definition-json"></a>Definição de adesão (JSON)

Se estiver a trabalhar em vista de código, pode definir a estrutura de adesão na definição JSON da sua aplicação lógica, por exemplo:

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
* Para submeter ou votar em funcionalidades e sugestões, visite o site de feedback do utilizador das [Aplicações Lógicas Azure.](https://aka.ms/logicapps-wish)

## <a name="next-steps"></a>Passos seguintes

* [Passos de execução com base numa condição (declarações condicionais)](../logic-apps/logic-apps-control-flow-conditional-statement.md)
* [Executar passos com base em diferentes valores (declarações de switch)](../logic-apps/logic-apps-control-flow-switch-statement.md)
* [Executar e repetir passos (loops)](../logic-apps/logic-apps-control-flow-loops.md)
* [Passos de execução baseados no estado de ação agruparado (âmbitos)](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)
