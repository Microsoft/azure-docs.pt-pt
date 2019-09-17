---
title: Tratamento de erros e exceções-aplicativos lógicos do Azure
description: Saiba mais sobre padrões para tratamento de erros e exceções em aplicativos lógicos do Azure
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: dereklee
ms.author: deli
ms.reviewer: klam, estfan, LADocs
ms.date: 01/31/2018
ms.topic: article
ms.openlocfilehash: 828bea50a66b90f35843901ae2d7c703ffa58f2d
ms.sourcegitcommit: 5f67772dac6a402bbaa8eb261f653a34b8672c3a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/01/2019
ms.locfileid: "70208185"
---
# <a name="handle-errors-and-exceptions-in-azure-logic-apps"></a>Tratar erros e exceções nos aplicativos lógicos do Azure

A maneira como qualquer arquitetura de integração controla adequadamente o tempo de inatividade ou os problemas causados por sistemas dependentes pode representar um desafio. Para ajudá-lo a criar integrações robustas e resilientes que lidam normalmente com problemas e falhas, os aplicativos lógicos fornecem uma experiência de primeira classe para lidar com erros e exceções. 

<a name="retry-policies"></a>

## <a name="retry-policies"></a>Repetir políticas

Para obter a exceção mais básica e o tratamento de erros, você pode usar uma *política de repetição* em qualquer ação ou gatilho onde houver suporte. Uma política de repetição especifica se e como a ação ou o gatilho tenta novamente uma solicitação quando a solicitação original atinge o tempo limite ou falha, o que é qualquer solicitação que resulte em uma resposta 408, 429 ou 5xx. Se nenhuma outra política de repetição for usada, a política padrão será usada. 

Estes são os tipos de política de repetição: 

| Type | Descrição | 
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

2. Abra as **configurações** de uma ação ou gatilho.

3. Se a ação ou o gatilho der suporte a políticas de repetição, em **política de repetição**, selecione o tipo desejado. 

Ou, você pode especificar manualmente a política de repetição na `inputs` seção para uma ação ou um gatilho que ofereça suporte a políticas de repetição. Se você não especificar uma política de repetição, a ação usará a política padrão.

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
         "maximumInterval": "<maximun-interval>"
      },
      "<other-action-specific-inputs>"
   },
   "runAfter": {}
}
```

*Necessário*

| Value | Type | Descrição |
|-------|------|-------------|
| <*retry-policy-type*> | String | O tipo de política de repetição que você deseja `default`usar `none`: `fixed`,, ou`exponential` | 
| <*retry-interval*> | String | O intervalo de repetição em que o valor deve usar o [formato ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations). O intervalo mínimo padrão é `PT5S` e o intervalo máximo é `PT1D`. Ao usar a política de intervalo exponencial, você pode especificar valores mínimo e máximo diferentes. | 
| <*retry-attempts*> | Integer | O número de tentativas de repetição, que devem estar entre 1 e 90 | 
||||

*Adicional*

| Value | Type | Descrição |
|-------|------|-------------|
| <*intervalo mínimo*> | Cadeia | Para a política de intervalo exponencial, o menor intervalo para o intervalo selecionado aleatoriamente no [formato ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations) | 
| <*intervalo máximo*> | Cadeia | Para a política de intervalo exponencial, o maior intervalo para o intervalo selecionado aleatoriamente no [formato ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations) | 
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

### <a name="none"></a>Nenhum

Para especificar que a ação ou o gatilho não tente novamente as solicitações com falha, defina o < >*tentativa-política-tipo*para `none`.

### <a name="fixed-interval"></a>Intervalo fixo

Para especificar que a ação ou o gatilho aguarde o intervalo especificado antes de enviar a próxima solicitação, defina o < > de*tentativa-política*de `fixed`repetição para.

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

Para especificar que a ação ou o gatilho aguarda um intervalo aleatório antes de enviar a próxima solicitação, defina o < > de*tentativa-política*de `exponential`repetição para. O intervalo aleatório é selecionado de um intervalo de aumento exponencial. Opcionalmente, você também pode substituir os intervalos mínimo e máximo padrão especificando seus próprios intervalos mínimo e máximo.

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

## <a name="catch-and-handle-failures-with-the-runafter-property"></a>Detectar e lidar com falhas com a propriedade RunAfter

Cada ação de aplicativo lógico declara as ações que devem ser concluídas antes que a ação seja iniciada, semelhante a como você especifica a ordem das etapas em seu fluxo de trabalho. Em uma definição de ação, a propriedade **runAfter** define essa ordenação e é um objeto que descreve quais status de ação e ações executam a ação.

Por padrão, todas as ações que você adiciona no designer de aplicativo lógico são definidas para execução após a etapa anterior quando o resultado da etapa anterior é **bem-sucedido**. No entanto, você pode personalizar o valor **runAfter** para que as ações sejam acionadas quando as ações anteriores resultam em **falha**, **ignoradas**ou alguma combinação desses valores. Por exemplo, para adicionar um item a um tópico específico do barramento de serviço após a falha de uma ação **Insert_Row** específica, você poderia usar esta definição de **runAfter** de exemplo:

```json
"Send_message": {
    "inputs": {
        "body": {
            "ContentData": "@{encodeBase64(body('Insert_Row'))}",
            "ContentType": "{ \"content-type\" : \"application/json\" }"
        },
        "host": {
            "api": {
                "runtimeUrl": "https://logic-apis-westus.azure-apim.net/apim/servicebus"
            },
            "connection": {
                "name": "@parameters('$connections')['servicebus']['connectionId']"
            }
        },
        "method": "post",
        "path": "/@{encodeURIComponent('failures')}/messages"
    },
    "runAfter": {
        "Insert_Row": [
            "Failed"
        ]
    }
}
```

A propriedade **runAfter** é definida para ser executada quando o status da ação **Insert_Row** é **falha**. Para executar a ação se o status da ação for **êxito**, **falhar**ou **ignorado**, use esta sintaxe:

```json
"runAfter": {
        "Insert_Row": [
            "Failed", "Succeeded", "Skipped"
        ]
    }
