---
title: Adicionar declarações do comutador para fluxos de trabalho - Azure Logic Apps | Documentos da Microsoft
description: Como criar declarações do comutador que controlam as ações de fluxo de trabalho com base nos valores específicos nas Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
ms.date: 10/08/2018
ms.openlocfilehash: 2a3f8ee5cba3110d392555fad78c1cb2513b5d4e
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60683181"
---
# <a name="create-switch-statements-that-run-workflow-actions-based-on-specific-values-in-azure-logic-apps"></a>Criar demonstrativos de comutador que executam ações de fluxo de trabalho com base nos valores específicos nas Azure Logic Apps

Para executar ações específicas com base nos valores dos objetos, expressões ou tokens, adicione uma *mudar* instrução. Esta estrutura avalia o objeto, expressão ou token, escolhe o caso que faz corresponder o resultado e executa as ações específicas apenas para esse caso. Quando é executada a instrução switch, apenas um cenário deve corresponder o resultado.

Por exemplo, suponha que pretende que uma aplicação lógica que toma as medidas diferentes com base numa opção selecionada no e-mail. Neste exemplo, a aplicação lógica verifica o RSS feed para o novo conteúdo de um Web site. Quando um novo item aparece no RSS feed, a aplicação lógica envia um e-mail para um aprovador. Com base em se o aprovador seleciona "Aprovar" ou "Rejeitar", a aplicação lógica segue os passos diferentes.

> [!TIP]
> Como todas as linguagens de programação, as declarações do comutador suportam apenas os operadores de igualdade. Se precisar de outros operadores relacionais, como "maior que", utilize um [instrução condicional](../logic-apps/logic-apps-control-flow-conditional-statement.md).
> Para garantir o comportamento de execução determinística, casos tem de conter um valor exclusivo e estático em vez de tokens dinâmicos ou expressões.

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição, [inscreva-se numa conta do Azure gratuita](https://azure.microsoft.com/free/).

* A seguir o exemplo neste artigo, [criar esta aplicação de lógica de exemplo](../logic-apps/quickstart-create-first-logic-app-workflow.md) com uma conta do Outlook.com ou o Outlook do Office 365.

  1. Ao adicionar a ação para enviar correio eletrónico, localize e selecione a ação em vez disso: **Enviar um e-mail de aprovação**

     ![Selecione "Enviar um e-mail de aprovação"](./media/logic-apps-control-flow-switch-statement/send-approval-email-action.png)

  1. Forneça os campos obrigatórios, como o endereço de e-mail da pessoa que obtém a mensagem de e-mail de aprovação. 
  Sob **opções do utilizador**, introduza "Aprovar, rejeitar".

     ![Introduza os detalhes de e-mail](./media/logic-apps-control-flow-switch-statement/send-approval-email-details.png)

## <a name="add-switch-statement"></a>Adicionar instrução

1. Neste exemplo, adicione uma instrução de comutador no final do fluxo de trabalho de exemplo. Após a última etapa, escolha **novo passo**.

   Quando deseja adicionar uma instrução de comutador entre etapas, mova o ponteiro sobre a seta para onde pretende adicionar a instrução switch. Escolha o **sinal** ( **+** ) que aparece, em seguida, escolha **adicionar uma ação**.

1. Na caixa de pesquisa, introduza "alternar" como o filtro. Selecione a ação: **Comutador - controlo**

   ![Adicionar o comutador](./media/logic-apps-control-flow-switch-statement/add-switch-statement.png)

   Uma instrução switch é apresentada com um caso e um caso de predefinição. 
   Por predefinição, uma instrução switch requer, pelo menos, um caso mais o caso de predefinição. 

   ![Falha da instrução vazia padrão](./media/logic-apps-control-flow-switch-statement/empty-switch.png)

1. Clique no interior da **no** caixa para que a lista de conteúdo dinâmico apareça. A partir dessa lista, selecione o **SelectedOption** campo cuja saída determina a ação a efetuar. 

   ![Selecione "SelectedOption"](./media/logic-apps-control-flow-switch-statement/select-selected-option.png)

1. Para lidar com casos em que o aprovador seleciona `Approve` ou `Reject`, adicione outro caso entre **caso** e **predefinido**. 

   ![Adicionar outro caso](./media/logic-apps-control-flow-switch-statement/switch-plus.png)

1. Adicione estas ações para os casos correspondentes:

   | N. º maiúsculas | **SelectedOption** | Ação |
   |--------|--------------------|--------|
   | Caso 1 | **Aprovar** | Adicionar o Outlook **enviar um e-mail** ação para o envio de detalhes do item RSS apenas quando o aprovador selecionou **aprovar**. |
   | Caso 2 | **Reject** | Adicionar o Outlook **enviar um e-mail** ação para notificar os outros aprovadores que o item RSS foi rejeitado. |
   | Predefinição | Nenhuma | Nenhuma ação necessária. Neste exemplo, o **predefinido** caso está vazio porque **SelectedOption** tem apenas duas opções. |
   |||

   ![Instrução concluído](./media/logic-apps-control-flow-switch-statement/finished-switch.png)

1. Guarde a aplicação lógica. 

   Para testar manualmente neste exemplo, escolha **executar** até que a aplicação lógica encontrar um novo item RSS e envia um e-mail de aprovação. 
   Selecione **aprovar** para observar os resultados.

## <a name="json-definition"></a>Definição de JSON

Agora que criou uma aplicação lógica com uma instrução switch, vamos analisar a definição de alto nível de código por trás da instrução switch.

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
| `"Switch"`         | O nome da instrução switch, que pode renomear para facilitar a leitura |
| `"type": "Switch"` | Especifica que a ação é uma instrução switch |
| `"expression"`     | Neste exemplo, especifica a opção selecionada do aprovador, o que é avaliada em comparação com cada caso, como declarado mais tarde na definição |
| `"cases"` | Define a qualquer número de casos. Para cada caso, `"Case_*"` é o nome predefinido para esse caso, o que pode renomear para facilitar a leitura |
| `"case"` | Especifica o valor do caso, o que deve ser um valor constante e é exclusivo e que a instrução switch utiliza para comparação. Se não existem casos correspondem o resultado da expressão de comutador, as ações no `"default"` secção são executados. | 
| | | 

## <a name="get-support"></a>Obter suporte

* Relativamente a dúvidas, visite o [fórum do Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Para submeter ou votar em funcionalidades ou sugestões, visite o [site de comentários de utilizadores do Azure Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Passos Seguintes

* [Execute os passos com base numa condição (instruções condicionais)](../logic-apps/logic-apps-control-flow-conditional-statement.md)
* [Executar e repita os passos (loops)](../logic-apps/logic-apps-control-flow-loops.md)
* [Executar ou unir a passos paralelos (ramos)](../logic-apps/logic-apps-control-flow-branches.md)
* [Execute os passos com base no estado da ação agrupados (âmbitos)](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)
