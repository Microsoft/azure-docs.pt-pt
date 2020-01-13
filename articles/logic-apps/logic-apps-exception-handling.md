---
title: Tratar erros e exceções em fluxos de trabalho
description: Saiba como lidar com erros e exceções que ocorrem em tarefas automatizadas e fluxos de trabalho criados com o uso de aplicativos lógicos do Azure
services: logic-apps
ms.suite: integration
author: dereklee
ms.author: deli
ms.reviewer: klam, estfan, logicappspm
ms.date: 01/11/2020
ms.topic: article
ms.openlocfilehash: a5cfb79626370ab9f8493038ac1583993a154b59
ms.sourcegitcommit: f9601bbccddfccddb6f577d6febf7b2b12988911
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/12/2020
ms.locfileid: "75912051"
---
# <a name="handle-errors-and-exceptions-in-azure-logic-apps"></a>Tratar erros e exceções nos aplicativos lógicos do Azure

A maneira como qualquer arquitetura de integração controla adequadamente o tempo de inatividade ou os problemas causados por sistemas dependentes pode representar um desafio. Para ajudá-lo a criar integrações robustas e resilientes que lidam normalmente com problemas e falhas, os aplicativos lógicos fornecem uma experiência de primeira classe para lidar com erros e exceções.

<a name="retry-policies"></a>

## <a name="retry-policies"></a>Repetir políticas

