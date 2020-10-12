---
title: Lidar com erros e exceções nos fluxos de trabalho
description: Saiba como lidar com erros e exceções que ocorrem em tarefas automatizadas e fluxos de trabalho criados através da utilização de Apps Azure Logic
services: logic-apps
ms.suite: integration
author: dereklee
ms.author: deli
ms.reviewer: klam, estfan, logicappspm
ms.date: 01/11/2020
ms.topic: article
ms.openlocfilehash: 73b116117530e5a2103b604efbf757d691006508
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "84704527"
---
# <a name="handle-errors-and-exceptions-in-azure-logic-apps"></a>Processar erros e exceções no Azure Logic Apps

A forma como qualquer arquitetura de integração lida adequadamente com o tempo de inatividade ou problemas causados por sistemas dependentes pode representar um desafio. Para ajudá-lo a criar integrações robustas e resilientes que lidam graciosamente com problemas e falhas, a Logic Apps proporciona uma experiência de primeira classe para lidar com erros e exceções.

<a name="retry-policies"></a>

## <a name="retry-policies"></a>Políticas de retíria

Para obter a exceção mais básica e o tratamento de erros, pode utilizar uma *política de repetição* em qualquer ação ou gatilho em que seja suportada, por exemplo, consulte [ações HTTP](../logic-apps/logic-apps-workflow-actions-triggers.md#http-trigger). Uma política de recandidamento especifica se e como a ação ou desencadeia uma tentativa quando o pedido original termina ou falha, que é qualquer pedido que resulte numa resposta 408, 429 ou 5xx. Se não for utilizada outra política de reação, a política por defeito é utilizada.

Aqui estão os tipos de política de relíndi:

| Tipo | Descrição |
|------|-------------|
| **Predefinição** | Esta política envia até quatro retrações em intervalos *exponencialmente crescentes,* que escalam 7,5 segundos, mas estão limitadas entre 5 e 45 segundos. |
| **Intervalo exponencial**  | Esta política aguarda um intervalo aleatório selecionado a partir de um intervalo exponencialmente crescente antes de enviar o próximo pedido. |
| **Intervalo fixo**  | Esta política aguarda o intervalo especificado antes de enviar o próximo pedido. |
| **Nenhuma**  | Não reensiem o pedido. |
|||

Para obter informações sobre os limites de política de relícuro, consulte [os limites e configuração das Aplicações Lógicas](../logic-apps/logic-apps-limits-and-config.md#request-limits).

### <a name="change-retry-policy"></a>Alterar a política de relemissão

Para selecionar uma política de repetição diferente, siga estes passos:

1. Abra a sua aplicação lógica no Logic App Designer.

1. Abra as **Definições** para uma ação ou gatilho.

1. Se a ação ou o gatilho suportar políticas de repetição, no âmbito **da Reformulação Política,** selecione o tipo que pretende.

Ou, pode especificar manualmente a política de repetição na `inputs` secção para uma ação ou gatilho que suporte políticas de repetição. Se não especificar uma política de repetição, a ação utiliza a política por defeito.

```json
"<action-name>": {
   "type": "<action-type>",
   "inputs": {
      "<action-specific-inputs>",
      "retryPolicy": {
         "type": "<retry-policy-type>",
         "interval": "<retry-interval>",
         "count": <retry-attempts>,
         "minimumInterval": "<minimum-interval>",
         "maximumInterval": "<maximum-interval>"
      },
      "<other-action-specific-inputs>"
   },
   "runAfter": {}
}
```

*Necessário*

| Valor | Tipo | Descrição |
|-------|------|-------------|
| <*retrip-política tipo*> | Cadeia | O tipo de política de regata que pretende utilizar: `default` `none` , , `fixed` ou `exponential` |
| <*retrip-intervalo*> | Cadeia | O intervalo de repetição em que o valor deve utilizar [o formato ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations). O intervalo mínimo predefinido é `PT5S` e o intervalo máximo é `PT1D` . Quando utilizar a política de intervalo exponencial, pode especificar valores mínimos e máximos diferentes. |
| <*recandiduções tentativas*> | Número inteiro | O número de tentativas de repetição, que devem ser entre 1 e 90 |
||||

*Opcional*

| Valor | Tipo | Descrição |
|-------|------|-------------|
| <*intervalo mínimo*> | Cadeia | Para a política de intervalo exponencial, o intervalo mais pequeno para o intervalo selecionado aleatoriamente no [formato ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations) |
| <*intervalo máximo*> | Cadeia | Para a política de intervalo exponencial, o maior intervalo para o intervalo selecionado aleatoriamente no [formato ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations) |
||||

Aqui está mais informações sobre os diferentes tipos de política.

<a name="default-retry"></a>

### <a name="default"></a>Predefinição

Se não especificar uma política de repetição, a ação utiliza a política de incumprimento, que é na verdade uma [política exponencial](#exponential-interval) de intervalos que envia até quatro retrações em intervalos exponencialmente crescentes que são dimensionado em 7,5 segundos. O intervalo é limitado entre 5 e 45 segundos.

Embora não explicitamente definida na sua ação ou gatilho, eis como a política por defeito se comporta num exemplo de ação HTTP:

```json
"HTTP": {
   "type": "Http",
   "inputs": {
      "method": "GET",
      "uri": "http://myAPIendpoint/api/action",
      "retryPolicy" : {
         "type": "exponential",
         "interval": "PT7S",
         "count": 4,
         "minimumInterval": "PT5S",
         "maximumInterval": "PT1H"
      }
   },
   "runAfter": {}
}
```

### <a name="none"></a>Nenhum

Para especificar que a ação ou o gatilho não recandidam os pedidos falhados, desaccione o <> *de política de retry para* `none` .

### <a name="fixed-interval"></a>Intervalo fixo

Para especificar que a ação ou o gatilho aguardam o intervalo especificado antes de enviar o próximo pedido, desaccione o <> *de nova política* para `fixed` .

*Exemplo*

Esta nova tentativa de tentar obter as últimas notícias mais duas vezes após o primeiro pedido falhado com um atraso de 30 segundos entre cada tentativa:

```json
"Get_latest_news": {
   "type": "Http",
   "inputs": {
      "method": "GET",
      "uri": "https://mynews.example.com/latest",
      "retryPolicy": {
         "type": "fixed",
         "interval": "PT30S",
         "count": 2
      }
   }
}
```

<a name="exponential-interval"></a>

### <a name="exponential-interval"></a>Intervalo exponencial

Para especificar que a ação ou o gatilho aguardam um intervalo aleatório antes de enviar o próximo pedido, desaccione o <> *de nova política* para `exponential` . O intervalo aleatório é selecionado a partir de uma gama de crescimento exponencial. Opcionalmente, também pode anular os intervalos mínimos e máximos predefinidos especificando os seus próprios intervalos mínimos e máximos.

**Gamas variáveis aleatórias**

Esta tabela mostra como as Aplicações Lógicas geram uma variável aleatória uniforme na gama especificada para cada tentativa até ao número de retrações:

| Número de retíria | Intervalo mínimo | Intervalo máximo |
|--------------|------------------|------------------|
| 1 | máximo (0, <*intervalo mínimo*>) | min (intervalo, <*intervalo máximo*>) |
| 2 | max (intervalo, <*intervalo mínimo*>) | min(intervalo 2 * <*intervalo máximo*>) |
| 3 | max (intervalo de 2 * <*intervalo mínimo*>) | min(intervalo 4 * <*intervalo máximo*>) |
| 4 | max (intervalo 4* <*intervalo mínimo*>) | min(intervalo 8 * <*intervalo máximo*>) |
| .... | .... | .... |
||||

<a name="control-run-after-behavior"></a>

## <a name="catch-and-handle-failures-by-changing-run-after-behavior"></a>Pegar e lidar com falhas alterando o comportamento de "correr atrás"

Quando adiciona ações no Logic App Designer, declara implicitamente a ordem a usar para executar essas ações. Depois de uma ação terminar a correr, essa ação é marcada com um estatuto `Succeeded` `Failed` como, `Skipped` ou `TimedOut` . Em cada definição de ação, o `runAfter` imóvel especifica a ação antecessora que deve terminar primeiro e os estatutos permitidos para esse antecessor antes que a ação do sucessor possa ser executada. Por predefinição, uma ação que adiciona no designer só funciona depois de o antecessor completar o `Succeeded` estado.

Quando uma ação lança um erro ou exceção não manipulado, a ação é marcada `Failed` , e qualquer ação sucessora é marcada `Skipped` . Se este comportamento acontecer para uma ação que tem ramificações paralelas, o motor De Aplicações Lógicas segue os outros ramos para determinar os seus estados de conclusão. Por exemplo, se uma sucursal termina com uma `Skipped` ação, o estado de conclusão desse ramo baseia-se no estatuto de antecessor da ação. Após a execução da aplicação lógica, o motor determina todo o estado da execução avaliando todos os estados do ramo. Se algum ramo terminar em falha, toda a aplicação lógica executada está marcada `Failed` .

![Exemplos que mostram como os estados de execução são avaliados](./media/logic-apps-exception-handling/status-evaluation-for-parallel-branches.png)

Para garantir que uma ação ainda pode ser executada apesar do estatuto do seu antecessor, [personalize o comportamento de uma ação "run after"](#customize-run-after) para lidar com os estatutos infrutíferos do antecessor.

<a name="customize-run-after"></a>

### <a name="customize-run-after-behavior"></a>Personalize o comportamento de "correr atrás"

Pode personalizar o comportamento de "correr atrás" de uma ação para que a ação seja executada quando o estado do antecessor é `Succeeded` , ou qualquer um `Failed` `Skipped` `TimedOut` destes estatutos. Por exemplo, para enviar um e-mail após a ação do antecessor do Excel Online `Add_a_row_into_a_table` ser marcado , em vez de alterar o comportamento de `Failed` `Succeeded` "correr atrás" seguindo qualquer passo:

* Na vista de design, selecione o botão elipses **(...**) e, em seguida, selecione **Configure run after**.

  ![Configurar comportamento "correr atrás" para uma ação](./media/logic-apps-exception-handling/configure-run-after-property-setting.png)

  A forma de ação mostra o estado padrão necessário para a ação do antecessor, que é **Adicionar uma linha a uma tabela** neste exemplo:

  ![Comportamento padrão "correr atrás" para uma ação](./media/logic-apps-exception-handling/change-run-after-property-status.png)

  Altere o comportamento "run after" para o estado que deseja, o que **falhou** neste exemplo:

  ![Alterar comportamento de "correr atrás" para "falhou"](./media/logic-apps-exception-handling/run-after-property-status-set-to-failed.png)

  Para especificar que a ação corre se a ação do antecessor está marcada como `Failed` , `Skipped` `TimedOut` ou, selecione os outros estatutos:

  ![Alterar o comportamento de "correr atrás" para ter qualquer outro estatuto](./media/logic-apps-exception-handling/run-after-property-multiple-statuses.png)

* Na visão de código, na definição JSON da ação, edite a `runAfter` propriedade, que segue esta sintaxe:

  ```json
  "<action-name>": {
     "inputs": {
        "<action-specific-inputs>"
     },
     "runAfter": {
        "<preceding-action>": [
           "Succeeded"
        ]
     },
     "type": "<action-type>"
  }
  ```

  Para este exemplo, mude a `runAfter` propriedade `Succeeded` `Failed` de:

  ```json
  "Send_an_email_(V2)": {
     "inputs": {
        "body": {
           "Body": "<p>Failed to&nbsp;add row to &nbsp;@{body('Add_a_row_into_a_table')?['Terms']}</p>",,
           "Subject": "Add row to table failed: @{body('Add_a_row_into_a_table')?['Terms']}",
           "To": "Sophia.Owen@fabrikam.com"
        },
        "host": {
           "connection": {
              "name": "@parameters('$connections')['office365']['connectionId']"
           }
        },
        "method": "post",
        "path": "/v2/Mail"
     },
     "runAfter": {
        "Add_a_row_into_a_table": [
           "Failed"
        ]
     },
     "type": "ApiConnection"
  }
  ```

  Para especificar que a ação corre se a ação do antecessor está marcada como `Failed` , ou , adicionar os `Skipped` `TimedOut` outros estatutos:

  ```json
  "runAfter": {
     "Add_a_row_into_a_table": [
        "Failed", "Skipped", "TimedOut"
     ]
  },
  ```

<a name="scopes"></a>

## <a name="evaluate-actions-with-scopes-and-their-results"></a>Avaliar ações com âmbitos e seus resultados

Semelhante a passos de execução após ações individuais com a `runAfter` propriedade, você pode agrupar ações juntos dentro de um [âmbito](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md). Pode utilizar os âmbitos quando quiser agrupar logicamente as ações em conjunto, avaliar o estado agregado do âmbito e executar ações com base nesse estatuto. Depois de todas as ações num âmbito final, o próprio âmbito obtém o seu próprio estatuto.

Para verificar o estado de um âmbito, pode utilizar os mesmos critérios que utiliza para verificar o estado de funcionação de uma aplicação lógica, `Succeeded` `Failed` como, por exemplo, e assim por diante.

Por padrão, quando todas as ações do âmbito forem bem sucedidas, o estado do âmbito está marcado `Succeeded` . Se a ação final num âmbito resultar `Failed` como ou , o estado do âmbito está marcado `Aborted` `Failed` .

Para obter exceções num `Failed` âmbito e executar ações que lidam com esses erros, você pode usar a `runAfter` propriedade para esse `Failed` âmbito. Assim, se *alguma* ação no âmbito falhar, e usar a `runAfter` propriedade para esse âmbito, pode criar uma única ação para apanhar falhas.

Para limites de âmbitos, consulte [Limites e config](../logic-apps/logic-apps-limits-and-config.md).

<a name="get-results-from-failures"></a>

### <a name="get-context-and-results-for-failures"></a>Obtenha contexto e resultados para falhas

Embora as falhas de captura de um âmbito sejam úteis, também pode querer que o contexto o ajude a entender exatamente quais as ações falhadas, além de quaisquer erros ou códigos de estado que foram devolvidos.

A [`result()`](../logic-apps/workflow-definition-language-functions-reference.md#result) função proporciona contexto sobre os resultados de todas as ações num âmbito. A `result()` função aceita um único parâmetro, que é o nome do âmbito, e devolve uma matriz que contém todos os resultados de ação dentro desse âmbito. Estes objetos de ação incluem os mesmos atributos que o `actions()` objeto, tais como a hora de início da ação, tempo final, estado, entradas, IDs de correlação e saídas. Para enviar contexto para quaisquer ações que falharam dentro de um âmbito, você pode facilmente emparelhar uma `@result()` expressão com a `runAfter` propriedade.

Para executar uma ação para cada ação num âmbito que tenha um `Failed` resultado, e filtrar a matriz de resultados até às ações falhadas, pode emparelhar uma `@result()` expressão com uma ação Filter [**Array**](logic-apps-perform-data-operations.md#filter-array-action) e um [**For cada**](../logic-apps/logic-apps-control-flow-loops.md) loop. Pode pegar na matriz de resultados filtrada e efetuar uma ação para cada falha utilizando o `For_each` laço.

Aqui está um exemplo, seguido de uma explicação detalhada, que envia um pedido HTTP POST com o organismo de resposta para quaisquer ações que falharam no âmbito "My_Scope":

```json
"Filter_array": {
   "type": "Query",
   "inputs": {
      "from": "@result('My_Scope')",
      "where": "@equals(item()['status'], 'Failed')"
   },
   "runAfter": {
      "My_Scope": [
         "Failed"
      ]
    }
},
"For_each": {
   "type": "foreach",
   "actions": {
      "Log_exception": {
         "type": "Http",
         "inputs": {
            "method": "POST",
            "body": "@item()['outputs']['body']",
            "headers": {
               "x-failed-action-name": "@item()['name']",
               "x-failed-tracking-id": "@item()['clientTrackingId']"
            },
            "uri": "http://requestb.in/"
         },
         "runAfter": {}
      }
   },
   "foreach": "@body('Filter_array')",
   "runAfter": {
      "Filter_array": [
         "Succeeded"
      ]
   }
}
```

Aqui está uma passagem detalhada que descreve o que acontece neste exemplo:

1. Para obter o resultado de todas as ações dentro de "My_Scope", a ação **Filter Array** utiliza esta expressão de filtro: `@result('My_Scope')`

1. A condição de **Filter Array** é qualquer item que tenha um estado igual `@result()` a `Failed` . Esta condição filtra a matriz que tem todos os resultados de ação de "My_Scope" até um conjunto com apenas os resultados de ação falhados.

1. Efetue uma `For_each` ação de loop nas saídas filtradas da *matriz.* Este passo executa uma ação para cada resultado de ação falhado que foi previamente filtrado.

   Se uma única ação no âmbito falhar, as ações em `For_each` loop só funcionam uma vez. Múltiplas ações falhadas causam uma ação por falha.

1. Envie um HTTP POST no corpo de resposta do `For_each` item, que é a `@item()['outputs']['body']` expressão.

   A `@result()` forma do item é a mesma que a forma e pode ser analisada da mesma `@actions()` forma.

1. Inclua dois cabeçalhos personalizados com o nome de ação falhado ( `@item()['name']` ) e o ID de rastreio do cliente de execução falhada ( `@item()['clientTrackingId']` ).

Para referência, aqui está um exemplo de um único `@result()` item, mostrando o `name` , e propriedades que são `body` `clientTrackingId` analisados no exemplo anterior. Fora de uma `For_each` ação, `@result()` devolve uma série destes objetos.

```json
{
   "name": "Example_Action_That_Failed",
   "inputs": {
      "uri": "https://myfailedaction.azurewebsites.net",
      "method": "POST"
   },
   "outputs": {
      "statusCode": 404,
      "headers": {
         "Date": "Thu, 11 Aug 2016 03:18:18 GMT",
         "Server": "Microsoft-IIS/8.0",
         "X-Powered-By": "ASP.NET",
         "Content-Length": "68",
         "Content-Type": "application/json"
      },
      "body": {
         "code": "ResourceNotFound",
         "message": "/docs/folder-name/resource-name does not exist"
      }
   },
   "startTime": "2016-08-11T03:18:19.7755341Z",
   "endTime": "2016-08-11T03:18:20.2598835Z",
   "trackingId": "bdd82e28-ba2c-4160-a700-e3a8f1a38e22",
   "clientTrackingId": "08587307213861835591296330354",
   "code": "NotFound",
   "status": "Failed"
}
```

Para executar diferentes padrões de tratamento de exceções, pode utilizar as expressões previamente descritas neste artigo. Pode optar por executar uma única ação de tratamento de exceção fora do âmbito de aplicação que aceite toda a série filtrada de falhas e remova a `For_each` ação. Também pode incluir outras propriedades úteis da `\@result()` resposta, como descrito anteriormente.

## <a name="set-up-azure-monitor-logs"></a>Configurar registos do Monitor Azure

Os padrões anteriores são uma ótima maneira de lidar com erros e exceções dentro de uma corrida, mas também pode identificar e responder a erros independentes da própria corrida. [O Azure Monitor](../azure-monitor/overview.md) fornece uma forma simples de enviar todos os eventos de fluxo de trabalho, incluindo todos os status de execução e ação, para um [espaço de trabalho Log Analytics,](../azure-monitor/platform/data-platform-logs.md) [conta de armazenamento Azure,](../storage/blobs/storage-blobs-overview.md)ou [Azure Event Hubs](../event-hubs/event-hubs-about.md).

Para avaliar os estados de execução, pode monitorizar os registos e métricas ou publicá-los em qualquer ferramenta de monitorização que prefira. Uma opção potencial é transmitir todos os eventos através de Event Hubs para [a Azure Stream Analytics.](https://azure.microsoft.com/services/stream-analytics/) No Stream Analytics, pode escrever consultas ao vivo com base em quaisquer anomalias, médias ou falhas dos registos de diagnóstico. Pode utilizar o Stream Analytics para enviar informações para outras fontes de dados, tais como filas, tópicos, SQL, Azure Cosmos DB ou Power BI.

## <a name="next-steps"></a>Passos seguintes

* [Veja como um cliente constrói o tratamento de erros com apps Azure Logic](../logic-apps/logic-apps-scenario-error-and-exception-handling.md)
* [Encontre mais exemplos e cenários de Apps Lógicas](../logic-apps/logic-apps-examples-and-scenarios.md)
