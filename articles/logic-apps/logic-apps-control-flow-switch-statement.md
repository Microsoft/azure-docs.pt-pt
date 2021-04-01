---
title: Adicionar declarações de switch aos fluxos de trabalho
description: Como criar declarações de switch que controlam ações de fluxo de trabalho com base em valores específicos em Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 10/08/2018
ms.openlocfilehash: 62f147104cf9e0e1605b02a420cb6d20190361b8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "89657450"
---
# <a name="create-switch-statements-that-run-workflow-actions-based-on-specific-values-in-azure-logic-apps"></a>Criar declarações de switch que executam ações de fluxo de trabalho com base em valores específicos em Azure Logic Apps

Para executar ações específicas com base nos valores de objetos, expressões ou fichas, adicione uma declaração *de comutação.* Esta estrutura avalia o objeto, expressão ou token, escolhe o caso que corresponde ao resultado, e executa ações específicas apenas para esse caso. Quando a declaração do interruptor for executado, apenas um caso deve coincidir com o resultado.

Por exemplo, suponha que quer uma aplicação lógica que tome diferentes passos com base numa opção selecionada no e-mail. Neste exemplo, a aplicação lógica verifica o feed RSS de um site para novos conteúdos. Quando um novo item aparece no feed RSS, a aplicação lógica envia e-mail para um aprovador. Com base no facto de o aprovador selecionar "Aprovar" ou "Rejeitar", a aplicação lógica segue diferentes passos.

> [!TIP]
> Como todas as linguagens de programação, as declarações de switch suportam apenas operadores de igualdade. Se precisar de outros operadores relacionais, como "maior do que", utilize uma [declaração condicional.](../logic-apps/logic-apps-control-flow-conditional-statement.md)
> Para garantir um comportamento de execução determinista, os casos devem conter um valor único e estático em vez de fichas ou expressões dinâmicas.

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição, [inscreva-se numa conta do Azure gratuita](https://azure.microsoft.com/free/).

* Para seguir o exemplo neste artigo, [crie esta app lógica de amostra](../logic-apps/quickstart-create-first-logic-app-workflow.md) com uma conta Outlook.com ou uma conta de trabalho ou escola.

  1. Quando adicionar a ação para enviar e-mail, encontre e selecione esta ação em vez disso: **Envie um e-mail de aprovação**

     ![Selecione "Enviar um e-mail de aprovação"](./media/logic-apps-control-flow-switch-statement/send-approval-email-action.png)

  1. Forneça os campos necessários, como o endereço de e-mail para a pessoa que recebe o e-mail de aprovação. 
  Nas **Opções do Utilizador**, insira "Aprovar, Rejeitar".

     ![Insira detalhes do e-mail](./media/logic-apps-control-flow-switch-statement/send-approval-email-details.png)

## <a name="add-switch-statement"></a>Adicionar declaração de switch

1. Para este exemplo, adicione uma declaração de comutação no final do seu fluxo de trabalho da amostra. Após o último passo, escolha **Novo passo.**

   Quando pretender adicionar uma declaração de comutação entre os passos, mova o ponteiro sobre a seta onde pretende adicionar a declaração do interruptor. Escolha o **sinal de mais** **+** () que aparece e, em seguida, escolha **Adicionar uma ação**.

1. Na caixa de pesquisa, introduza o "switch" como filtro. Selecione esta ação: **Switch - Controle**

   ![Adicionar interruptor](./media/logic-apps-control-flow-switch-statement/add-switch-statement.png)

   Uma declaração de comutação aparece com um caso e um caso predefinido. 
   Por predefinição, uma declaração de comutação requer pelo menos um caso mais o caso padrão. 

   ![Declaração de switch padrão vazia](./media/logic-apps-control-flow-switch-statement/empty-switch.png)

1. Clique dentro da caixa **On** para que apareça a lista de conteúdos dinâmicos. A partir dessa lista, selecione o campo **Escolha de Opção** cuja saída determina a ação a realizar. 

   ![Selecione "Escolha seletiva"](./media/logic-apps-control-flow-switch-statement/select-selected-option.png)

1. Para tratar dos casos em que o aprovador seleciona `Approve` ou , adicione outro caso entre `Reject` **Caso** e **Predefinição**. 

   ![Adicione outro caso](./media/logic-apps-control-flow-switch-statement/switch-plus.png)

1. Adicione estas ações aos casos correspondentes:

   | Caso # | **Opção Selecionada** | Ação |
   |--------|--------------------|--------|
   | Caso 1 | **Aprovar** | Adicionar o Outlook Enviar por email o artigo **Enviar por email** o artigo RSS apenas quando o aprovador selecionado **aprovar.** |
   | Caso 2 | **Rejeitar** | Adicionar o Outlook Enviar por email uma ação por **e-mail** para notificar outros aprovadores de que o item RSS foi rejeitado. |
   | Predefinição | Nenhum | Não é necessária ação. Neste exemplo, o caso **Padrão** está vazio porque **a SelectOption** tem apenas duas opções. |
   |||

   ![Declaração de switch terminada](./media/logic-apps-control-flow-switch-statement/finished-switch.png)

1. Guarde a sua aplicação lógica. 

   Para testar manualmente este exemplo, escolha **Executar** até que a aplicação lógica encontre um novo item RSS e envie um e-mail de aprovação. 
   **Selecione Aprovar** para observar os resultados.

## <a name="json-definition"></a>Definição JSON

Agora que criou uma aplicação lógica usando uma declaração de comutação, vamos olhar para a definição de código de alto nível por trás da declaração do Switch.

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

| Etiqueta | Description |
|-------|-------------|
| `"Switch"`         | O nome da declaração do switch, que pode renomear para a legibilidade |
| `"type": "Switch"` | Especifica que a ação é uma declaração de comutação |
| `"expression"`     | Neste exemplo, especifica a opção selecionada do aprovador que é avaliada em cada caso, conforme declarado posteriormente na definição |
| `"cases"` | Define qualquer número de casos. Para cada caso, `"Case_*"` é o nome padrão para esse caso, que pode renomear para a legibilidade |
| `"case"` | Especifica o valor do caso, que deve ser um valor constante e único que a declaração do comutação utiliza para comparação. Se nenhum caso corresponder ao resultado da expressão do interruptor, as ações na `"default"` secção são executadas. | 
| | | 

## <a name="get-support"></a>Obter suporte

* Para dúvidas, visite a página de perguntas do [Microsoft Q&Uma página de perguntas para aplicações Azure Logic](/answers/topics/azure-logic-apps.html).
* Para submeter ou votar em funcionalidades ou sugestões, visite o site de feedback do [utilizador do Azure Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Passos seguintes

* [Passos de execução com base numa condição (declarações condicionais)](../logic-apps/logic-apps-control-flow-conditional-statement.md)
* [Executar e repetir passos (laços)](../logic-apps/logic-apps-control-flow-loops.md)
* [Executar ou fundir etapas paralelas (ramos)](../logic-apps/logic-apps-control-flow-branches.md)
* [Passos de execução baseados no estado de ação agrupado (âmbitos)](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)
