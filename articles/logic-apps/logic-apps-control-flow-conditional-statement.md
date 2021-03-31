---
title: Adicionar declarações condicionais aos fluxos de trabalho
description: Como criar condições que controlem ações em fluxos de trabalho em Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 10/09/2018
ms.openlocfilehash: 19f42270e37c42cf56bbde04f73c01027a56ff63
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "89657390"
---
# <a name="create-conditional-statements-that-control-workflow-actions-in-azure-logic-apps"></a>Criar declarações condicionais que controlam as ações de fluxo de trabalho em Azure Logic Apps

Para executar ações específicas na sua aplicação lógica apenas após a passagem de uma condição especificada, adicione uma *declaração condicional*. Esta estrutura de controlo compara os dados no seu fluxo de trabalho face a valores ou campos específicos. Em seguida, pode especificar diferentes ações que são executadas com base no facto de os dados cumprirem ou não a condição. Podem nidificar as condições dentro umas das outras.

Por exemplo, suponha que tem uma aplicação lógica que envia muitos e-mails quando novos itens aparecem no feed RSS de um site. Pode adicionar uma declaração condicional para enviar e-mail apenas quando o novo item inclui uma cadeia específica. 

> [!TIP]
> Para executar diferentes passos com base em diferentes valores específicos, utilize uma [*declaração de comutação.*](../logic-apps/logic-apps-control-flow-switch-statement.md)

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição, [inscreva-se numa conta do Azure gratuita](https://azure.microsoft.com/free/).

* Conhecimento básico sobre [como criar aplicativos lógicos](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Para seguir o exemplo neste artigo, [crie esta app lógica de amostra](../logic-apps/quickstart-create-first-logic-app-workflow.md) com uma conta Outlook.com ou uma conta de trabalho ou escola.

## <a name="add-condition"></a>Adicionar condição

1. No <a href="https://portal.azure.com" target="_blank">portal Azure,</a>abra a sua aplicação lógica no Logic App Designer.

1. Adicione uma condição no local que deseja. 

   Para adicionar uma condição entre os passos, mova o ponteiro sobre a seta onde pretende adicionar a condição. Escolha o **sinal de mais** **+** () que aparece e, em seguida, escolha **Adicionar uma ação**. Por exemplo:

   ![Adicionar ação entre passos](./media/logic-apps-control-flow-conditional-statement/add-action.png)

   Quando quiser adicionar uma condição no final do seu fluxo de trabalho, na parte inferior da sua aplicação lógica, escolha  **Novo passo** Adicione > **uma ação**.

1. Na caixa de pesquisa, introduza "condição" como filtro. Selecione esta ação: **Condição - Controlo**

   ![Adicionar condição](./media/logic-apps-control-flow-conditional-statement/add-condition.png)

1. Na caixa **de condição,** construa a sua condição. 

   1. Na caixa esquerda, especifique os dados ou campo que pretende comparar.

      Quando clica dentro da caixa esquerda, aparece a lista de conteúdos dinâmicos para que possa selecionar saídas de passos anteriores na sua aplicação lógica. 
      Para este exemplo, selecione o resumo do feed RSS.

      ![Construa a sua condição](./media/logic-apps-control-flow-conditional-statement/edit-condition.png)

   1. Na caixa central, selecione a operação para realizar. 
   Para este exemplo, selecione "**contém**". 

   1. Na caixa direita, especifique um valor ou campo como critérios. 
   Para este exemplo, especifique esta cadeia: **Microsoft**

   Aqui está a condição completa:

   ![Concluir condição](./media/logic-apps-control-flow-conditional-statement/edit-condition-2.png)

   Para adicionar mais uma linha à sua condição, escolha  >  **Adicionar linha**. 
   Para adicionar um grupo com subcondições, escolha **adicionar**  >  **grupo**. 
   Para agrupar as linhas existentes, selecione as caixas de verificação para essas linhas, escolha as elipses (...) botão para qualquer linha e, em seguida, escolha **Fazer grupo**.

1. Em **Se Verdadeiro** e Em Caso de **Mentira,** adicione os passos a executar com base no facto de a condição ser cumprida. Por exemplo:

   ![Condição com caminhos "Se verdadeiro" e "Se falso"](./media/logic-apps-control-flow-conditional-statement/condition-yes-no-path.png)

   > [!TIP]
   > Pode arrastar as ações existentes para o **"Se verdadeiro"** e **"Se forem caminhos** falsos".

1. Guarde a sua aplicação lógica.

Esta aplicação lógica envia agora correio apenas quando os novos itens no feed RSS cumprem a sua condição.

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

* Para dúvidas, visite a página de perguntas do [Microsoft Q&Uma página de perguntas para aplicações Azure Logic](/answers/topics/azure-logic-apps.html).
* Para submeter ou votar em funcionalidades e sugestões, visite o site de feedback do [utilizador do Azure Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Passos seguintes

* [Passos de execução com base em diferentes valores (declarações de switch)](../logic-apps/logic-apps-control-flow-switch-statement.md)
* [Executar e repetir passos (laços)](../logic-apps/logic-apps-control-flow-loops.md)
* [Executar ou fundir etapas paralelas (ramos)](../logic-apps/logic-apps-control-flow-branches.md)
* [Passos de execução baseados no estado de ação agrupado (âmbitos)](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)
