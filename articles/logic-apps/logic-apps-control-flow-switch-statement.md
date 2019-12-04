---
title: Adicionar instruções switch a fluxos de trabalho
description: Como criar instruções switch que controlam ações de fluxo de trabalho com base em valores específicos em aplicativos lógicos do Azure
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 10/08/2018
ms.openlocfilehash: 5c40feec2dca65e4bc9617a71a6d0a8e4c872a3a
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74793228"
---
# <a name="create-switch-statements-that-run-workflow-actions-based-on-specific-values-in-azure-logic-apps"></a>Criar instruções switch que executam ações de fluxo de trabalho com base em valores específicos em aplicativos lógicos do Azure

Para executar ações específicas com base nos valores de objetos, expressões ou tokens, adicione uma instrução *switch* . Essa estrutura avalia o objeto, a expressão ou o token, escolhe o caso que corresponde ao resultado e executa ações específicas somente para esse caso. Quando a instrução switch é executada, somente um caso deve corresponder ao resultado.

Por exemplo, suponha que você queira um aplicativo lógico que usa etapas diferentes com base em uma opção selecionada no email. Neste exemplo, o aplicativo lógico verifica o RSS feed de um site para obter um novo conteúdo. Quando um novo item é exibido no RSS feed, o aplicativo lógico envia um email para um Aprovador. Com base em se o aprovador seleciona "aprovar" ou "rejeitar", o aplicativo lógico segue etapas diferentes.

> [!TIP]
> Como todas as linguagens de programação, as instruções switch dão suporte apenas a operadores de igualdade. Se você precisar de outros operadores relacionais, como "maior que", use uma [instrução condicional](../logic-apps/logic-apps-control-flow-conditional-statement.md).
> Para garantir o comportamento de execução determinística, os casos devem conter um valor exclusivo e estático em vez de tokens ou expressões dinâmicas.

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição, [inscreva-se numa conta do Azure gratuita](https://azure.microsoft.com/free/).

* Para seguir o exemplo neste artigo, [crie este aplicativo lógico de exemplo](../logic-apps/quickstart-create-first-logic-app-workflow.md) com uma conta do Outlook Outlook.com ou Office 365.

  1. Ao adicionar a ação para enviar email, localize e selecione esta ação em vez disso: **enviar um email de aprovação**

     ![Selecione "enviar um email de aprovação"](./media/logic-apps-control-flow-switch-statement/send-approval-email-action.png)

  1. Forneça os campos obrigatórios, como o endereço de email para a pessoa que recebe o email de aprovação. 
  Em **Opções do usuário**, digite "aprovar, rejeitar".

     ![Inserir detalhes do email](./media/logic-apps-control-flow-switch-statement/send-approval-email-details.png)

## <a name="add-switch-statement"></a>Adicionar instrução switch

1. Para este exemplo, adicione uma instrução switch no final do fluxo de trabalho de exemplo. Após a última etapa, escolha **nova etapa**.

   Quando você quiser adicionar uma instrução switch entre etapas, mova o ponteiro sobre a seta onde você deseja adicionar a instrução switch. Escolha o **sinal de adição** ( **+** ) que aparece e, em seguida, escolha **Adicionar uma ação**.

1. Na caixa de pesquisa, digite "alternar" como filtro. Selecione esta ação: **switch-Control**

   ![Adicionar opção](./media/logic-apps-control-flow-switch-statement/add-switch-statement.png)

   Uma instrução switch é exibida com um caso e um caso padrão. 
   Por padrão, uma instrução switch requer pelo menos um caso e o caso padrão. 

   ![Instrução de comutador padrão vazia](./media/logic-apps-control-flow-switch-statement/empty-switch.png)

1. Clique dentro da caixa **em** para que a lista de conteúdo dinâmico seja exibida. Nessa lista, selecione o campo **SelectedOption** cuja saída determina a ação a ser executada. 

   ![Selecione "SelectedOption"](./media/logic-apps-control-flow-switch-statement/select-selected-option.png)

1. Para lidar com os casos em que o aprovador seleciona `Approve` ou `Reject`, adicione outro caso entre o **caso** e o **padrão**. 

   ![Adicionar outro caso](./media/logic-apps-control-flow-switch-statement/switch-plus.png)

1. Adicione essas ações aos casos correspondentes:

   | Casos # | **SelectedOption** | Ação |
   |--------|--------------------|--------|
   | Caso 1 | **Confirmar** | Adicionar a ação **enviar um email** do Outlook para enviar detalhes sobre o item RSS somente quando o aprovador selecionado **aprovar**. |
   | Caso 2 | **Rejeitar** | Adicionar a ação **enviar um email** do Outlook para notificar outros Aprovadores de que o item RSS foi rejeitado. |
   | Predefinição | Nenhuma | Nenhuma ação necessária. Neste exemplo, o caso **padrão** é vazio porque **SelectedOption** tem apenas duas opções. |
   |||

   ![Instrução switch concluída](./media/logic-apps-control-flow-switch-statement/finished-switch.png)

1. Guarde a aplicação lógica. 

   Para testar manualmente este exemplo, escolha **executar** até que o aplicativo lógico Localize um novo item RSS e envie um email de aprovação. 
   Selecione **aprovar** para observar os resultados.

## <a name="json-definition"></a>Definição de JSON

Agora que você criou um aplicativo lógico usando uma instrução switch, vamos examinar a definição de código de alto nível por trás da instrução switch.

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

| Label | Descrição |
|-------|-------------|
| `"Switch"`         | O nome da instrução switch, que pode ser renomeada para facilitar a leitura |
| `"type": "Switch"` | Especifica que a ação é uma instrução switch |
| `"expression"`     | Neste exemplo, especifica a opção selecionada do aprovador que é avaliada em cada caso como declarado posteriormente na definição |
| `"cases"` | Define qualquer número de casos. Para cada caso, `"Case_*"` é o nome padrão para esse caso, que pode ser renomeado para facilitar a leitura |
| `"case"` | Especifica o valor do caso, que deve ser uma constante e um valor exclusivo que a instrução switch usa para comparação. Se nenhum caso corresponder ao resultado da expressão switch, as ações na seção `"default"` serão executadas. | 
| | | 

## <a name="get-support"></a>Obter suporte

* Relativamente a dúvidas, visite o [fórum do Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Para enviar ou votar em recursos ou sugestões, visite o [site de comentários do usuário dos aplicativos lógicos do Azure](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Passos seguintes

* [Executar etapas com base em uma condição (instruções condicionais)](../logic-apps/logic-apps-control-flow-conditional-statement.md)
* [Executar e repetir etapas (loops)](../logic-apps/logic-apps-control-flow-loops.md)
* [Executar ou mesclar etapas paralelas (branches)](../logic-apps/logic-apps-control-flow-branches.md)
* [Executar etapas com base no status da ação agrupada (escopos)](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)
