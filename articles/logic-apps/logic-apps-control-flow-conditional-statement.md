---
title: Adicionar declarações condicionais aos fluxos de trabalho
description: Como criar condições que controlam ações em fluxos de trabalho em Aplicações Lógicas Azure
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 10/09/2018
ms.openlocfilehash: fe79cf5af86e1f303e4735214b993d8db4488a25
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74793247"
---
# <a name="create-conditional-statements-that-control-workflow-actions-in-azure-logic-apps"></a>Criar declarações condicionais que controlam ações de fluxo de trabalho em Aplicações Lógicas Azure

Para executar ações específicas na sua aplicação lógica apenas depois de passar uma condição especificada, adicione uma *declaração condicional*. Esta estrutura de controlo compara os dados do seu fluxo de trabalho com valores ou campos específicos. Pode então especificar diferentes ações que são executadas com base no facto de os dados cumprirem ou não a condição. Podem nidificar as condições dentro umas das outras.

Por exemplo, suponha que tenha uma aplicação lógica que envia demasiados e-mails quando novos itens aparecem no feed RSS de um site. Só pode adicionar uma declaração condicional para enviar e-mail quando o novo item incluir uma cadeia específica. 

> [!TIP]
> Para executar diferentes passos com base em diferentes valores específicos, utilize uma declaração de [*comutação.*](../logic-apps/logic-apps-control-flow-switch-statement.md)

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição, [inscreva-se numa conta do Azure gratuita](https://azure.microsoft.com/free/).

* Conhecimento básico sobre [como criar aplicações lógicas](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Para seguir o exemplo neste artigo, [crie esta aplicação lógica](../logic-apps/quickstart-create-first-logic-app-workflow.md) de amostra com uma conta Outlook.com ou Office 365 Outlook.

## <a name="add-condition"></a>Adicionar condição

1. No <a href="https://portal.azure.com" target="_blank">portal Azure,</a>abra a sua aplicação lógica no Logic App Designer.

1. Adicione uma condição no local que quiser. 

   Para adicionar uma condição entre passos, mova o ponteiro sobre a seta onde pretende adicionar a condição. Escolha o sinal**+** de **mais** () que aparece e, em seguida, escolha Adicionar uma **ação**. Por exemplo:

   ![Adicionar ação entre passos](./media/logic-apps-control-flow-conditional-statement/add-action.png)

   Quando quiser adicionar uma condição no final do seu fluxo de trabalho, na parte inferior da sua aplicação lógica, escolha **novo passo** > **Adicione uma ação**.

1. Na caixa de pesquisa, introduza "condição" como filtro. Selecione esta ação: **Condição - Controlo**

   ![Adicionar condição](./media/logic-apps-control-flow-conditional-statement/add-condition.png)

1. Na caixa da **Condição,** construa a sua condição. 

   1. Na caixa esquerda, especifique os dados ou campo que pretende comparar.

      Quando clica dentro da caixa esquerda, aparece a lista de conteúdos dinâmicos para que possa selecionar saídas a partir de passos anteriores na sua aplicação lógica. 
      Para este exemplo, selecione o resumo do feed RSS.

      ![Construa a sua condição](./media/logic-apps-control-flow-conditional-statement/edit-condition.png)

   1. Na caixa intermédia, selecione a operação para executar. 
   Para este exemplo, selecione "**contém**". 

   1. Na caixa certa, especifique um valor ou campo como critérios. 
   Para este exemplo, especifique esta cadeia: **Microsoft**

   Aqui está a condição completa:

   ![Concluir condição](./media/logic-apps-control-flow-conditional-statement/edit-condition-2.png)

   Para adicionar outra linha à sua condição, escolha **Adicionar** > **a linha**. 
   Para adicionar um grupo com subcondições, escolha **Adicionar** > **grupo**. 
   Para agrupar as linhas existentes, selecione as caixas de verificação para essas linhas, escolha o botão elipses (...) para qualquer linha e, em seguida, escolha **grupo Fazer**.

1. Em **se for verdade** e se for **falso,** adicione os passos a executar com base no facto de a condição estar reunida. Por exemplo:

   ![Condição com caminhos "Se verdadeiro" e "Se falso"](./media/logic-apps-control-flow-conditional-statement/condition-yes-no-path.png)

   > [!TIP]
   > Pode arrastar as ações existentes para o **"Se verdadeiro"** e **"Se caminhos** falsos".

1. Guarde a aplicação lógica.

Esta aplicação lógica envia agora correio apenas quando os novos itens no feed RSS satisfazem a sua condição.

## <a name="json-definition"></a>Definição JSON

Aqui está a definição de código de alto nível por trás de uma declaração condicional:

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
* Para submeter ou votar em funcionalidades e sugestões, visite o site de feedback do utilizador das [Aplicações Lógicas Azure.](https://aka.ms/logicapps-wish)

## <a name="next-steps"></a>Passos seguintes

* [Executar passos com base em diferentes valores (declarações de switch)](../logic-apps/logic-apps-control-flow-switch-statement.md)
* [Executar e repetir passos (loops)](../logic-apps/logic-apps-control-flow-loops.md)
* [Executar ou fundir passos paralelos (ramos)](../logic-apps/logic-apps-control-flow-branches.md)
* [Passos de execução baseados no estado de ação agruparado (âmbitos)](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)