```

> [!TIP]
> Ações que são executadas e concluídas com êxito após a falha de uma ação anterior foram marcadas como **bem-sucedidas**. Esse comportamento significa que, se você detectar todas as falhas em um fluxo de trabalho com êxito, a execução será marcada como **bem-sucedida**.

<a name="scopes"></a>

## <a name="evaluate-actions-with-scopes-and-their-results"></a>Avaliar ações com escopos e seus resultados

Semelhante à execução de etapas após ações individuais com a propriedade **runAfter** , você pode agrupar ações em um [escopo](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md). Você pode usar escopos quando quiser agrupar ações logicamente, avaliar o status agregado do escopo e executar ações com base nesse status. Depois que todas as ações em um escopo terminarem de ser executadas, o próprio escopo obterá seu próprio status. 

Para verificar o status de um escopo, você pode usar os mesmos critérios usados para verificar o status de execução de um aplicativo lógico, como com **êxito**, **falha**e assim por diante. 

Por padrão, quando todas as ações do escopo forem bem-sucedidas, o status do escopo será marcado como **êxito**. Se a ação final em um escopo resultar em **falha** ou **anulada**, o status do escopo será marcado como **falha**. 

Para capturar exceções em um escopo **com falha** e executar ações que manipulam esses erros, você pode usar a propriedade **runAfter** para esse escopo **com falha** . Dessa forma, se *alguma* ação no escopo falhar e você usar a propriedade **runAfter** para esse escopo, você poderá criar uma única ação para detectar falhas.

Para limites em escopos, consulte [limites e configuração](../logic-apps/logic-apps-limits-and-config.md).

<a name="get-results-from-failures"></a>

### <a name="get-context-and-results-for-failures"></a>Obter contexto e resultados para falhas

Embora a captura de falhas de um escopo seja útil, você também pode querer o contexto para ajudá-lo a entender exatamente quais ações falharam, além de erros ou códigos de status que foram retornados.

A [`result()`](../logic-apps/workflow-definition-language-functions-reference.md#result) função fornece contexto sobre os resultados de todas as ações em um escopo. A `result()` função aceita um único parâmetro, que é o nome do escopo, e retorna uma matriz que contém todos os resultados de ação de dentro desse escopo. Esses objetos de ação incluem os mesmos atributos que `@actions()` o objeto, como a hora de início da ação, a hora de término, o status, as entradas, as IDs de correlação e as saídas. Para enviar o contexto para todas as ações que falharam em um escopo, você pode `@result()` facilmente emparelhar `runAfter` uma expressão com a propriedade.

Para executar uma ação para cada ação em um escopo que tenha um resultado **com falha** e filtrar a matriz de resultados para as ações com falha, você pode emparelhar uma `@result()` expressão com uma ação [**Filtrar matriz**](../connectors/connectors-native-query.md) e um loop [**for each**](../logic-apps/logic-apps-control-flow-loops.md) . Você pode pegar a matriz de resultados filtrados e executar uma ação para cada falha usando o loop **for each** .

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

1. Para obter o resultado de todas as ações dentro de "My_Scope", a ação **Filtrar matriz** usa esta expressão de filtro:`@result('My_Scope')`

2. A condição da **matriz de filtro** é `@result()` qualquer item que tenha um status igual a com **falha**. Essa condição filtra a matriz que tem todos os resultados de ação de "My_Scope" para uma matriz com apenas os resultados de ação com falha.

3. Execute uma ação **para cada** loop nas saídas da *matriz filtrada* . Esta etapa executa uma ação para cada resultado de ação com falha que foi anteriormente filtrado.

   Se uma única ação no escopo falhar, as ações no loop **for each** são executadas apenas uma vez. 
   Várias ações com falha causam uma ação por falha.

4. Envie um http post no corpo **de resposta de cada** item, que é a `@item()['outputs']['body']` expressão. 

   A `@result()` forma do item é igual `@actions()` à forma e pode ser analisada da mesma maneira.

5. Inclua dois cabeçalhos personalizados com o nome da ação com`@item()['name']`falha () e a ID de rastreamento do`@item()['clientTrackingId']`cliente em execução com falha ().

Para referência, aqui está um exemplo de um único `@result()` item, mostrando as propriedades **Name**, **Body**e **clientTrackingId** que são analisadas no exemplo anterior. Fora de um **para cada** ação `@result()` , retorna uma matriz desses objetos.

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

Para executar diferentes padrões de manipulação de exceção, você pode usar as expressões descritas anteriormente neste artigo. Você pode optar por executar uma única ação de tratamento de exceção fora do escopo que aceita toda a matriz filtrada de falhas e remover a ação **para cada** . Você também pode incluir outras propriedades úteis da  **\@resposta Result ()** , conforme descrito anteriormente.

## <a name="azure-diagnostics-and-metrics"></a>Diagnóstico do Azure e métricas

Os padrões anteriores são uma ótima maneira de lidar com erros e exceções dentro de uma execução, mas você também pode identificar e responder a erros independentes da própria execução. 
O [diagnóstico do Azure](../logic-apps/logic-apps-monitor-your-logic-apps.md) fornece uma maneira simples de enviar todos os eventos de fluxo de trabalho, incluindo todos os status de execução e ação, para uma conta de armazenamento do Azure ou um hub de eventos criado com os hubs de eventos do Azure. 

Para avaliar os status de execução, você pode monitorar os logs e as métricas ou publicá-los em qualquer ferramenta de monitoramento que preferir. Uma opção potencial é transmitir todos os eventos por meio de hubs de eventos para [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/). No Stream Analytics, você pode escrever consultas dinâmicas com base em quaisquer anomalias, médias ou falhas dos logs de diagnóstico. Você pode usar Stream Analytics para enviar informações para outras fontes de dados, como filas, tópicos, SQL, Azure Cosmos DB ou Power BI.

## <a name="next-steps"></a>Passos Seguintes

* [Veja como um cliente cria o tratamento de erros com os aplicativos lógicos do Azure](../logic-apps/logic-apps-scenario-error-and-exception-handling.md)
* [Encontre mais exemplos e cenários de aplicativos lógicos](../logic-apps/logic-apps-examples-and-scenarios.md)

<!-- References -->
[retryPolicyMSDN]: https://docs.microsoft.com/rest/api/logic/actions-and-triggers#Anchor_9
