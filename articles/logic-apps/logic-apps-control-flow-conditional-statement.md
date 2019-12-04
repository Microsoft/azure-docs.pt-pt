---
title: Adicionar instruções condicionais a fluxos de trabalho
description: Como criar condições que controlam ações em fluxos de trabalho em aplicativos lógicos do Azure
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 10/09/2018
ms.openlocfilehash: fe79cf5af86e1f303e4735214b993d8db4488a25
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74793247"
---
# <a name="create-conditional-statements-that-control-workflow-actions-in-azure-logic-apps"></a>Criar instruções condicionais que controlam ações de fluxo de trabalho em aplicativos lógicos do Azure

Para executar ações específicas em seu aplicativo lógico somente após passar uma condição especificada, adicione uma *instrução condicional*. Essa estrutura de controle compara os dados em seu fluxo de trabalho com valores ou campos específicos. Em seguida, você pode especificar ações diferentes que são executadas com base em se os dados atendem ou não à condição. Você pode aninhar condições dentro das outras.

Por exemplo, suponha que você tenha um aplicativo lógico que envia emails demais quando novos itens aparecem no RSS feed de um site. Você pode adicionar uma instrução condicional para enviar email somente quando o novo item incluir uma cadeia de caracteres específica. 

> [!TIP]
> Para executar etapas diferentes com base em valores específicos diferentes, use uma [*instrução switch*](../logic-apps/logic-apps-control-flow-switch-statement.md) em vez disso.

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição, [inscreva-se numa conta do Azure gratuita](https://azure.microsoft.com/free/).

* Conhecimento básico sobre [como criar aplicativos lógicos](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Para seguir o exemplo neste artigo, [crie este aplicativo lógico de exemplo](../logic-apps/quickstart-create-first-logic-app-workflow.md) com uma conta do Outlook Outlook.com ou Office 365.

## <a name="add-condition"></a>Adicionar condição

1. No <a href="https://portal.azure.com" target="_blank">portal do Azure</a>, abra seu aplicativo lógico no designer de aplicativo lógico.

1. Adicione uma condição no local desejado. 

   Para adicionar uma condição entre etapas, mova o ponteiro sobre a seta onde você deseja adicionar a condição. Escolha o **sinal de adição** ( **+** ) que aparece e, em seguida, escolha **Adicionar uma ação**. Por exemplo:

   ![Adicionar ação entre etapas](./media/logic-apps-control-flow-conditional-statement/add-action.png)

   Quando desejar adicionar uma condição no final do fluxo de trabalho, na parte inferior do aplicativo lógico, escolha **nova etapa** > **Adicionar uma ação**.

1. Na caixa de pesquisa, digite "Condition" como filtro. Selecione esta ação: **controle de condição**

   ![Adicionar condição](./media/logic-apps-control-flow-conditional-statement/add-condition.png)

1. Na caixa **condição** , crie sua condição. 

   1. Na caixa à esquerda, especifique os dados ou o campo que você deseja comparar.

      Quando você clica dentro da caixa à esquerda, a lista de conteúdo dinâmico é exibida para que você possa selecionar saídas de etapas anteriores em seu aplicativo lógico. 
      Para este exemplo, selecione o resumo do RSS feed.

      ![Crie sua condição](./media/logic-apps-control-flow-conditional-statement/edit-condition.png)

   1. Na caixa do meio, selecione a operação a ser executada. 
   Para este exemplo, selecione "**Contains**". 

   1. Na caixa à direita, especifique um valor ou campo como critério. 
   Para este exemplo, especifique esta cadeia de caracteres: **Microsoft**

   Aqui está a condição completa:

   ![Concluir condição](./media/logic-apps-control-flow-conditional-statement/edit-condition-2.png)

   Para adicionar outra linha à sua condição, escolha **adicionar** > **Adicionar linha**. 
   Para adicionar um grupo com subcondições, escolha **adicionar** > **Adicionar grupo**. 
   Para agrupar linhas existentes, marque as caixas de seleção para essas linhas, escolha o botão de reticências (...) para qualquer linha e escolha **Criar grupo**.

1. Em **se verdadeiro** e **se falso**, adicione as etapas a serem executadas com base em se a condição é atendida. Por exemplo:

   ![Condição com caminhos "If true" e "If false"](./media/logic-apps-control-flow-conditional-statement/condition-yes-no-path.png)

   > [!TIP]
   > Você pode arrastar ações existentes para os caminhos **If true** e **If false** .

1. Guarde a aplicação lógica.

Esse aplicativo lógico agora envia email somente quando os novos itens no feed RSS atendem à sua condição.

## <a name="json-definition"></a>Definição de JSON

Aqui está a definição de código de alto nível por trás de uma instrução condicional:

``` json
"actions": {
  "Condition": {
    "type": "If",
    "actions": {
      "Send_an_email": {
        "inputs": {},
        "runAfter": {}
    },
    "expression": {
      "and": [ 
        { 
          "contains": [ 
            "@triggerBody()?['summary']", 
            "Microsoft"
          ]
        } 
      ]
    },
    "runAfter": {}
  }
},
```

## <a name="get-support"></a>Obter suporte

* Relativamente a dúvidas, visite o [fórum do Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Para enviar ou votar em recursos e sugestões, visite o [site de comentários do usuário dos aplicativos lógicos do Azure](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Passos seguintes

* [Executar etapas com base em valores diferentes (instruções switch)](../logic-apps/logic-apps-control-flow-switch-statement.md)
* [Executar e repetir etapas (loops)](../logic-apps/logic-apps-control-flow-loops.md)
* [Executar ou mesclar etapas paralelas (branches)](../logic-apps/logic-apps-control-flow-branches.md)
* [Executar etapas com base no status da ação agrupada (escopos)](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)
