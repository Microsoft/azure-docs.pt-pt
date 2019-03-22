---
title: Adicionar loops que repetir ações ou processam matrizes - Azure Logic Apps | Documentos da Microsoft
description: Como criar loops que repetir ações de fluxo de trabalho ou processam matrizes no Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
manager: jeconnoc
ms.date: 01/05/2019
ms.topic: article
ms.openlocfilehash: c37e41bce481fff5e172687907cce527c10ae006
ms.sourcegitcommit: 12d67f9e4956bb30e7ca55209dd15d51a692d4f6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58225013"
---
# <a name="create-loops-that-repeat-workflow-actions-or-process-arrays-in-azure-logic-apps"></a>Criar ciclos que repetir ações de fluxo de trabalho ou processam matrizes no Azure Logic Apps

Para processar uma matriz na sua aplicação lógica, pode criar uma [loop "Foreach"](#foreach-loop). Este ciclo repete-se uma ou mais ações em cada item na matriz. Para podem processar limites no número de itens de matriz "Foreach" faz um loop, consulte [limites e configuração](../logic-apps/logic-apps-limits-and-config.md). 

Para repetir ações até que uma condição é cumprida ou um estado é alterado, pode criar uma ["loop até"](#until-loop). A aplicação lógica executa todas as ações dentro do loop e, em seguida, verifica o estado ou condição. Se a condição for cumprida, o loop para. Caso contrário, o loop se repetir. Limites no número de "Até" de loops numa aplicação lógica executar, veja [limites e configuração](../logic-apps/logic-apps-limits-and-config.md). 

> [!TIP]
> Se tiver um acionador que recebe uma matriz e quer executar um fluxo de trabalho para cada item da matriz, pode *debatch* essa matriz com o [ **SplitOn** acionar propriedade](../logic-apps/logic-apps-workflow-actions-triggers.md#split-on-debatch). 

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição, [inscreva-se numa conta do Azure gratuita](https://azure.microsoft.com/free/). 

* Conhecimento básico sobre [como criar aplicações lógicas](../logic-apps/quickstart-create-first-logic-app-workflow.md)

<a name="foreach-loop"></a>

## <a name="foreach-loop"></a>Loop "Foreach"

Um loop"Foreach" repete-se uma ou mais ações em cada item da matriz e funciona somente em matrizes. Executam iterações num loop "Foreach" em paralelo. No entanto, pode executar iterações um cada vez ao configurar uma [sequencial "Foreach" loop](#sequential-foreach-loop). 

Aqui estão algumas considerações ao usar os loops "Foreach":

* Em loops aninhados, iterações sempre são executados sequencialmente, não em paralelo. Para executar operações em paralelo para itens num loop aninhado, crie e [chamar uma aplicação de lógica de subordinados](../logic-apps/logic-apps-http-endpoint.md).

* Para obter resultados previsíveis de operações em variáveis durante cada iteração do loop, sequencialmente, execute os loops. Por exemplo, quando um em execução em simultâneo um loop termina, o incremento, diminuir e acrescentar à variável operações devolva resultados previsíveis. No entanto, durante cada iteração no loop em execução em simultâneo, estas operações podem devolver resultados imprevisíveis. 

* Utilize um loop de ações num "Foreach" a [`@item()`](../logic-apps/workflow-definition-language-functions-reference.md#item) 
expressão de referência e processar cada item da matriz. Se especificar dados que não esteja numa matriz, o fluxo de trabalho de aplicação lógica falha. 

Esta aplicação de lógica de exemplo envia um resumo diário para um feed RSS do Web site. A aplicação utiliza um loop "Foreach" que envia um e-mail para cada item novo.

1. [Criar esta aplicação de lógica de exemplo](../logic-apps/quickstart-create-first-logic-app-workflow.md) com uma conta do Outlook.com ou o Outlook do Office 365.

2. Entre o RSS acionar e enviar a ação de e-mail, adicionar um loop "Foreach". 

   1. Para adicionar um ciclo entre etapas, mova o ponteiro do mouse por cima da seta entre esses passos. 
   Escolha o **sinal** (**+**) que aparece, em seguida, selecione **adicionar uma ação**.

      ![Selecione "Adicionar uma ação"](media/logic-apps-control-flow-loops/add-for-each-loop.png)

   1. Na caixa de pesquisa, escolha **todos os**. Na caixa de pesquisa, escreva "for each" como o filtro. Na lista de ações, selecione a ação: **Para cada - controle**

      ![Adicionar ciclo "For each"](media/logic-apps-control-flow-loops/select-for-each.png)

3. Agora, crie o loop. Sob **selecionar uma saída dos passos anteriores** depois do **adicionar conteúdo dinâmico** é apresentada a lista, selecione o **ligações de Feed** matriz, o que é o resultado do acionador RSS. 

   ![Selecione na lista de conteúdo dinâmica](media/logic-apps-control-flow-loops/for-each-loop-dynamic-content-list.png)

   > [!NOTE] 
   > Pode selecionar *apenas* matriz gera a saída do passo anterior.

   A matriz de selecionado aparece agora aqui:

   ![Selecione a matriz](media/logic-apps-control-flow-loops/for-each-loop-select-array.png)

4. Para executar uma ação em cada item da matriz, arraste o **enviar um e-mail** ação para o loop. 

   A aplicação lógica pode ser algo semelhante a este exemplo:

   ![Adicione passos à loop "Foreach"](media/logic-apps-control-flow-loops/for-each-loop-with-step.png)

5. Guarde a aplicação lógica. Para testar manualmente a sua aplicação lógica, na barra de ferramentas da estruturador, escolha **executar**.

<a name="for-each-json"></a>

## <a name="foreach-loop-definition-json"></a>Definição de loop "Foreach" (JSON)

Se estiver a trabalhar na vista de código para a aplicação lógica, pode definir o `Foreach` um loop na definição de JSON da sua aplicação lógica em vez disso, por exemplo:

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

## <a name="foreach-loop-sequential"></a>Ciclo de "Foreach": Sequencial

Por predefinição, os ciclos num loop "Foreach" são executadas em paralelo. Para cada ciclo são executados sequencialmente, defina o loop **Sequential** opção. Loops "Foreach" tem de executar sequencialmente quando aninha ciclos ou variáveis em ciclos em que esperar resultados previsíveis. 

1. No canto direito superior do ciclo, escolha **reticências** (**...** ) > **Definições**.

   ![No loop "Foreach", escolha "..." > "Definições"](media/logic-apps-control-flow-loops/for-each-loop-settings.png)

1. Sob **controlo de simultaneidade**, ativar o **controlo de simultaneidade** definir como **no**. Mover o **grau de paralelismo** controlo de deslize **1**e escolha **feito**.

   ![Ativar o controlo de simultaneidade](media/logic-apps-control-flow-loops/for-each-loop-sequential-setting.png)

Se estiver a trabalhar com a definição de JSON da sua aplicação lógica, pode utilizar o `Sequential` opção ao adicionar o `operationOptions` parâmetro, por exemplo:

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

## <a name="until-loop"></a>"Até" loop
  
Para repetir ações até que uma condição é cumprida ou um estado é alterado, coloque essas ações num loop "Até". Seguem-se alguns cenários comuns em que pode usar um loop "Até":

* Chame um ponto final até obter a resposta pretendida.

* Crie um registo numa base de dados. Aguarde até que um campo específico em que o registo seja aprovado. Continue o processamento. 

Começando pelo 8:00, todos os dias, esta aplicação de lógica de exemplo incrementa uma variável até que o valor da variável é igual a 10. A aplicação lógica, em seguida, envia um e-mail que confirma o valor atual. 

> [!NOTE]
> Estes passos utilizam o Outlook do Office 365, mas pode utilizar qualquer fornecedor de e-mail que suporta o Logic Apps. 
> [Verifique a lista de conectores aqui](https://docs.microsoft.com/connectors/). Se utilizar outra conta de e-mail, os passos gerais são os mesmos, mas sua interface do Usuário poderá parecer ligeiramente diferente. 

1. Criar uma aplicação lógica em branco. No Estruturador da aplicação lógica, na caixa de pesquisa, escolha **todos os**. Procure "periodicidade". 
   Na lista de disparadores, selecione este acionador: **Periodicidade - agenda**

   ![Adicionar acionador "– de periodicidade"](./media/logic-apps-control-flow-loops/do-until-loop-add-trigger.png)

1. Especifique quando o acionador é acionado ao definir o intervalo, a frequência e a hora do dia. Para definir a hora, escolha **Mostrar opções avançadas**.

   ![Configurar a agenda de periodicidade](./media/logic-apps-control-flow-loops/do-until-loop-set-trigger-properties.png)

   | Propriedade | Value |
   | -------- | ----- |
   | **Intervalo** | 1 | 
   | **Frequência** | Dia |
   | **At these hours** (A estas horas) | 8 |
   ||| 

1. No acionador, escolha **novo passo**. 
   Procure "variables" e selecione a ação: **Inicializar variável - variáveis**

   ![Adicionar ação de "Initialize variável - variáveis"](./media/logic-apps-control-flow-loops/do-until-loop-add-variable.png)

1. Configure a sua variável com estes valores:

   ![Definir propriedades de variável](./media/logic-apps-control-flow-loops/do-until-loop-set-variable-properties.png)

   | Propriedade | Value | Descrição |
   | -------- | ----- | ----------- |
   | **Nome** | Limite | Nome da variável | 
   | **Tipo** | Número inteiro | Tipo de dados da variável | 
   | **Valor** | 0 | A variável do valor inicial | 
   |||| 

1. Sob o **Initialize variable** ação, escolha **novo passo**. 

1. Na caixa de pesquisa, escolha **todos os**. Procure "até" e selecione a ação: **Até - controlo**

   ![Adicionar "Até" loop](./media/logic-apps-control-flow-loops/do-until-loop-add-until-loop.png)

1. Criar a condição de saída do ciclo, selecionando o **limite** variável e o **equivale** operador. 
   Introduza **10** como o valor de comparação.

   ![Criar a condição de saída para interromper o loop](./media/logic-apps-control-flow-loops/do-until-loop-settings.png)

1. Dentro do loop, escolha **adicionar uma ação**. 

1. Na caixa de pesquisa, escolha **todos os**. Procure "variables" e selecione a ação: **Incrementar a variável - variáveis**

   ![Adicionar ação por incrementar a variável](./media/logic-apps-control-flow-loops/do-until-loop-increment-variable.png)

1. Para **Name**, selecione a **limite** variável. Para **valor**, introduza "1". 

     ![Incremento "Limite" em 1](./media/logic-apps-control-flow-loops/do-until-loop-increment-variable-settings.png)

1. Fora e sob o loop, escolher **novo passo**. 

1. Na caixa de pesquisa, escolha **todos os**. 
     Localize e adicione uma ação que envia um e-mail, por exemplo: 

     ![Adicionar ação que envia um e-mail](media/logic-apps-control-flow-loops/do-until-loop-send-email.png)

1. Se tal lhe for solicitado, inicie sessão na sua conta de e-mail.

1. Defina propriedades da ação de e-mail. Adicionar a **limite** variável para o assunto. Dessa forma, pode confirmar que valor atual da variável cumpre a condição especificada, por exemplo:

      ![Configurar propriedades de e-mail](./media/logic-apps-control-flow-loops/do-until-loop-send-email-settings.png)

      | Propriedade | Value | Descrição |
      | -------- | ----- | ----------- | 
      | **Para** | *< endereço de e-mail\@domínio >* | endereço de e-mail do destinatário. Para fins de teste, utilize o seu endereço de e-mail. | 
      | **Assunto** | O valor atual para "Limite de" é **limite** | Especifique o assunto do e-mail. Neste exemplo, certifique-se de que inclui a **limite** variável. | 
      | **Corpo** | <*email-content*> | Especifique o conteúdo da mensagem de e-mail que pretende enviar. Neste exemplo, introduza qualquer texto que desejar. | 
      |||| 

1. Guarde a aplicação lógica. Para testar manualmente a sua aplicação lógica, na barra de ferramentas da estruturador, escolha **executar**.

      Depois da lógica entrar em execução, receberá um e-mail com o conteúdo que especificou:

      ![E-mail recebido](./media/logic-apps-control-flow-loops/do-until-loop-sent-email.png)

## <a name="prevent-endless-loops"></a>Impedir que os loops intermináveis

Um loop "Até" tem limites predefinidos que pare a execução se alguma das seguintes condições:

| Propriedade | Valor predefinido | Descrição | 
| -------- | ------------- | ----------- | 
| **Contagem** | 60 | O maior número de ciclos executados antes do loop é encerrado. A predefinição é 60 ciclos. | 
| **Tempo limite** | PT1H | A maioria dos quantidade de tempo para executar um loop antes do loop é encerrado. A predefinição é de uma hora e é especificada no formato ISO 8601. <p>O valor de tempo limite é avaliado para cada ciclo de loop. Se qualquer ação no loop demora mais tempo do que o limite de tempo limite, não para o ciclo de atual. No entanto, o próximo ciclo não iniciar porque a condição de limite não for cumprida. | 
|||| 

Para alterar esses limites predefinidos, escolha **Mostrar opções avançadas** na forma da ação de loop.

<a name="until-json"></a>

## <a name="until-definition-json"></a>"Até" definição (JSON)

Se estiver a trabalhar na vista de código para a aplicação lógica, pode definir um `Until` um loop na definição de JSON da sua aplicação lógica em vez disso, por exemplo:

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

Neste exemplo, "Até" loop chama um ponto de final HTTP, que cria um recurso. O loop termina quando o corpo da resposta HTTP retorna com `Completed` estado. Para impedir que os loops intermináveis, o loop para também se alguma das seguintes condições:

* O loop foi executado 10 vezes conforme especificado pelo `count` atributo. A predefinição é 60 vezes. 

* O loop foi executado durante duas horas, conforme especificado pelo `timeout` atributo no formato ISO 8601. A predefinição é de uma hora.
  
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
* Para submeter ou votar em recursos e sugestões, [site de comentários de utilizadores do Azure Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Passos Seguintes

* [Execute os passos com base numa condição (instruções condicionais)](../logic-apps/logic-apps-control-flow-conditional-statement.md)
* [Execute os passos com base nos valores diferentes (declarações do comutador)](../logic-apps/logic-apps-control-flow-switch-statement.md)
* [Executar ou unir a passos paralelos (ramos)](../logic-apps/logic-apps-control-flow-branches.md)
* [Execute os passos com base no estado da ação agrupados (âmbitos)](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)
