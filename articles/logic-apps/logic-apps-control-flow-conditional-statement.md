---
title: Adicionar instruções condicionais para fluxos de trabalho - Azure Logic Apps | Documentos da Microsoft
description: Como criar condições que controlam ações em fluxos de trabalho no Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
ms.date: 10/09/2018
ms.openlocfilehash: 9ee484971e217b0ca4dd7ad855e9e6dc3313e5d4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60684805"
---
# <a name="create-conditional-statements-that-control-workflow-actions-in-azure-logic-apps"></a>Criar instruções condicionais que controlam ações de fluxo de trabalho no Azure Logic Apps

Para executar ações específicas na sua aplicação lógica apenas depois de passar uma condição especificada, adicione uma *instrução condicional*. Esta estrutura de controle compara os dados no fluxo de trabalho em relação a campos ou valores específicos. Em seguida, pode especificar diferentes ações que são executadas com base em se é ou não os dados atenda à condição. Pode aninhar condições dentro uns dos outros.

Por exemplo, suponha que tem uma aplicação lógica que envia e-mails demasiados quando aparecem itens novos no feed RSS de um site. Pode adicionar uma instrução condicional para enviar e-mail apenas quando o novo item inclui uma cadeia específica. 

> [!TIP]
> Para executar etapas diferentes com base em diferentes valores específicos, utilize um [ *mudar instrução* ](../logic-apps/logic-apps-control-flow-switch-statement.md) em vez disso.

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição, [inscreva-se numa conta do Azure gratuita](https://azure.microsoft.com/free/).

* Conhecimento básico sobre [como criar aplicações lógicas](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* A seguir o exemplo neste artigo, [criar esta aplicação de lógica de exemplo](../logic-apps/quickstart-create-first-logic-app-workflow.md) com uma conta do Outlook.com ou o Outlook do Office 365.

## <a name="add-condition"></a>Adicionar condição

1. Na <a href="https://portal.azure.com" target="_blank">portal do Azure</a>, abra a aplicação lógica no Estruturador da aplicação lógica.

1. Adicione uma condição na localização que pretende. 

   Para adicionar uma condição entre etapas, mova o ponteiro sobre a seta para onde pretende adicionar a condição. Escolha o **sinal** (**+**) que aparece, em seguida, escolha **adicionar uma ação**. Por exemplo:

   ![Adicionar ação entre passos](./media/logic-apps-control-flow-conditional-statement/add-action.png)

   Quando deseja adicionar uma condição no final do seu fluxo de trabalho, na parte inferior da sua aplicação lógica, escolha **novo passo** > **adicionar uma ação**.

1. Na caixa de pesquisa, introduza "condição" como o filtro. Selecione a ação: **Condição - controlo**

   ![Adicionar condição](./media/logic-apps-control-flow-conditional-statement/add-condition.png)

1. Na **condição** caixa, a condição de compilação. 

   1. Na caixa à esquerda, especifique os dados ou o campo que pretende comparar.

      Quando clica dentro da caixa à esquerda, é apresentada a lista de conteúdo dinâmico pelo que pode selecionar saídas dos passos anteriores na sua aplicação lógica. 
      Neste exemplo, selecione o resumo do RSS feed.

      ![Crie sua condição](./media/logic-apps-control-flow-conditional-statement/edit-condition.png)

   1. Na caixa do meio, selecione a operação a efetuar. 
   Para este exemplo, selecione "**contém**". 

   1. Na caixa da direita, especifique um valor ou o campo como seus critérios. 
   Neste exemplo, especifique esta cadeia: **Microsoft**

   Segue-se a condição concluída:

   ![Concluir condição](./media/logic-apps-control-flow-conditional-statement/edit-condition-2.png)

   Para adicionar outra linha para a condição, escolha **Add** > **Adicionar linha**. 
   Para adicionar um grupo com subconditions, escolha **Add** > **adicionar grupo**. 
   Para agrupar linhas existentes, selecione as caixas de verificação para as linhas, escolha o botão de reticências (...) para qualquer linha e, em seguida, escolha **grupo de marca**.

1. Sob **se for true** e **se for FALSO**, adicionar os passos para efetuar com base em se a condição é cumprida. Por exemplo:

   ![Condição com "Se verdadeiro" e os caminhos de "Se falso"](./media/logic-apps-control-flow-conditional-statement/condition-yes-no-path.png)

   > [!TIP]
   > Pode arrastar ações existentes para o **se for true** e **se for FALSO** caminhos.

1. Guarde a aplicação lógica.

Esta aplicação lógica agora envia correio apenas quando a condição de cumprir os novos itens no RSS feed.

## <a name="json-definition"></a>Definição de JSON

Esta é a definição de alto nível de código por trás de uma instrução condicional:

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
* Para submeter ou votar em recursos e sugestões, visite o [site de comentários de utilizadores do Azure Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Passos Seguintes

* [Execute os passos com base nos valores diferentes (declarações do comutador)](../logic-apps/logic-apps-control-flow-switch-statement.md)
* [Executar e repita os passos (loops)](../logic-apps/logic-apps-control-flow-loops.md)
* [Executar ou unir a passos paralelos (ramos)](../logic-apps/logic-apps-control-flow-branches.md)
* [Execute os passos com base no estado da ação agrupados (âmbitos)](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)
