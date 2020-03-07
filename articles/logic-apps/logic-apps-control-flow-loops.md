---
title: Adicione loops para repetir ações
description: Crie loops que repitam ações de fluxo de trabalho ou matrizes de processos em Aplicações Lógicas Azure
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 01/05/2019
ms.openlocfilehash: 5f6c04c9a57dc8c250d99f2fa944203d2d73c404
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78358743"
---
# <a name="create-loops-that-repeat-workflow-actions-or-process-arrays-in-azure-logic-apps"></a>Crie loops que repitam ações de fluxo de trabalho ou matrizes de processos em Aplicações Lógicas Azure

Para processar uma matriz na sua aplicação lógica, pode criar um [loop "Foreach".](#foreach-loop) Este loop repete uma ou mais ações em cada item da matriz. Para limites no número de itens de matriz que os loops "Foreach" podem processar, ver [Limites e configuração](../logic-apps/logic-apps-limits-and-config.md). 

Para repetir as ações até que uma condição seja satisfeita ou um estado mude, pode criar um [loop "Until".](#until-loop) A sua aplicação lógica executa primeiro todas as ações dentro do loop e, em seguida, verifica a condição ou estado. Se a condição for satisfeita, o laço para. Caso contrário, o laço repete-se. Para limites no número de loops "Até" numa aplicação lógica, consulte [Limites e configuração](../logic-apps/logic-apps-limits-and-config.md). 

> [!TIP]
> Se tiver um gatilho que receba uma matriz e queira executar um fluxo de trabalho para cada item de matriz, pode *desempantar* essa matriz com a propriedade do gatilho [ **SplitOn** ](../logic-apps/logic-apps-workflow-actions-triggers.md#split-on-debatch). 

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição, [inscreva-se numa conta do Azure gratuita](https://azure.microsoft.com/free/). 

* Conhecimento básico sobre [como criar aplicações lógicas](../logic-apps/quickstart-create-first-logic-app-workflow.md)

<a name="foreach-loop"></a>

## <a name="foreach-loop"></a>Loop "Foreach"

Um "foreach loop" repete uma ou mais ações em cada item de matriz e funciona apenas em matrizes. Iterações num ciclo "Foreach" correm em paralelo. No entanto, pode executar iterações uma de cada vez, configurando um [ciclo sequencial "Foreach".](#sequential-foreach-loop) 

Aqui ficam algumas considerações quando utiliza loops "Foreach":

* Em laços aninhados, as iterações sempre correm sequencialmente, não em paralelo. Para executar operações paralelas a itens num laço aninhado, crie e [chame uma aplicação lógica infantil](../logic-apps/logic-apps-http-endpoint.md).

* Para obter resultados previsíveis de operações em variáveis durante cada iteração de loop, executar esses loops sequencialmente. Por exemplo, quando um ciclo de execução em simultâneo termina, o incremento, o decreamento e o apêndice a operações variáveis retornam resultados previsíveis. No entanto, durante cada iteração no ciclo de execução em simultâneo, estas operações podem dar resultados imprevisíveis. 

* As ações num ciclo "Foreach" utilizam o [`@item()`](../logic-apps/workflow-definition-language-functions-reference.md#item) 
expressão para referência e processar cada item na matriz. Se especificar dados que não estão numa matriz, o fluxo de trabalho da aplicação lógica falha. 

Esta aplicação lógica de exemplo envia um resumo diário para um feed RSS do site. A aplicação usa um loop "Foreach" que envia um e-mail para cada novo item.

1. [Crie esta aplicação lógica de amostra](../logic-apps/quickstart-create-first-logic-app-workflow.md) com uma conta Outlook.com ou Office 365 Outlook.

2. Entre o gatilho RSS e a ação de e-mail, adicione um loop "Foreach". 

   1. Para adicionar um laço entre passos, mova o ponteiro sobre a seta entre esses degraus. 
   Escolha o **sinal plus** **(+)** que aparece e, em seguida, selecione **Adicionar uma ação**.

      ![Selecione "Adicionar uma ação"](media/logic-apps-control-flow-loops/add-for-each-loop.png)

   1. Debaixo da caixa de pesquisa, escolha **All**. Na caixa de pesquisa, digite "para cada um" como filtro. Da lista de ações, selecione esta ação: **Para cada um - Controlo**

      ![Adicione o loop "Para cada"](media/logic-apps-control-flow-loops/select-for-each.png)

3. Agora construa o loop. Em **selecionar uma saída de passos anteriores** após a verificação da lista de conteúdos **dinâmicos Adicionar,** selecione a matriz de **links feed,** que é saída do gatilho RSS. 

   ![Selecione na lista de conteúdos dinâmicos](media/logic-apps-control-flow-loops/for-each-loop-dynamic-content-list.png)

   > [!NOTE] 
   > Só pode *selecionar* saídas de matriz a partir do passo anterior.

   A matriz selecionada aparece agora aqui:

   ![Selecione matriz](media/logic-apps-control-flow-loops/for-each-loop-select-array.png)

4. Para executar uma ação em cada item de matriz, arraste a ação **de envio de um e-mail** para o loop. 

   A sua aplicação lógica pode parecer-se com este exemplo:

   ![Adicione passos ao loop "Foreach"](media/logic-apps-control-flow-loops/for-each-loop-with-step.png)

5. Guarde a aplicação lógica. Para testar manualmente a sua aplicação lógica, na barra de ferramentas de design, escolha **Run**.

<a name="for-each-json"></a>

## <a name="foreach-loop-definition-json"></a>Definição de loop "Foreach" (JSON)

Se estiver a trabalhar em vista de código para a sua aplicação lógica, pode definir o loop `Foreach` na definição JSON da sua aplicação lógica, por exemplo:

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

Por padrão, os ciclos num ciclo "Foreach" funcionam em paralelo. Para executar cada ciclo sequencialmente, detete a opção **sequencial** do loop. Os laços "Foreach" devem ser executados sequencialmente quando se tem laços ou variáveis aninhados dentro de loops onde se espera resultados previsíveis. 

1. No canto superior direito do loop, escolha **elipses** ( **...** ) > **Definições**.

   ![No loop "Foreach", escolha "..." > "Definições"](media/logic-apps-control-flow-loops/for-each-loop-settings.png)

1. Sob **controlo de moeda,** ligue a definição de **Controlo de Moedas** para **ligar**. Mova o **grau de deslizador do paralelismo** para **1**, e escolha **Done**.

   ![Ligue o controlo da concurrency](media/logic-apps-control-flow-loops/for-each-loop-sequential-setting.png)

Se estiver a trabalhar com a definição JSON da sua aplicação lógica, pode utilizar a opção `Sequential` adicionando o parâmetro `operationOptions`, por exemplo:

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
  
Para executar e repetir ações até que uma condição seja satisfeita ou um estado mude, coloque essas ações num ciclo "Até". A sua aplicação lógica executa primeiro todas e quaisquer ações dentro do loop, e depois verifica a condição ou estado. Se a condição for satisfeita, o laço para. Caso contrário, o laço repete-se.

Aqui estão alguns cenários comuns onde você pode usar um loop "Até":

* Ligue para um ponto final até obter a resposta que quiser.

* Crie um registo numa base de dados. Espere até que um campo específico desse registo seja aprovado. Continue a processar. 

A partir das 8:00 da manhã de cada dia, esta aplicação lógica de exemplo incrementa uma variável até que o valor da variável seja igual a 10. A aplicação lógica envia então um e-mail que confirma o valor atual. 

> [!NOTE]
> Estes passos utilizam o Office 365 Outlook, mas pode utilizar qualquer fornecedor de e-mail que as Aplicações Lógicas suportem. 
> [Consulte a lista de conectores aqui](https://docs.microsoft.com/connectors/). Se utilizar outra conta de e-mail, os passos gerais permanecem os mesmos, mas a sua UI pode parecer ligeiramente diferente. 

1. Criar uma aplicação lógica em branco. Na Logic App Designer, sob a caixa de pesquisa, escolha **All**. Procurar por "recorrência". 
   A partir da lista de gatilhos, selecione este gatilho: **Recurrence - Agendar**

   ![Adicionar gatilho "Recurrence - Agendar"](./media/logic-apps-control-flow-loops/do-until-loop-add-trigger.png)

1. Especifique quando o gatilho dispara, afinando o intervalo, a frequência e a hora do dia. Para definir a hora, escolha **Mostrar opções avançadas.**

   ![Configurar o calendário de recorrência](./media/logic-apps-control-flow-loops/do-until-loop-set-trigger-properties.png)

   | Propriedade | Valor |
   | -------- | ----- |
   | **Intervalo** | 1 | 
   | **Frequência** | Dia |
   | **At these hours** (A estas horas) | 8 |
   ||| 

1. Sob o gatilho, escolha **novo passo**. 
   Procure por "variáveis", e selecione esta ação: **Inicializar variável - Variáveis**

   ![Adicionar ação "Inicializar variável - Variáveis"](./media/logic-apps-control-flow-loops/do-until-loop-add-variable.png)

1. Configura risa a sua variável com estes valores:

   ![Definir propriedades variáveis](./media/logic-apps-control-flow-loops/do-until-loop-set-variable-properties.png)

   | Propriedade | Valor | Descrição |
   | -------- | ----- | ----------- |
   | **Nome** | Limite | O nome da sua variável | 
   | **Tipo** | Número inteiro | O tipo de dados da sua variável | 
   | **Valor** | 0 | O valor inicial da sua variável | 
   |||| 

1. Sob a **ação variável Inicializar,** escolha **novo passo**. 

1. Debaixo da caixa de pesquisa, escolha **All**. Procure "até", e selecione esta ação: **Até - Controlo**

   ![Adicione o laço "Até"](./media/logic-apps-control-flow-loops/do-until-loop-add-until-loop.png)

1. Construa a condição de saída do loop selecionando a variável **Limite** e o **operador é igual.** 
   Insira **10** como o valor de comparação.

   ![Construir condição de saída para parar loop](./media/logic-apps-control-flow-loops/do-until-loop-settings.png)

1. Dentro do laço, escolha **Adicionar uma ação**. 

1. Debaixo da caixa de pesquisa, escolha **All**. Procure por "variáveis", e selecione esta ação: **Incremento variável - Variáveis**

   ![Adicionar ação para incrementar variável](./media/logic-apps-control-flow-loops/do-until-loop-increment-variable.png)

1. Para **Nome**, selecione a variável **Limite.** Para **Valor,** insira "1". 

     ![Incremento "Limite" por 1](./media/logic-apps-control-flow-loops/do-until-loop-increment-variable-settings.png)

1. Fora e sob o laço, escolha **novo passo.** 

1. Debaixo da caixa de pesquisa, escolha **All**. 
     Encontre e adicione uma ação que envie e-mail, por exemplo: 

     ![Adicione ação que envia e-mail](media/logic-apps-control-flow-loops/do-until-loop-send-email.png)

1. Se tal lhe for solicitado, inicie sessão na sua conta de e-mail.

1. Detete as propriedades da ação de e-mail. Adicione a variável **Limite** ao sujeito. Desta forma, pode confirmar que o valor atual da variável satisfaz a sua condição especificada, por exemplo:

      ![Configurar propriedades de e-mail](./media/logic-apps-control-flow-loops/do-until-loop-send-email-settings.png)

      | Propriedade | Valor | Descrição |
      | -------- | ----- | ----------- | 
      | **Para** | *\<endereço de e-mail\@domínio>* | O endereço de e-mail do destinatário. Para testes, utilize o seu próprio endereço de e-mail. | 
      | **Assunto** | Valor atual para "Limite" é **Limite** | Especifique o assunto do e-mail. Para este exemplo, certifique-se de que inclui a variável **Limite.** | 
      | **Corpo** | <> *de e-mail-conteúdo* | Especifique o conteúdo da mensagem de e-mail que pretende enviar. Para este exemplo, insira o texto que quiser. | 
      |||| 

1. Guarde a aplicação lógica. Para testar manualmente a sua aplicação lógica, na barra de ferramentas de design, escolha **Run**.

      Depois de a sua lógica começar a funcionar, recebe um e-mail com o conteúdo que especificou:

      ![E-mail recebido](./media/logic-apps-control-flow-loops/do-until-loop-sent-email.png)

## <a name="prevent-endless-loops"></a>Prevenir laços intermináveis

Um loop "Até" tem limites de incumprimento que param a execução se alguma destas condições acontecer:

| Propriedade | Valor predefinido | Descrição | 
| -------- | ------------- | ----------- | 
| **Contagem** | 60 | O maior número de voltas que correm antes da saída do loop. O padrão é de 60 ciclos. | 
| **Intervalo** | PT1H | A maior quantidade de tempo para fazer um loop antes que o loop saia. O predefinido é de uma hora e é especificado no formato ISO 8601. <p>O valor do tempo de paragem é avaliado para cada ciclo de loop. Se qualquer ação no loop demorar mais tempo do que o limite de tempo, o ciclo atual não para. No entanto, o próximo ciclo não começa porque a condição limite não é cumprida. | 
|||| 

Para alterar estes limites predefinidos, escolha **Mostrar opções avançadas** na forma de ação em loop.

<a name="until-json"></a>

## <a name="until-definition-json"></a>Definição "Até" (JSON)

Se estiver a trabalhar em vista de código para a sua aplicação lógica, pode definir um `Until` loop na definição JSON da sua aplicação lógica, por exemplo:

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

Este ciclo de exemplo "Até" chama um ponto final HTTP, que cria um recurso. O laço para quando o corpo de resposta HTTP retorna com `Completed` estado. Para evitar laços intermináveis, o laço também para se alguma destas condições acontecer:

* O loop correu 10 vezes mais do que especificado pelo atributo `count`. O padrão é 60 vezes. 

* O loop funcionou durante duas horas, conforme especificado pelo `timeout` atributo no formato ISO 8601. O padrão é de uma hora.
  
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
* Para submeter ou votar em funcionalidades e sugestões, site de feedback do utilizador das [Aplicações Lógicas Azure.](https://aka.ms/logicapps-wish)

## <a name="next-steps"></a>Passos Seguintes

* [Passos de execução com base numa condição (declarações condicionais)](../logic-apps/logic-apps-control-flow-conditional-statement.md)
* [Executar passos com base em diferentes valores (declarações de switch)](../logic-apps/logic-apps-control-flow-switch-statement.md)
* [Executar ou fundir passos paralelos (ramos)](../logic-apps/logic-apps-control-flow-branches.md)
* [Passos de execução baseados no estado de ação agruparado (âmbitos)](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)
