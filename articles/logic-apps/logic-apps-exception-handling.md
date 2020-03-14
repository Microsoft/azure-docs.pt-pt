---
title: Lidar com erros e exceções nos fluxos de trabalho
description: Saiba como lidar com erros e exceções que ocorrem em tarefas automatizadas e fluxos de trabalho criados através de Aplicações Lógicas Azure
services: logic-apps
ms.suite: integration
author: dereklee
ms.author: deli
ms.reviewer: klam, estfan, logicappspm
ms.date: 01/11/2020
ms.topic: article
ms.openlocfilehash: 73b116117530e5a2103b604efbf757d691006508
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79284034"
---
# <a name="handle-errors-and-exceptions-in-azure-logic-apps"></a>Lidar com erros e exceções em Aplicações Lógicas Azure

A forma como qualquer arquitetura de integração lida adequadamente com o tempo de inatividade ou problemas causados por sistemas dependentes pode representar um desafio. Para ajudá-lo a criar integrações robustas e resilientes que lidam graciosamente com problemas e falhas, a Logic Apps proporciona uma experiência de primeira classe para lidar com erros e exceções.

<a name="retry-policies"></a>

## <a name="retry-policies"></a>Políticas de retry

Para a exceção mais básica e manipulação de erros, pode utilizar uma política de *retry* em qualquer ação ou gatilho onde suporte, por exemplo, ver [ação HTTP](../logic-apps/logic-apps-workflow-actions-triggers.md#http-trigger). Uma política de retry especifica se e como a ação ou o gatilho retenta um pedido quando o pedido original é para fora ou falha, o que é qualquer pedido que resulte numa resposta 408, 429 ou 5xx. Se não for utilizada outra política de retry, a política de incumprimento é utilizada.

Aqui estão os tipos de política de retry:

| Tipo | Descrição |
|------|-------------|
| **Predefinição** | Esta política envia até quatro repetições em intervalos *exponencialmente crescentes,* que escalam em 7,5 segundos, mas estão limitadas entre 5 e 45 segundos. |
| **Intervalo exponencial**  | Esta política aguarda um intervalo aleatório selecionado a partir de uma gama exponencialmente crescente antes de enviar o próximo pedido. |
| **Intervalo fixo**  | Esta política aguarda o intervalo especificado antes de enviar o próximo pedido. |
| **Nenhuma.**  | Não reenvie o pedido. |
|||

Para obter informações sobre os limites da política de retry, consulte [os limites e configuração das Aplicações Lógicas.](../logic-apps/logic-apps-limits-and-config.md#request-limits)

### <a name="change-retry-policy"></a>Mudar a política de retry

Para selecionar uma política de retry diferente, siga estes passos:

1. Abra a sua aplicação lógica no Logic App Designer.

1. Abra as **Definições** para uma ação ou gatilho.

1. Se a ação ou o gatilho apoiarem as políticas de retry, no âmbito da **Política de Retry,** selecione o tipo que pretende.

Ou, pode especificar manualmente a política de retry na secção `inputs` para uma ação ou gatilho que apoie políticas de retry. Se não especificar uma política de retry, a ação utiliza a política de incumprimento.

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
| < *> de política de retry-tipo* | String | O tipo de política de retry que pretende utilizar: `default`, `none`, `fixed`ou `exponential` |
| < *> de intervalo de repetição* | String | O intervalo de repetição em que o valor deve utilizar o [formato ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations). O intervalo mínimo por defeito é `PT5S` e o intervalo máximo é `PT1D`. Quando utilizar a política de intervalo seletor exponencial, pode especificar diferentes valores mínimos e máximos. |
| <*tentativas de retry->* | Número inteiro | O número de tentativas de retry, que deve ser entre 1 e 90 |
||||

*Opcional*

| Valor | Tipo | Descrição |
|-------|------|-------------|
| <> *de intervalo mínimo* | String | Para a política de intervalo exponencial, o menor intervalo para o intervalo selecionado aleatoriamente no [formato ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations) |
| <> *de intervalo máximo* | String | Para a política de intervalo exponencial, o maior intervalo para o intervalo selecionado aleatoriamente no [formato ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations) |
||||

Aqui está mais informações sobre os diferentes tipos de políticas.

<a name="default-retry"></a>

### <a name="default"></a>Predefinição

Se não especificar uma política de repetição, a ação utiliza a política de incumprimento, que na verdade é uma [política de intervalo exponencial](#exponential-interval) que envia até quatro repetições em intervalos exponencialmente crescentes que são escalados em 7,5 segundos. O intervalo está limitado entre 5 e 45 segundos.

Embora não explicitamente definida na sua ação ou gatilho, eis como a política padrão se comporta num exemplo de ação HTTP:

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

Para especificar que a ação ou o gatilho não retry pedidos falhados, detete o <*retry-policy-type*> para `none`.

### <a name="fixed-interval"></a>Intervalo fixo

Para especificar que a ação ou o gatilho aguardam o intervalo especificado antes de enviar o próximo pedido, detetete o tipo de política de*repetição*> para `fixed`.

*Exemplo*

Esta política de retry tenta obter as últimas notícias mais duas vezes após o primeiro pedido falhado com um atraso de 30 segundos entre cada tentativa:

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

Para especificar que a ação ou o gatilho aguarda um intervalo aleatório antes de enviar o próximo pedido, detete o tipo de política de*repetição*> para `exponential`. O intervalo aleatório é selecionado a partir de uma gama exponencialmente crescente. Opcionalmente, também pode anular os intervalos mínimos e máximos predefinidos especificando os seus próprios intervalos mínimos e máximos.

**Gamas variáveis aleatórias**

Esta tabela mostra como as Aplicações Lógicas geram uma variável aleatória uniforme na gama especificada para cada retry até e incluindo o número de repetições:

| Número de retry | Intervalo mínimo | Intervalo máximo |
|--------------|------------------|------------------|
| 1 | máx(0, <*intervalo mínimo*>) | min (intervalo, <*intervalo máximo*>) |
| 2 | máx (intervalo, <*intervalo mínimo*>) | min(2 * intervalo, <*intervalo máximo*>) |
| 3 | máx(2 * intervalo, <*intervalo mínimo*>) | min(intervalo 4* e <*intervalo máximo*>) |
| 4 | máx(4 * intervalo, <*intervalo mínimo*>) | min(8 * intervalo, <*intervalo máximo*>) |
| .... | .... | .... |
||||

<a name="control-run-after-behavior"></a>

## <a name="catch-and-handle-failures-by-changing-run-after-behavior"></a>Catch and handle falhas alterando o comportamento "correr atrás"

Ao adicionar ações no Logic App Designer, declara implicitamente a ordem a utilizar para executar essas ações. Depois de uma ação terminar a decorrer, essa ação é marcada com um estatuto como `Succeeded`, `Failed`, `Skipped`ou `TimedOut`. Em cada definição de ação, o `runAfter` propriedade especifica a ação antecessora que deve terminar primeiro e os estatutos permitidos para esse antecessor antes que a ação sucessora possa ser executada. Por padrão, uma ação que adiciona no designer só funciona depois de o antecessor completar com `Succeeded` estatuto.

Quando uma ação lança um erro ou uma exceção não manuseados, a ação é marcada `Failed`, e qualquer ação sucessora é marcada `Skipped`. Se este comportamento acontecer para uma ação que tem ramos paralelos, o motor Logic Apps segue os outros ramos para determinar os seus estados de conclusão. Por exemplo, se um ramo terminar com uma ação `Skipped`, o estado de conclusão dessa sucursal baseia-se no estatuto de antecessor da ação. Após o execução da aplicação lógica, o motor determina todo o estado da execução avaliando todos os estados da sucursal. Se algum ramo terminar em falha, toda a execução da aplicação lógica está marcada `Failed`.

![Exemplos que mostram como os estados de execução são avaliados](./media/logic-apps-exception-handling/status-evaluation-for-parallel-branches.png)

Para garantir que uma ação ainda pode decorrer apesar do estatuto do seu antecessor, [personalize o comportamento "correr atrás" de uma ação](#customize-run-after) para lidar com os estatutos mal sucedidos do antecessor.

<a name="customize-run-after"></a>

### <a name="customize-run-after-behavior"></a>Personalize o comportamento "correr depois"

Pode personalizar o comportamento "correr atrás" de uma ação para que a ação decorra quando o estatuto do antecessor for `Succeeded`, `Failed`, `Skipped`, `TimedOut`, ou qualquer um destes estatutos. Por exemplo, enviar um e-mail após a ação do antecessor `Add_a_row_into_a_table` Excel Online é marcada `Failed`, em vez de `Succeeded`, alterar o comportamento "correr atrás" seguindo qualquer passo:

* Na vista de design, selecione o botão elipses ( **...** ) e, em seguida, selecione **Configurar executar depois**de .

  ![Configure o comportamento "correr atrás" para uma ação](./media/logic-apps-exception-handling/configure-run-after-property-setting.png)

  A forma de ação mostra o estado padrão necessário para a ação antecessora, que é **adicionar uma linha a uma tabela** neste exemplo:

  ![Comportamento padrão "correr atrás" para uma ação](./media/logic-apps-exception-handling/change-run-after-property-status.png)

  Mude o comportamento "correr atrás" para o estado que deseja, o que **falhou** neste exemplo:

  ![Mudar comportamento "correr atrás" para "falhou"](./media/logic-apps-exception-handling/run-after-property-status-set-to-failed.png)

  Para especificar que a ação decorre se a ação antecessora está marcada como `Failed`, `Skipped` ou `TimedOut`, selecione os outros estatutos:

  ![Alterar comportamento "correr atrás" para ter qualquer outro estatuto](./media/logic-apps-exception-handling/run-after-property-multiple-statuses.png)

* Em vista de código, na definição JSON da ação, edite o `runAfter` propriedade, que segue esta sintaxe:

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

  Para este exemplo, mude o `runAfter` imóvel de `Succeeded` para `Failed`:

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

  Para especificar que a ação decorre se a ação antecessora está marcada como `Failed`, `Skipped` ou `TimedOut`, adicione os outros estatutos:

  ```json
  "runAfter": {
     "Add_a_row_into_a_table": [
        "Failed", "Skipped", "TimedOut"
     ]
  },
  ```

<a name="scopes"></a>

## <a name="evaluate-actions-with-scopes-and-their-results"></a>Avaliar ações com âmbitos e resultados

Semelhante a passos de execução após ações individuais com a propriedade `runAfter`, você pode agrupar ações dentro de um [âmbito](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md). Pode utilizar os âmbitos quando quiser, logicamente, agrupar as ações, avaliar o estatuto agregado do âmbito e realizar ações com base nesse estado. Depois de todas as ações num final de âmbito, o âmbito em si obtém o seu próprio estatuto.

Para verificar o estado de um âmbito, pode utilizar os mesmos critérios que utiliza para verificar o estado de execução de uma aplicação lógica, como `Succeeded`, `Failed`, e assim por diante.

Por predefinição, quando todas as ações do âmbito forem bem sucedidas, o estatuto do âmbito é marcado `Succeeded`. Se a ação final num âmbito de aplicação resultar como `Failed` ou `Aborted`, o estatuto do âmbito de aplicação é marcado `Failed`.

Para capturar exceções num âmbito `Failed` e executar ações que lidam com esses erros, pode utilizar a propriedade `runAfter` para esse âmbito `Failed`. Dessa forma, se *alguma* ação no âmbito falhar, e usar a propriedade `runAfter` para esse âmbito, pode criar uma única ação para apanhar falhas.

Para limites de alcances, consulte [Limites e config](../logic-apps/logic-apps-limits-and-config.md).

<a name="get-results-from-failures"></a>

### <a name="get-context-and-results-for-failures"></a>Obter contexto e resultados para falhas

Embora capturar falhas de um âmbito seja útil, você também pode querer contexto para ajudá-lo a entender exatamente quais as ações falhadas mais quaisos erros ou códigos de estado que foram devolvidos.

A função [`result()`](../logic-apps/workflow-definition-language-functions-reference.md#result) fornece contexto sobre os resultados de todas as ações de âmbito. A função `result()` aceita um único parâmetro, que é o nome do âmbito, e devolve uma matriz que contém todos os resultados de ação a partir desse âmbito. Estes objetos de ação incluem os mesmos atributos que o `actions()` objeto, tais como o tempo de início da ação, o tempo final, o estado, as inputs, as iDs de correlação e as saídas. Para enviar contexto para quaisquer ações que falhassem dentro de um âmbito, você pode facilmente emparelhar uma expressão `@result()` com a propriedade `runAfter`.

Para executar uma ação para cada ação num âmbito que tenha um resultado `Failed`, e filtrar a matriz de resultados até às ações falhadas, pode emparelhar uma expressão `@result()` com uma ação [**Filter Array**](logic-apps-perform-data-operations.md#filter-array-action) e um [**Para cada**](../logic-apps/logic-apps-control-flow-loops.md) loop. Pode pegar na matriz filtrada do resultado e realizar uma ação para cada falha utilizando o laço `For_each`.

Aqui está um exemplo, seguido de uma explicação detalhada, que envia um pedido http post com o órgão de resposta para quaisquer ações que falharam dentro do âmbito "My_Scope":

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

Aqui está um passe detalhado que descreve o que acontece neste exemplo:

1. Para obter o resultado de todas as ações dentro de "My_Scope", a ação **Filter Array** utiliza esta expressão de filtro: `@result('My_Scope')`

1. A condição para **o Filter Array** é qualquer item `@result()` que tenha um estatuto igual a `Failed`. Esta condição filtra a matriz que tem todos os resultados de ação de "My_Scope" até uma matriz com apenas os resultados de ação falhados.

1. Execute uma ação de loop `For_each` nas saídas filtradas da *matriz.* Este passo executa uma ação para cada resultado de ação falhado que foi previamente filtrado.

   Se uma única ação no âmbito falhar, as ações no ciclo `For_each` só funcionam uma vez. Múltiplas ações falhadas causam uma ação por falha.

1. Envie um POST HTTP sobre o organismo de resposta do artigo `For_each`, que é a expressão `@item()['outputs']['body']`.

   A forma do item `@result()` é a mesma que a forma `@actions()` e pode ser analisada da mesma forma.

1. Inclua dois cabeçalhos personalizados com o nome de ação falhado (`@item()['name']`) e o ID de rastreio do cliente falhado (`@item()['clientTrackingId']`).

Para referência, aqui está um exemplo de um único item `@result()`, mostrando as propriedades `name`, `body`e `clientTrackingId` que são analisadas no exemplo anterior. Fora de uma ação `For_each`, `@result()` devolve uma série destes objetos.

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

Para executar diferentes padrões de manipulação de exceções, pode utilizar as expressões anteriormente descritas neste artigo. Pode optar por executar uma única ação de manipulação de exceção fora do âmbito que aceita toda a gama filtrada de falhas e remover a ação `For_each`. Também pode incluir outras propriedades úteis a partir da resposta `\@result()`, conforme descrito anteriormente.

## <a name="set-up-azure-monitor-logs"></a>Configurar registos do Monitor Azure

Os padrões anteriores são uma ótima maneira de lidar com erros e exceções dentro de uma corrida, mas também pode identificar e responder a erros independentes da própria execução. [O Azure Monitor](../azure-monitor/overview.md) fornece uma forma simples de enviar todos os eventos de fluxo de trabalho, incluindo todos os estados de execução e ação, para um espaço de [trabalho log Analytics,](../azure-monitor/platform/data-platform-logs.md)conta de armazenamento [Azure,](../storage/blobs/storage-blobs-overview.md)ou Hubs de [Eventos Azure.](../event-hubs/event-hubs-about.md)

Para avaliar os estados de execução, pode monitorizar os registos e métricas ou publicá-los em qualquer ferramenta de monitorização que prefira. Uma opção potencial é transmitir todos os eventos através de Hubs de Eventos para [O Analytics De Fluxo.](https://azure.microsoft.com/services/stream-analytics/) No Stream Analytics, pode escrever consultas ao vivo com base em quaisquer anomalias, médias ou falhas dos registos de diagnóstico. Pode utilizar o Stream Analytics para enviar informações para outras fontes de dados, tais como filas, tópicos, SQL, Azure Cosmos DB ou Power BI.

## <a name="next-steps"></a>Passos seguintes

* [Veja como um cliente constrói manipulação de erros com aplicações da Azure Logic](../logic-apps/logic-apps-scenario-error-and-exception-handling.md)
* [Encontre mais exemplos e cenários de Apps Lógicas](../logic-apps/logic-apps-examples-and-scenarios.md)
