---
title: Adicione loops para repetir ações
description: Criar ciclos que repetem as ações do fluxo de trabalho e processam as matrizes no Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 01/05/2019
ms.openlocfilehash: 88f1c88e721419bf944207b9c748b9250a25f428
ms.sourcegitcommit: 99955130348f9d2db7d4fb5032fad89dad3185e7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93348071"
---
# <a name="create-loops-that-repeat-workflow-actions-or-process-arrays-in-azure-logic-apps"></a>Criar ciclos que repetem as ações do fluxo de trabalho e processam as matrizes no Azure Logic Apps

Para processar um conjunto na sua aplicação lógica, pode criar um [loop "Foreach".](#foreach-loop) Este loop repete uma ou mais ações em cada item da matriz. Para o limite do número de itens de matriz que um laço "Foreach" pode processar, consulte [Concurrency, looping e limites de debacação](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits).

Para repetir ações até que uma condição seja satisfeita ou um estado mude, pode criar um [loop "Até".](#until-loop) A sua aplicação lógica executa primeiro todas as ações dentro do loop, e depois verifica a condição ou estado. Se a condição for cumprida, o ciclo para. Caso contrário, o laço repete-se. Para o limite do número de loops "Até" que uma aplicação lógica pode ter, ver [Concurrency, looping e debatching limites](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits).

> [!TIP]
> Se tiver um gatilho que receba uma matriz e queira executar um fluxo de trabalho para cada item de matriz, pode *debaste* essa matriz com a propriedade do gatilho [ **SplitOn**](../logic-apps/logic-apps-workflow-actions-triggers.md#split-on-debatch).

## <a name="prerequisites"></a>Pré-requisitos

* Uma conta e subscrição do Azure. Se não tiver uma subscrição, [inscreva-se numa conta do Azure gratuita](https://azure.microsoft.com/free/). 

* Conhecimento básico sobre [como criar aplicativos lógicos](../logic-apps/quickstart-create-first-logic-app-workflow.md)

<a name="foreach-loop"></a>

## <a name="foreach-loop"></a>Loop "Foreach"

Um laço "Foreach" repete uma ou mais ações em cada item de matriz e funciona apenas em matrizes. Aqui ficam algumas considerações quando se usa os loops "Foreach":

* O laço "Foreach" pode processar um número limitado de itens de matriz. Para este limite, consulte [Os limites de Concurrency, looping e debatching](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits).

* Por predefinição, as iterações num laço "Foreach" são executadas ao mesmo tempo, ou paralelamente. Este comportamento difere da [ **Aplicação** da Power Automamate a cada ciclo](/power-automate/apply-to-each) onde as iterações são executadas uma de cada vez, ou sequencialmente. No entanto, pode [configurar iterações sequenciais em loop "Foreach".](#sequential-foreach-loop) Por exemplo, se pretender fazer uma pausa na próxima iteração num laço "Foreach", utilizando a [ação Delay](../connectors/connectors-native-delay.md), tem de definir o laço para funcionar sequencialmente.

  A exceção ao comportamento padrão são laços aninhados onde as iterações são sempre executadas sequencialmente, não em paralelo. Para executar operações paralelas para itens em loop aninhado, crie e [chame uma aplicação de lógica infantil](../logic-apps/logic-apps-http-endpoint.md).

* Para obter resultados previsíveis das operações em variáveis durante cada iteração em loop, executar esses loops sequencialmente. Por exemplo, quando um loop em execução simultâneo termina, o incremento, decremento e apego às operações variáveis devolvem resultados previsíveis. No entanto, durante cada iteração no ciclo de funcionamento simultâneo, estas operações podem devolver resultados imprevisíveis. 

* Ações em um loop "Foreach" usar o [`@item()`](../logic-apps/workflow-definition-language-functions-reference.md#item) 
expressão à referência e processar cada item na matriz. Se especificar dados que não estão num array, o fluxo de trabalho da aplicação lógica falha. 

Esta aplicação lógica de exemplo envia um resumo diário para um feed RSS do site. A aplicação usa um loop "Foreach" que envia um e-mail para cada novo item.

1. [Crie esta aplicação lógica de amostra](../logic-apps/quickstart-create-first-logic-app-workflow.md) com uma conta Outlook.com ou uma conta de trabalho ou escola.

2. Entre o gatilho RSS e enviar ação de e-mail, adicione um loop "Foreach". 

   1. Para adicionar um laço entre passos, mova o ponteiro sobre a seta entre esses degraus. 
   Escolha o **sinal de mais** **+** () que aparece e, em seguida, **selecione Adicione uma ação**.

      ![Selecione "Adicionar uma ação"](media/logic-apps-control-flow-loops/add-for-each-loop.png)

   1. Debaixo da caixa de pesquisa, escolha **Todos.** Na caixa de pesquisa, escreva "para cada um" como filtro. A partir da lista de ações, selecione esta ação: **Para cada um - Controlo**

      ![Adicione loop "Para cada"](media/logic-apps-control-flow-loops/select-for-each.png)

3. Agora constrói o laço. Em **Selecionar uma saída dos passos anteriores** após a produção da lista **de conteúdos dinâmicos Add,** selecione a matriz de links de **alimentação,** que é saída do gatilho RSS. 

   ![Selecione a partir da lista de conteúdos dinâmicos](media/logic-apps-control-flow-loops/for-each-loop-dynamic-content-list.png)

   > [!NOTE] 
   > Pode selecionar *apenas* saídas de matriz do passo anterior.

   A matriz selecionada aparece agora aqui:

   ![Selecione matriz](media/logic-apps-control-flow-loops/for-each-loop-select-array.png)

4. Para executar uma ação em cada item de matriz, arraste a ação **enviar um e-mail** para o loop. 

   A sua aplicação lógica pode parecer algo com este exemplo:

   ![Adicione passos ao laço "Foreach"](media/logic-apps-control-flow-loops/for-each-loop-with-step.png)

5. Guarde a sua aplicação lógica. Para testar manualmente a sua aplicação lógica, na barra de ferramentas do designer, escolha **Executar**.

<a name="for-each-json"></a>

## <a name="foreach-loop-definition-json"></a>Definição de loop "Foreach" (JSON)

Se estiver a trabalhar na visão de código para a sua aplicação lógica, pode definir o `Foreach` loop na definição JSON da sua aplicação lógica, por exemplo:

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

## <a name="foreach-loop-sequential"></a>Loop "Foreach": Sequencial

Por predefinição, os ciclos num loop "Foreach" funcionam em paralelo. Para executar cada ciclo sequencialmente, desacorda a opção **sequencial** do loop. Os laços "Foreach" devem ser executados sequencialmente quando tiver laços aninhados ou variáveis dentro de loops onde se espera resultados previsíveis. 

1. No canto superior direito do laço, escolha **elipses** **(...** ) > **Definições**.

   ![No loop "Foreach", escolha "..." > "Definições"](media/logic-apps-control-flow-loops/for-each-loop-settings.png)

1. Sob **controlo de concurrency,** rode a definição **de Controlo de Concurrency** para **On**. Mova o **Curso de Paralelismo** para **1** , e escolha **Feito**.

   ![Ligue o controlo da conuncy](media/logic-apps-control-flow-loops/for-each-loop-sequential-setting.png)

Se estiver a trabalhar com a definição JSON da sua aplicação lógica, pode utilizar a `Sequential` opção adicionando o `operationOptions` parâmetro, por exemplo:

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

## <a name="until-loop"></a>Loop "Até"
  
Para executar e repetir ações até que uma condição seja satisfeita ou um estado mude, coloque essas ações num loop "Até". A sua aplicação lógica executa primeiro todas e quaisquer ações dentro do loop, e depois verifica a condição ou estado. Se a condição for cumprida, o ciclo para. Caso contrário, o laço repete-se. Para o limite do número de loops "Até" que uma aplicação lógica pode ter, ver [Concurrency, looping e debatching limites](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits).

Aqui estão alguns cenários comuns onde você pode usar um loop "Até":

* Ligue para um ponto final até obter a resposta que quiser.

* Crie um registo numa base de dados. Espere até que um campo específico nesse registo seja aprovado. Continue a processar. 

A partir das 8:00 todos os dias, este exemplo de aplicação lógica incrementa uma variável até que o valor da variável seja igual a 10. A aplicação lógica envia então um e-mail que confirma o valor atual. 

> [!NOTE]
> Estes passos utilizam o Office 365 Outlook, mas pode utilizar qualquer fornecedor de e-mail que a Logic Apps suporte. 
> [Consulte aqui a lista de conectores.](/connectors/) Se utilizar outra conta de e-mail, os passos gerais permanecem os mesmos, mas a sua UI pode parecer ligeiramente diferente. 

1. Criar uma aplicação lógica em branco. No Logic App Designer, sob a caixa de pesquisa, escolha **Todos.** Procure por "recorrência". 
   A partir da lista de gatilhos, selecione este gatilho: **Recorrência - Agenda**

   ![Adicionar gatilho "Recorrência - Agendar"](./media/logic-apps-control-flow-loops/do-until-loop-add-trigger.png)

1. Especificar quando o gatilho dispara definindo o intervalo, a frequência e a hora do dia. Para definir a hora, escolha **as opções avançadas do Show**.

   ![Configurar o calendário de recorrência](./media/logic-apps-control-flow-loops/do-until-loop-set-trigger-properties.png)

   | Propriedade | Valor |
   | -------- | ----- |
   | **Intervalo** | 1 | 
   | **Frequência** | Dia |
   | **At these hours** (A estas horas) | 8 |
   ||| 

1. Sob o gatilho, escolha **novo passo.** 
   Procure por "variáveis", e selecione esta ação: **Inicializar variável - Variáveis**

   ![Adicionar ação "Inicializar variável - Variáveis"](./media/logic-apps-control-flow-loops/do-until-loop-add-variable.png)

1. Configurar a sua variável com estes valores:

   ![Definir propriedades variáveis](./media/logic-apps-control-flow-loops/do-until-loop-set-variable-properties.png)

   | Propriedade | Valor | Descrição |
   | -------- | ----- | ----------- |
   | **Nome** | Limite | O nome da sua variável | 
   | **Tipo** | Número inteiro | O tipo de dados da sua variável | 
   | **Valor** | 0 | O valor inicial da sua variável | 
   |||| 

1. Sob a ação **variável Inicialize,** escolha **Novo passo.** 

1. Debaixo da caixa de pesquisa, escolha **Todos.** Procure por "até", e selecione esta ação: **Até - Controlo**

   ![Adicione loop "Até".](./media/logic-apps-control-flow-loops/do-until-loop-add-until-loop.png)

1. Construa a condição de saída do loop selecionando a variável **Limite** e o operador **é igual.** 
   Insira **10** como valor de comparação.

   ![Construa condições de saída para o circuito de paragem](./media/logic-apps-control-flow-loops/do-until-loop-settings.png)

1. Dentro do laço, escolha **Adicionar uma ação**. 

1. Debaixo da caixa de pesquisa, escolha **Todos.** Procure por "variáveis", e selecione esta ação: **Incremento variável - Variáveis**

   ![Adicione ação para incrementar variável](./media/logic-apps-control-flow-loops/do-until-loop-increment-variable.png)

1. Para **nome,** selecione a variável **Limite.** Para **Valor** , insira "1". 

     ![Incremento "Limite" por 1](./media/logic-apps-control-flow-loops/do-until-loop-increment-variable-settings.png)

1. Fora e sob o circuito, escolha **Novo passo.** 

1. Debaixo da caixa de pesquisa, escolha **Todos.** 
     Encontre e adicione uma ação que envia e-mail, por exemplo: 

     ![Adicionar ação que envia e-mail](media/logic-apps-control-flow-loops/do-until-loop-send-email.png)

1. Se tal lhe for solicitado, inicie sessão na sua conta de e-mail.

1. Desa estade as propriedades da ação de e-mail. Adicione a variável **Limite** ao sujeito. Desta forma, pode confirmar que o valor atual da variável cumpre a sua condição especificada, por exemplo:

      ![Configurar propriedades de e-mail](./media/logic-apps-control-flow-loops/do-until-loop-send-email-settings.png)

      | Propriedade | Valor | Descrição |
      | -------- | ----- | ----------- | 
      | **Para** | *\<email-address\@domain>* | O endereço de e-mail do destinatário. Para testar, use o seu próprio endereço de e-mail. | 
      | **Assunto** | Valor atual para "Limite" é **Limite** | Especifique o assunto do e-mail. Para este exemplo, certifique-se de que inclui a variável **Limite.** | 
      | **Corpo** | <*conteúdo de e-mail*> | Especifique o conteúdo da mensagem de e-mail que pretende enviar. Para este exemplo, insira o texto que quiser. | 
      |||| 

1. Guarde a sua aplicação lógica. Para testar manualmente a sua aplicação lógica, na barra de ferramentas do designer, escolha **Executar**.

      Depois de a sua lógica começar a funcionar, recebe um e-mail com o conteúdo especificado:

      ![E-mail recebido](./media/logic-apps-control-flow-loops/do-until-loop-sent-email.png)

## <a name="prevent-endless-loops"></a>Evitar ciclos intermináveis

Um ciclo "Até" tem limites predefinidos que impedem a execução se alguma destas condições acontecer:

| Propriedade | Valor predefinido | Descrição | 
| -------- | ------------- | ----------- | 
| **Contagem** | 60 | O maior número de loops que correm antes da saída do loop. O padrão é de 60 ciclos. | 
| **Intervalo de tempo** | PT1H | A maior quantidade de tempo para dar um loop antes da saída do circuito. O padrão é de uma hora e é especificado no formato ISO 8601. <p>O valor de tempo limite é avaliado para cada ciclo de ciclo. Se qualquer ação no circuito demorar mais do que o limite de tempo, o ciclo atual não para. No entanto, o próximo ciclo não começa porque a condição limite não está cumprida. | 
|||| 

Para alterar estes limites predefinidos, escolha **Mostrar opções avançadas** na forma de ação do loop.

<a name="until-json"></a>

## <a name="until-definition-json"></a>Definição "Até" (JSON)

Se estiver a trabalhar na visão de código para a sua aplicação lógica, pode definir um `Until` loop na definição JSON da sua aplicação lógica, por exemplo:

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

Este exemplo "Até" chama um ponto final HTTP, que cria um recurso. O loop para quando o corpo de resposta HTTP regressa com `Completed` estado. Para evitar ciclos intermináveis, o loop também para se alguma destas condições acontecer:

* O loop correu 10 vezes como especificado pelo `count` atributo. O padrão é 60 vezes. 

* O loop durou duas horas conforme especificado no `timeout` atributo no formato ISO 8601. O padrão é de uma hora.
  
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

* Para dúvidas, visite a página de perguntas do [Microsoft Q&Uma página de perguntas para aplicações Azure Logic](/answers/topics/azure-logic-apps.html).
* Para submeter ou votar em funcionalidades e sugestões, [o site de feedback do utilizador da Azure Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Próximos passos

* [Passos de execução com base numa condição (declarações condicionais)](../logic-apps/logic-apps-control-flow-conditional-statement.md)
* [Passos de execução com base em diferentes valores (declarações de switch)](../logic-apps/logic-apps-control-flow-switch-statement.md)
* [Executar ou fundir etapas paralelas (ramos)](../logic-apps/logic-apps-control-flow-branches.md)
* [Passos de execução baseados no estado de ação agrupado (âmbitos)](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)
