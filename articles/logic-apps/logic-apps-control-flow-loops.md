---
title: Adicionar loops a ações de repetição
description: Criar loops que repetem ações de fluxo de trabalho ou matrizes de processo em aplicativos lógicos do Azure
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 01/05/2019
ms.openlocfilehash: 5f6c04c9a57dc8c250d99f2fa944203d2d73c404
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74791758"
---
# <a name="create-loops-that-repeat-workflow-actions-or-process-arrays-in-azure-logic-apps"></a>Criar loops que repetem ações de fluxo de trabalho ou matrizes de processo em aplicativos lógicos do Azure

Para processar uma matriz em seu aplicativo lógico, você pode criar um [loop "foreach"](#foreach-loop). Esse loop repete uma ou mais ações em cada item na matriz. Para limites no número de itens de matriz que os loops "foreach" podem processar, consulte [limites e configuração](../logic-apps/logic-apps-limits-and-config.md). 

Para repetir ações até que uma condição seja atendida ou um estado seja alterado, você pode criar um [loop "until"](#until-loop). O aplicativo lógico primeiro executa todas as ações dentro do loop e, em seguida, verifica a condição ou o estado. Se a condição for atendida, o loop será interrompido. Caso contrário, o loop se repetirá. Para limites no número de loops "until" em uma execução de aplicativo lógico, consulte [limites e configuração](../logic-apps/logic-apps-limits-and-config.md). 

> [!TIP]
> Se você tiver um gatilho que recebe uma matriz e quiser executar um fluxo de trabalho para cada item de matriz, poderá desvincular *essa matriz* com a propriedade de gatilho de [ **divisão** ](../logic-apps/logic-apps-workflow-actions-triggers.md#split-on-debatch). 

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição, [inscreva-se numa conta do Azure gratuita](https://azure.microsoft.com/free/). 

* Conhecimento básico sobre [como criar aplicativos lógicos](../logic-apps/quickstart-create-first-logic-app-workflow.md)

<a name="foreach-loop"></a>

## <a name="foreach-loop"></a>Loop "foreach"

Um "loop foreach" repete uma ou mais ações em cada item de matriz e funciona apenas em matrizes. As iterações em um loop "foreach" são executadas em paralelo. No entanto, você pode executar iterações uma de cada vez Configurando um [loop "foreach" sequencial](#sequential-foreach-loop). 

Aqui estão algumas considerações quando você usa loops "foreach":

* Em loops aninhados, as iterações sempre são executadas em sequência, não em paralelo. Para executar operações em paralelo para itens em um loop aninhado, crie e [chame um aplicativo lógico filho](../logic-apps/logic-apps-http-endpoint.md).

* Para obter resultados previsíveis de operações em variáveis durante cada iteração de loop, execute os loops sequencialmente. Por exemplo, quando um loop em execução simultâneo termina, as operações de incremento, decréscimo e acréscimo a variáveis retornam resultados previsíveis. No entanto, durante cada iteração no loop em execução simultânea, essas operações podem retornar resultados imprevisíveis. 

* As ações em um loop "foreach" usam o [`@item()`](../logic-apps/workflow-definition-language-functions-reference.md#item) 
expressão para referenciar e processar cada item na matriz. Se você especificar dados que não estão em uma matriz, o fluxo de trabalho do aplicativo lógico falhará. 

Este aplicativo lógico de exemplo envia um resumo diário para um RSS feed de site. O aplicativo usa um loop "foreach" que envia um email para cada novo item.

1. [Crie este aplicativo lógico de exemplo](../logic-apps/quickstart-create-first-logic-app-workflow.md) com uma conta do Outlook Outlook.com ou Office 365.

2. Entre o gatilho de RSS e a ação enviar email, adicione um loop "foreach". 

   1. Para adicionar um loop entre as etapas, mova o ponteiro sobre a seta entre essas etapas. 
   Escolha o **sinal de adição** ( **+** ) que aparece e, em seguida, selecione **Adicionar uma ação**.

      ![Selecione "adicionar uma ação"](media/logic-apps-control-flow-loops/add-for-each-loop.png)

   1. Na caixa de pesquisa, escolha **tudo**. Na caixa de pesquisa, digite "para cada" como seu filtro. Na lista ações, selecione esta ação: **para cada controle**

      ![Adicionar loop "for each"](media/logic-apps-control-flow-loops/select-for-each.png)

3. Agora, compile o loop. Em **selecionar uma saída das etapas anteriores** após a lista **adicionar conteúdo dinâmico** aparecer, selecione a matriz **links de feed** , que é a saída do gatilho RSS. 

   ![Selecionar na lista de conteúdo dinâmico](media/logic-apps-control-flow-loops/for-each-loop-dynamic-content-list.png)

   > [!NOTE] 
   > Você pode selecionar *apenas* saídas de matriz da etapa anterior.

   A matriz selecionada agora aparece aqui:

   ![Selecionar matriz](media/logic-apps-control-flow-loops/for-each-loop-select-array.png)

4. Para executar uma ação em cada item de matriz, arraste a ação **enviar um email** para o loop. 

   Seu aplicativo lógico pode ser semelhante a este exemplo:

   ![Adicionar etapas ao loop "foreach"](media/logic-apps-control-flow-loops/for-each-loop-with-step.png)

5. Guarde a aplicação lógica. Para testar manualmente seu aplicativo lógico, na barra de ferramentas do designer, escolha **executar**.

<a name="for-each-json"></a>

## <a name="foreach-loop-definition-json"></a>Definição de loop "foreach" (JSON)

Se você estiver trabalhando no modo de exibição de código para seu aplicativo lógico, poderá definir o loop de `Foreach` na definição JSON do aplicativo lógico, por exemplo:

``` json
"actions": {
   "myForEachLoopName": {
      "type": "Foreach",
      "actions": {
         "Send_an_email": {
            "type": "ApiConnection",
            "inputs": {
               "body": {
                  "Body": "@{item()}",
                  "Subject": "New CNN post @{triggerBody()?['publishDate']}",
                  "To": "me@contoso.com"
               },
               "host": {
                  "api": {
                     "runtimeUrl": "https://logic-apis-westus.azure-apim.net/apim/office365"
                  },
                  "connection": {
                     "name": "@parameters('$connections')['office365']['connectionId']"
                  }
               },
               "method": "post",
               "path": "/Mail"
            },
            "runAfter": {}
         }
      },
      "foreach": "@triggerBody()?['links']",
      "runAfter": {}
   }
}
```

<a name="sequential-foreach-loop"></a>

## <a name="foreach-loop-sequential"></a>Loop "foreach": sequencial

Por padrão, os ciclos em um loop "foreach" são executados em paralelo. Para executar cada ciclo sequencialmente, defina a opção **sequencial** do loop. Loops "foreach" devem ser executados sequencialmente quando você tem loops aninhados ou variáveis dentro de loops em que você espera resultados previsíveis. 

1. No canto superior direito do loop, escolha **reticências** ( **...** ) > **configurações**.

   ![No loop "foreach", escolha "..." > "Configurações"](media/logic-apps-control-flow-loops/for-each-loop-settings.png)

1. Em **controle de simultaneidade**, ative a configuração de **controle de simultaneidade** como **ativada**. Mova o controle deslizante **grau de paralelismo** para **1**e escolha **concluído**.

   ![Ativar o controle de simultaneidade](media/logic-apps-control-flow-loops/for-each-loop-sequential-setting.png)

Se você estiver trabalhando com a definição de JSON do aplicativo lógico, poderá usar a opção `Sequential` adicionando o parâmetro `operationOptions`, por exemplo:

``` json
"actions": {
   "myForEachLoopName": {
      "type": "Foreach",
      "actions": {
         "Send_an_email": { }
      },
      "foreach": "@triggerBody()?['links']",
      "runAfter": {},
      "operationOptions": "Sequential"
   }
}
```

<a name="until-loop"></a>

## <a name="until-loop"></a>Loop "until"
  
Para executar e repetir ações até que uma condição seja atendida ou um estado é alterado, coloque essas ações em um loop "until". Seu aplicativo lógico primeiro executa qualquer e todas as ações dentro do loop e, em seguida, verifica a condição ou o estado. Se a condição for atendida, o loop será interrompido. Caso contrário, o loop se repetirá.

Aqui estão alguns cenários comuns em que você pode usar um loop "until":

* Chame um ponto de extremidade até obter a resposta desejada.

* Crie um registro em um banco de dados. Aguarde até que um campo específico nesse registro seja aprovado. Continuar o processamento. 

A partir de 8:00, todos os dias, esse aplicativo lógico de exemplo incrementa uma variável até que o valor da variável seja igual a 10. Em seguida, o aplicativo lógico envia um email que confirma o valor atual. 

> [!NOTE]
> Essas etapas usam o Outlook do Office 365, mas você pode usar qualquer provedor de email ao qual os aplicativos lógicos ofereçam suporte. 
> [Verifique a lista de conectores aqui](https://docs.microsoft.com/connectors/). Se você usar outra conta de email, as etapas gerais permanecerão as mesmas, mas sua interface do usuário poderá parecer um pouco diferente. 

1. Criar uma aplicação lógica em branco. No designer de aplicativo lógico, na caixa de pesquisa, escolha **tudo**. Procure "recorrência". 
   Na lista de gatilhos, selecione este gatilho: **recorrência-agenda**

   ![Adicionar o gatilho "recorrência-agendamento"](./media/logic-apps-control-flow-loops/do-until-loop-add-trigger.png)

1. Especifique quando o gatilho será acionado definindo o intervalo, a frequência e a hora do dia. Para definir a hora, escolha **Mostrar opções avançadas**.

   ![Configurar a agenda de recorrência](./media/logic-apps-control-flow-loops/do-until-loop-set-trigger-properties.png)

   | Propriedade | Valor |
   | -------- | ----- |
   | **Intervalo** | 1 | 
   | **Frequência** | Dia |
   | **At these hours** (A estas horas) | 8 |
   ||| 

1. No gatilho, escolha **nova etapa**. 
   Pesquise por "variáveis" e selecione esta ação: **inicializar variáveis de variável**

   ![Adicionar ação "inicializar variável-variáveis"](./media/logic-apps-control-flow-loops/do-until-loop-add-variable.png)

1. Configure sua variável com estes valores:

   ![Definir propriedades da variável](./media/logic-apps-control-flow-loops/do-until-loop-set-variable-properties.png)

   | Propriedade | Valor | Descrição |
   | -------- | ----- | ----------- |
   | **Nome** | Limite | O nome da variável | 
   | **Tipo** | Número inteiro | O tipo de dados da variável | 
   | **Valor** | 0 | O valor inicial de sua variável | 
   |||| 

1. Na ação **inicializar variável** , escolha **nova etapa**. 

1. Na caixa de pesquisa, escolha **tudo**. Pesquise "até" e selecione esta ação: **até-controle**

   ![Adicionar loop "until"](./media/logic-apps-control-flow-loops/do-until-loop-add-until-loop.png)

1. Crie a condição de saída do loop selecionando a variável **Limit** e o operador **is equal** . 
   Insira **10** como o valor de comparação.

   ![Condição de saída de compilação para parar loop](./media/logic-apps-control-flow-loops/do-until-loop-settings.png)

1. Dentro do loop, escolha **Adicionar uma ação**. 

1. Na caixa de pesquisa, escolha **tudo**. Pesquise "Variables" e selecione esta ação: **incrementar variável variáveis**

   ![Adicionar ação para incrementar variável](./media/logic-apps-control-flow-loops/do-until-loop-increment-variable.png)

1. Para **nome**, selecione a variável de **limite** . Para **valor**, insira "1". 

     ![Incrementar "limite" em 1](./media/logic-apps-control-flow-loops/do-until-loop-increment-variable-settings.png)

1. Fora e abaixo do loop, escolha **nova etapa**. 

1. Na caixa de pesquisa, escolha **tudo**. 
     Localize e adicione uma ação que envia email, por exemplo: 

     ![Adicionar ação que envia email](media/logic-apps-control-flow-loops/do-until-loop-send-email.png)

1. Se tal lhe for solicitado, inicie sessão na sua conta de e-mail.

1. Defina as propriedades da ação de email. Adicione a variável de **limite** à entidade. Dessa forma, você pode confirmar se o valor atual da variável atende à condição especificada, por exemplo:

      ![Configurar propriedades de email](./media/logic-apps-control-flow-loops/do-until-loop-send-email-settings.png)

      | Propriedade | Valor | Descrição |
      | -------- | ----- | ----------- | 
      | **Para** | *\<endereço de email\@domínio >* | o endereço de email do destinatário. Para teste, use seu próprio endereço de email. | 
      | **Assunto** | O valor atual para "Limit" é **limite** | Especifique o assunto do email. Para este exemplo, certifique-se de incluir a variável **limite** . | 
      | **Corpo** | <*email-> de conteúdo* | Especifique o conteúdo da mensagem de email que você deseja enviar. Para este exemplo, insira qualquer texto que você desejar. | 
      |||| 

1. Guarde a aplicação lógica. Para testar manualmente seu aplicativo lógico, na barra de ferramentas do designer, escolha **executar**.

      Depois que a lógica começar a ser executada, você receberá um email com o conteúdo que você especificou:

      ![Email recebido](./media/logic-apps-control-flow-loops/do-until-loop-sent-email.png)

## <a name="prevent-endless-loops"></a>Evitar loops intermináveis

Um loop "until" tem limites padrão que interrompem a execução se qualquer uma dessas condições acontecer:

| Propriedade | Valor predefinido | Descrição | 
| -------- | ------------- | ----------- | 
| **Contar** | 60 | O número mais alto de loops executados antes do loop ser encerrado. O padrão é 60 ciclos. | 
| **Cedido** | PT1H | A maior quantidade de tempo para executar um loop antes do loop ser encerrado. O padrão é uma hora e é especificado no formato ISO 8601. <p>O valor de tempo limite é avaliado para cada ciclo de loop. Se qualquer ação no loop demorar mais do que o tempo limite, o ciclo atual não será interrompido. No entanto, o próximo ciclo não é iniciado porque a condição de limite não é atendida. | 
|||| 

Para alterar esses limites padrão, escolha **Mostrar opções avançadas** na forma ação de loop.

<a name="until-json"></a>

## <a name="until-definition-json"></a>Definição "until" (JSON)

Se você estiver trabalhando no modo de exibição de código para seu aplicativo lógico, poderá definir um loop de `Until` na definição JSON do aplicativo lógico, por exemplo:

``` json
"actions": {
   "Initialize_variable": {
      // Definition for initialize variable action
   },
   "Send_an_email": {
      // Definition for send email action
   },
   "Until": {
      "type": "Until",
      "actions": {
         "Increment_variable": {
            "type": "IncrementVariable",
            "inputs": {
               "name": "Limit",
               "value": 1
            },
            "runAfter": {}
         }
      },
      "expression": "@equals(variables('Limit'), 10)",
      // To prevent endless loops, an "Until" loop 
      // includes these default limits that stop the loop. 
      "limit": { 
         "count": 60,
         "timeout": "PT1H"
      },
      "runAfter": {
         "Initialize_variable": [
            "Succeeded"
         ]
      }
   }
}
```

Este loop de exemplo "until" chama um ponto de extremidade HTTP, que cria um recurso. O loop para quando o corpo da resposta HTTP retorna com o status `Completed`. Para evitar loops intermináveis, o loop também parará se alguma dessas condições ocorrer:

* O loop foi executado 10 vezes, conforme especificado pelo atributo `count`. O padrão é 60 vezes. 

* O loop foi executado por duas horas, conforme especificado pelo atributo `timeout` no formato ISO 8601. O padrão é uma hora.
  
``` json
"actions": {
   "myUntilLoopName": {
      "type": "Until",
      "actions": {
         "Create_new_resource": {
            "type": "Http",
            "inputs": {
               "body": {
                  "resourceId": "@triggerBody()"
               },
               "url": "https://domain.com/provisionResource/create-resource",
               "body": {
                  "resourceId": "@triggerBody()"
               }
            },
            "runAfter": {},
            "type": "ApiConnection"
         }
      },
      "expression": "@equals(triggerBody(), 'Completed')",
      "limit": {
         "count": 10,
         "timeout": "PT2H"
      },
      "runAfter": {}
   }
}
```

## <a name="get-support"></a>Obter suporte

* Relativamente a dúvidas, visite o [fórum do Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Para enviar ou votar em recursos e sugestões, [site de comentários do usuário dos aplicativos lógicos do Azure](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Passos seguintes

* [Executar etapas com base em uma condição (instruções condicionais)](../logic-apps/logic-apps-control-flow-conditional-statement.md)
* [Executar etapas com base em valores diferentes (instruções switch)](../logic-apps/logic-apps-control-flow-switch-statement.md)
* [Executar ou mesclar etapas paralelas (branches)](../logic-apps/logic-apps-control-flow-branches.md)
* [Executar etapas com base no status da ação agrupada (escopos)](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)