Para obter a exceção mais básica e o tratamento de erros, você pode usar uma *política de repetição* em qualquer ação ou gatilho quando houver suporte, por exemplo, consulte [ação http](../logic-apps/logic-apps-workflow-actions-triggers.md#http-trigger). Uma política de repetição especifica se e como a ação ou o gatilho tenta novamente uma solicitação quando a solicitação original atinge o tempo limite ou falha, o que é qualquer solicitação que resulte em uma resposta 408, 429 ou 5xx. Se nenhuma outra política de repetição for usada, a política padrão será usada.

Estes são os tipos de política de repetição:

| Tipo | Descrição |
|------|-------------|
| **Predefinição** | Essa política envia até quatro repetições em intervalos *exponencialmente crescentes* , que são dimensionados por 7,5 segundos, mas são limitados entre 5 e 45 segundos. |
| **Intervalo exponencial**  | Essa política aguarda um intervalo aleatório selecionado de um intervalo exponencialmente crescente antes de enviar a próxima solicitação. |
| **Intervalo fixo**  | Essa política aguarda o intervalo especificado antes de enviar a próxima solicitação. |
| **Nenhum**  | Não reenvie a solicitação. |
|||

Para obter informações sobre limites de política de repetição, consulte [limites e configuração de aplicativos lógicos](../logic-apps/logic-apps-limits-and-config.md#request-limits).

### <a name="change-retry-policy"></a>Alterar política de repetição

Para selecionar uma política de repetição diferente, siga estas etapas:

1. Abra seu aplicativo lógico no designer de aplicativo lógico.

1. Abra as **configurações** de uma ação ou gatilho.

1. Se a ação ou o gatilho der suporte a políticas de repetição, em **política de repetição**, selecione o tipo desejado.

Ou, você pode especificar manualmente a política de repetição na seção `inputs` para uma ação ou um gatilho que ofereça suporte a políticas de repetição. Se você não especificar uma política de repetição, a ação usará a política padrão.

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
| <*retry-policy-type*> | String | O tipo de política de repetição que você deseja usar: `default`, `none`, `fixed`ou `exponential` |
| <*retry-interval*> | String | O intervalo de repetição em que o valor deve usar o [formato ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations). O intervalo mínimo padrão é `PT5S` e o intervalo máximo é `PT1D`. Ao usar a política de intervalo exponencial, você pode especificar valores mínimo e máximo diferentes. |
| <*retry-attempts*> | Número inteiro | O número de tentativas de repetição, que devem estar entre 1 e 90 |
||||

*Opcional*

| Valor | Tipo | Descrição |
|-------|------|-------------|
| <*de intervalo mínimo*> | String | Para a política de intervalo exponencial, o menor intervalo para o intervalo selecionado aleatoriamente no [formato ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations) |
| <*de intervalo máximo*> | String | Para a política de intervalo exponencial, o maior intervalo para o intervalo selecionado aleatoriamente no [formato ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations) |
||||

Aqui estão mais informações sobre os diferentes tipos de política.

<a name="default-retry"></a>

### <a name="default"></a>Predefinição

Se você não especificar uma política de repetição, a ação usará a política padrão, que é, na verdade, uma [política de intervalo exponencial](#exponential-interval) que envia até quatro repetições em intervalos de aumento exponencial que são dimensionados por 7,5 segundos. O intervalo é limitado entre 5 e 45 segundos.

Embora não seja definido explicitamente em sua ação ou gatilho, aqui está como a política padrão se comporta em uma ação HTTP de exemplo:

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

### <a name="none"></a>Nenhuma

Para especificar que a ação ou o gatilho não tente novamente as solicitações com falha, defina o < > de*tipo de política de repetição*como `none`.

### <a name="fixed-interval"></a>Intervalo fixo

Para especificar que a ação ou o gatilho aguarde o intervalo especificado antes de enviar a próxima solicitação, defina o < > de*tentativa-Policy-Type*como `fixed`.

*Exemplo*

Essa política de repetição tenta obter as últimas notícias mais duas vezes após a primeira solicitação com falha com um atraso de 30 segundos entre cada tentativa:

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

Para especificar que a ação ou o gatilho aguarda um intervalo aleatório antes de enviar a próxima solicitação, defina o < > de*tentativa-Policy-Type*como `exponential`. O intervalo aleatório é selecionado de um intervalo de aumento exponencial. Opcionalmente, você também pode substituir os intervalos mínimo e máximo padrão especificando seus próprios intervalos mínimo e máximo.

**Intervalos de variáveis aleatórias**

Esta tabela mostra como os aplicativos lógicos geram uma variável aleatória uniforme no intervalo especificado para cada repetição até e incluindo o número de repetições:

| Número de repetições | Intervalo mínimo | Intervalo máximo |
|--------------|------------------|------------------|
| 1 | Max (0, <*intervalo mínimo*>) | min (intervalo, <*intervalo máximo*>) |
| 2 | Max (intervalo, <*intervalo mínimo*>) | mín. (2 * intervalo, <*intervalo máximo*>) |
| 3 | Max (2 * Interval, <*intervalo mínimo*>) | mín. (4 * intervalo, <*intervalo máximo*>) |
| 4 | Max (4 * intervalo, <*intervalo mínimo*>) | min (8 * intervalo, <*intervalo máximo*>) |
| .... | .... | .... |
||||

<a name="control-run-after-behavior"></a>

## <a name="catch-and-handle-failures-by-changing-run-after-behavior"></a>Detectar e lidar com falhas alterando o comportamento de "executar após"

Ao adicionar ações no designer do aplicativo lógico, você declara implicitamente o pedido a ser usado para executar essas ações. Após a conclusão da execução de uma ação, essa ação é marcada com um status como `Succeeded`, `Failed`, `Skipped`ou `TimedOut`. Em cada definição de ação, a propriedade `runAfter` especifica a ação predecessora que deve primeiro ser concluída e os status permitidos para essa predecessora antes que a ação da sucessora possa ser executada. Por padrão, uma ação que você adiciona no designer é executada somente após a conclusão da predecessora com `Succeeded` status.

Quando uma ação gera um erro ou exceção sem tratamento, a ação é marcada `Failed`e qualquer ação de sucessor é marcada `Skipped`. Se esse comportamento ocorrer para uma ação que tenha ramificações paralelas, o mecanismo de aplicativos lógicos seguirá as outras ramificações para determinar seus status de conclusão. Por exemplo, se uma ramificação terminar com uma `Skipped` ação, o status de conclusão da ramificação será baseado no status predecessor da ação ignorada. Após a conclusão da execução do aplicativo lógico, o mecanismo determina o status da execução inteira avaliando todos os status da ramificação. Se qualquer ramificação for encerrada em falha, toda a execução do aplicativo lógico será marcada `Failed`.

![Exemplos que mostram como os status de execução são avaliados](./media/logic-apps-exception-handling/status-evaluation-for-parallel-branches.png)

Para garantir que uma ação ainda possa ser executada apesar do status da sua predecessora, [Personalize o comportamento de "executar após" de uma ação](#customize-run-after) para lidar com os status malsucedidos da predecessora.

<a name="customize-run-after"></a>

### <a name="customize-run-after-behavior"></a>Personalizar o comportamento "executar após"

Você pode personalizar o comportamento "executar após" de uma ação para que a ação seja executada quando o status da predecessora for `Succeeded`, `Failed`, `Skipped`, `TimedOut`ou qualquer um desses status. Por exemplo, para enviar um email após a ação `Add_a_row_into_a_table` predecessora do Excel online é marcada `Failed`, em vez de `Succeeded`, altere o comportamento "executar após" seguindo qualquer uma das etapas:

* Na exibição Design, selecione o botão de reticências ( **...** ) e, em seguida, selecione **configurar execução após**.

  ![Configurar o comportamento "executar após" para uma ação](./media/logic-apps-exception-handling/configure-run-after-property-setting.png)

  A forma de ação mostra o status padrão necessário para a ação predecessora, que é **Adicionar uma linha a uma tabela** neste exemplo:

  ![Comportamento padrão "executar após" para uma ação](./media/logic-apps-exception-handling/change-run-after-property-status.png)

  Altere o comportamento de "executar após" para o status desejado, que **tem falha** neste exemplo:

  ![Alterar o comportamento "executar após" para "falha"](./media/logic-apps-exception-handling/run-after-property-status-set-to-failed.png)

  Para especificar que a ação seja executada se a ação predecessora estiver marcada como `Failed`, `Skipped` ou `TimedOut`, selecione os outros status:

  ![Alterar o comportamento "executar após" para ter qualquer outro status](./media/logic-apps-exception-handling/run-after-property-multiple-statuses.png)

* Na exibição de código, na definição de JSON da ação, edite a propriedade `runAfter`, que segue essa sintaxe:

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

  Para este exemplo, altere a propriedade `runAfter` de `Succeeded` para `Failed`:

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

  Para especificar que a ação seja executada se a ação predecessora estiver marcada como `Failed`, `Skipped` ou `TimedOut`, adicione os outros status:

  ```json
  "runAfter": {
     "Add_a_row_into_a_table": [
        "Failed", "Skipped", "TimedOut"
     ]
  },
  ```

<a name="scopes"></a>

## <a name="evaluate-actions-with-scopes-and-their-results"></a>Avaliar ações com escopos e seus resultados

Semelhante à execução de etapas após ações individuais com a propriedade `runAfter`, você pode agrupar ações em um [escopo](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md). Você pode usar escopos quando quiser agrupar ações logicamente, avaliar o status agregado do escopo e executar ações com base nesse status. Depois que todas as ações em um escopo terminarem de ser executadas, o próprio escopo obterá seu próprio status. 

Para verificar o status de um escopo, você pode usar os mesmos critérios usados para verificar o status de execução de um aplicativo lógico, como `Succeeded`, `Failed`e assim por diante.

Por padrão, quando todas as ações do escopo forem realizadas com sucesso, o status do escopo será marcado `Succeeded`. Se a ação final em um escopo resultar como `Failed` ou `Aborted`, o status do escopo será marcado como `Failed`.

Para capturar exceções em um escopo de `Failed` e executar ações que manipulam esses erros, você pode usar a propriedade `runAfter` para esse escopo de `Failed`. Dessa forma, se *alguma* ação no escopo falhar e você usar a propriedade `runAfter` para esse escopo, você poderá criar uma única ação para detectar falhas.

Para limites em escopos, consulte [limites e configuração](../logic-apps/logic-apps-limits-and-config.md).

<a name="get-results-from-failures"></a>

### <a name="get-context-and-results-for-failures"></a>Obter contexto e resultados para falhas

Embora a captura de falhas de um escopo seja útil, você também pode querer o contexto para ajudá-lo a entender exatamente quais ações falharam, além de erros ou códigos de status que foram retornados.

A função [`result()`](../logic-apps/workflow-definition-language-functions-reference.md#result) fornece contexto sobre os resultados de todas as ações em um escopo. A função `result()` aceita um único parâmetro, que é o nome do escopo, e retorna uma matriz que contém todos os resultados da ação de dentro desse escopo. Esses objetos de ação incluem os mesmos atributos que o objeto `actions()`, como a hora de início da ação, a hora de término, o status, as entradas, as IDs de correlação e as saídas. Para enviar o contexto para todas as ações que falharam em um escopo, você pode facilmente emparelhar uma expressão de `@result()` com a propriedade `runAfter`.

Para executar uma ação para cada ação em um escopo que tenha um resultado de `Failed` e para filtrar a matriz de resultados para as ações com falha, você pode emparelhar uma expressão `@result()` com uma ação [**Filtrar matriz**](../connectors/connectors-native-query.md) e um loop [**for each**](../logic-apps/logic-apps-control-flow-loops.md) . Você pode pegar a matriz de resultados filtrados e executar uma ação para cada falha usando o loop de `For_each`.

Veja um exemplo, seguido por uma explicação detalhada, que envia uma solicitação HTTP POST com o corpo da resposta para todas as ações que falharam no escopo "My_Scope":

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

Aqui está uma explicação detalhada que descreve o que acontece neste exemplo:

1. Para obter o resultado de todas as ações dentro de "My_Scope", a ação **Filtrar matriz** usa esta expressão de filtro: `@result('My_Scope')`

1. A condição da **matriz de filtro** é qualquer `@result()` item que tenha um status igual a `Failed`. Essa condição filtra a matriz que tem todos os resultados da ação de "My_Scope" para uma matriz com apenas os resultados da ação com falha.

1. Execute uma ação de loop de `For_each` nas saídas de *matriz filtradas* . Esta etapa executa uma ação para cada resultado de ação com falha que foi anteriormente filtrado.

   Se uma única ação no escopo falhar, as ações no loop de `For_each` são executadas apenas uma vez. Várias ações com falha causam uma ação por falha.

1. Envie um HTTP POST no corpo de resposta do item de `For_each`, que é a expressão `@item()['outputs']['body']`.

   A forma `@result()` item é igual à forma `@actions()` e pode ser analisada da mesma maneira.

1. Inclua dois cabeçalhos personalizados com o nome da ação com falha (`@item()['name']`) e a ID de rastreamento do cliente em execução com falha (`@item()['clientTrackingId']`).

Para referência, aqui está um exemplo de um único item de `@result()`, mostrando as propriedades `name`, `body`e `clientTrackingId` que são analisadas no exemplo anterior. Fora de uma ação de `For_each`, `@result()` retorna uma matriz desses objetos.

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

Para executar diferentes padrões de manipulação de exceção, você pode usar as expressões descritas anteriormente neste artigo. Você pode optar por executar uma única ação de tratamento de exceção fora do escopo que aceita toda a matriz filtrada de falhas e remover a ação de `For_each`. Você também pode incluir outras propriedades úteis da resposta `\@result()` conforme descrito anteriormente.

## <a name="azure-diagnostics-and-metrics"></a>Diagnóstico do Azure e métricas

Os padrões anteriores são uma ótima maneira de lidar com erros e exceções dentro de uma execução, mas você também pode identificar e responder a erros independentes da própria execução. O [diagnóstico do Azure](../logic-apps/logic-apps-monitor-your-logic-apps.md) fornece uma maneira simples de enviar todos os eventos de fluxo de trabalho, incluindo todos os status de execução e ação, para uma conta de armazenamento do Azure ou um hub de eventos criado com os [hubs de eventos do Azure](../event-hubs/event-hubs-about.md).

Para avaliar os status de execução, você pode monitorar os logs e as métricas ou publicá-los em qualquer ferramenta de monitoramento que preferir. Uma opção potencial é transmitir todos os eventos por meio de hubs de eventos para [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/). No Stream Analytics, você pode escrever consultas dinâmicas com base em quaisquer anomalias, médias ou falhas dos logs de diagnóstico. Você pode usar Stream Analytics para enviar informações para outras fontes de dados, como filas, tópicos, SQL, Azure Cosmos DB ou Power BI.

## <a name="next-steps"></a>Passos seguintes

* [Veja como um cliente cria o tratamento de erros com os aplicativos lógicos do Azure](../logic-apps/logic-apps-scenario-error-and-exception-handling.md)
* [Encontre mais exemplos e cenários de aplicativos lógicos](../logic-apps/logic-apps-examples-and-scenarios.md)