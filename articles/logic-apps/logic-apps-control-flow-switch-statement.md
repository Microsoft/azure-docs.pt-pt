---
title: Adicione declarações de switch aos fluxos de trabalho
description: Como criar declarações de switch que controlam ações de fluxo de trabalho baseadas em valores específicos em Aplicações Lógicas Azure
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 10/08/2018
ms.openlocfilehash: 5c40feec2dca65e4bc9617a71a6d0a8e4c872a3a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74793228"
---
# <a name="create-switch-statements-that-run-workflow-actions-based-on-specific-values-in-azure-logic-apps"></a>Crie declarações de switch que executam ações de fluxo de trabalho com base em valores específicos em Aplicações Lógicas Azure

Para executar ações específicas com base nos valores de objetos, expressões ou fichas, adicione uma declaração de *comutação.* Esta estrutura avalia o objeto, expressão ou símbolo, escolhe o caso que corresponde ao resultado, e executa ações específicas apenas para esse caso. Quando a declaração da switch for terminada, apenas um caso deve corresponder ao resultado.

Por exemplo, suponha que quer uma aplicação lógica que tome diferentes passos com base numa opção selecionada no e-mail. Neste exemplo, a aplicação lógica verifica o feed RSS de um website para novos conteúdos. Quando um novo item aparece no feed RSS, a aplicação lógica envia e-mail a um aprovador. Com base no facto de o aprovador selecionar "Aprovar" ou "Rejeitar", a aplicação lógica segue diferentes passos.

> [!TIP]
> Como todas as linguagens de programação, as declarações da Switch suportam apenas os operadores de igualdade. Se precisar de outros operadores relacionais, como "maior do que", utilize uma [declaração condicional](../logic-apps/logic-apps-control-flow-conditional-statement.md).
> Para garantir o comportamento de execução determinista, os casos devem conter um valor único e estático em vez de tokens ou expressões dinâmicas.

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição, [inscreva-se numa conta do Azure gratuita](https://azure.microsoft.com/free/).

* Para seguir o exemplo neste artigo, [crie esta aplicação lógica](../logic-apps/quickstart-create-first-logic-app-workflow.md) de amostra com uma conta Outlook.com ou Office 365 Outlook.

  1. Quando adicionar a ação para enviar e-mail, encontre e selecione esta ação em vez disso: **Envie um e-mail** de aprovação

     ![Selecione "Enviar um e-mail de aprovação"](./media/logic-apps-control-flow-switch-statement/send-approval-email-action.png)

  1. Forneça os campos necessários, como o endereço de e-mail para a pessoa que recebe o e-mail de aprovação. 
  Em **opções de utilizador,** introduza "Aprovar, Rejeitar".

     ![Insira detalhes de e-mail](./media/logic-apps-control-flow-switch-statement/send-approval-email-details.png)

## <a name="add-switch-statement"></a>Adicionar declaração de comutação

1. Para este exemplo, adicione uma declaração de comutação no final do seu fluxo de trabalho da amostra. Depois do último passo, escolha **novo passo.**

   Quando pretender adicionar uma declaração de troca entre passos, mova o ponteiro sobre a seta onde pretende adicionar a declaração do interruptor. Escolha o sinal**+** de **mais** () que aparece e, em seguida, escolha Adicionar uma **ação**.

1. Na caixa de pesquisa, introduza "switch" como filtro. Selecione esta ação: **Switch - Controlo**

   ![Adicionar interruptor](./media/logic-apps-control-flow-switch-statement/add-switch-statement.png)

   Uma declaração de comutação aparece com um caso e um caso predefinido. 
   Por predefinição, uma declaração de comutação requer pelo menos um caso mais o caso predefinido. 

   ![Declaração de switch de padrão vazia](./media/logic-apps-control-flow-switch-statement/empty-switch.png)

1. Clique na caixa **On** para que a lista de conteúdos dinâmicos apareça. A partir dessa lista, selecione o campo **SelectOption** cuja saída determina a ação a realizar. 

   ![Selecione "SelectedOption"](./media/logic-apps-control-flow-switch-statement/select-selected-option.png)

1. Para tratar dos casos em `Approve` `Reject`que o aprovador seleciona ou, adicione outro caso entre **Case** e **Default**. 

   ![Adicione outro caso](./media/logic-apps-control-flow-switch-statement/switch-plus.png)

1. Adicione estas ações aos casos correspondentes:

   | Caso # | **Opção Selecionada** | Ação |
   |--------|--------------------|--------|
   | Caso 1 | **Aprovar** | Adicione o Outlook Enviar uma ação **de e-mail** para enviar detalhes sobre o item RSS apenas quando o aprovador selecionado **Aprovar**. |
   | Caso 2 | **Rejeitar** | Adicione o Outlook Enviar uma ação **de e-mail** para notificar outros aprovadores de que o item RSS foi rejeitado. |
   | Predefinição | Nenhuma | Não é necessária nenhuma ação. Neste exemplo, o caso **Predefinido** está vazio porque o **SelectedOption** tem apenas duas opções. |
   |||

   ![Declaração de comutação acabada](./media/logic-apps-control-flow-switch-statement/finished-switch.png)

1. Guarde a aplicação lógica. 

   Para testar manualmente este exemplo, escolha **Run** até que a aplicação lógica encontre um novo item RSS e envie um e-mail de aprovação. 
   Selecione **Aprovar** para observar os resultados.

## <a name="json-definition"></a>Definição JSON

Agora que criou uma aplicação lógica usando uma declaração de comutação, vamos olhar para a definição de código de alto nível por trás da declaração de switch.

``` json
"Switch": {
   "type": "Switch",
   "expression": "@body('Send_approval_email')?['SelectedOption']",
   "cases": {
      "Case": {
         "actions": {
           "Send_an_email": {}
         },
         "case" : "Approve"
      },
      "Case_2": {
         "actions": {
           "Send_an_email_2": {}
         },
         "case": "Reject"
      }
   },
   "default": {
      "actions": {}
   },
   "runAfter": {
      "Send_approval_email": [
         "Succeeded"
      ]
   }
}
```

| Etiqueta | Descrição |
|-------|-------------|
| `"Switch"`         | O nome da declaração da switch, que pode mudar o nome para a legibilidade |
| `"type": "Switch"` | Especifica que a ação é uma declaração de comutação |
| `"expression"`     | Neste exemplo, especifica a opção selecionada do aprovador que é avaliada em cada caso, como declarado posteriormente na definição |
| `"cases"` | Define vários casos. Para cada `"Case_*"` caso, é o nome padrão para esse caso, que pode mudar o nome para a legibilidade |
| `"case"` | Especifica o valor do caso, que deve ser um valor constante e único que a declaração da switch utiliza para comparação. Se nenhum caso corresponder ao resultado da `"default"` expressão da switch, as ações na secção são executadas. | 
| | | 

## <a name="get-support"></a>Obter suporte

* Relativamente a dúvidas, visite o [fórum do Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Para submeter ou votar em funcionalidades ou sugestões, visite o site de feedback do utilizador das [Aplicações Lógicas Do Azure.](https://aka.ms/logicapps-wish)

## <a name="next-steps"></a>Passos seguintes

* [Passos de execução com base numa condição (declarações condicionais)](../logic-apps/logic-apps-control-flow-conditional-statement.md)
* [Executar e repetir passos (loops)](../logic-apps/logic-apps-control-flow-loops.md)
* [Executar ou fundir passos paralelos (ramos)](../logic-apps/logic-apps-control-flow-branches.md)
* [Passos de execução baseados no estado de ação agruparado (âmbitos)](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)
