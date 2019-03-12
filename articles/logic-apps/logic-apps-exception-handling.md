---
title: Erro e manipulação de exceção - Azure Logic Apps | Documentos da Microsoft
description: Saiba mais sobre os padrões de erro e no Azure Logic Apps de manipulação de exceção
services: logic-apps
ms.service: logic-apps
author: dereklee
ms.author: deli
manager: jeconnoc
ms.date: 01/31/2018
ms.topic: article
ms.reviewer: klam, LADocs
ms.suite: integration
ms.openlocfilehash: 3f812c1142b5cd40169f7340163295b0f7ea6a4d
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57779152"
---
# <a name="handle-errors-and-exceptions-in-azure-logic-apps"></a>Lidar com erros e exceções no Azure Logic Apps

A maneira que qualquer arquitetura de integração manipula adequadamente o tempo de inatividade ou problemas causados por sistemas dependentes pode representar um desafio. Para ajudar a criar integrações robustas e flexíveis que lidar graciosamente com problemas e falhas, o Logic Apps fornece uma experiência de primeira classe para a manipulação de erros e exceções. 

<a name="retry-policies"></a>

## <a name="retry-policies"></a>Repetir políticas

Para a exceção de forma mais básica e o tratamento de erros, pode utilizar um *política de repetição* em qualquer ação ou acionador onde for suportado. Uma política de repetição Especifica se e como a ação ou acionador repete uma solicitação quando o pedido original expira ou falhar, que é qualquer solicitação que resulta num 408, 429 ou resposta 5xx. Não se for utilizada nenhuma política de repetição, é utilizada a política predefinida. 

Aqui estão os tipos de política de repetição: 

| Type | Descrição | 
|------|-------------| 
| **Predefinição** | Esta política envia as repetições até quatro *aumentar exponencialmente* intervalos, que dimensione o 7.5 segundos, mas estão colocados entre 5 e 45 segundos. | 
| **Intervalo exponencial**  | Esta política aguarda num intervalo aleatório selecionado a partir de um intervalo de exponencialmente crescente antes de enviar o pedido seguinte. | 
| **Intervalo fixo**  | Esta política tem de aguardar o intervalo especificado antes de enviar o pedido seguinte. | 
| **Nenhum**  | Não reenvie o pedido. | 
||| 

Para obter informações sobre os limites de política de repetição, consulte [Logic Apps limites e configuração](../logic-apps/logic-apps-limits-and-config.md#request-limits). 

### <a name="change-retry-policy"></a>Política de repetição de alteração

Para selecionar uma política de repetição diferente, siga estes passos: 

1. Abra a aplicação lógica no Estruturador da aplicação lógica. 

2. Abra o **definições** para uma ação ou acionador.

3. Se a ação ou acionador suporta políticas de repetição, em **política de repetição**, selecione o tipo desejado. 

Em alternativa, pode especificar manualmente a política de repetição no `inputs` secção para uma ação ou acionador suporta políticas de repetição. Se não especificar uma política de repetição, a ação utiliza a política predefinida.

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
| <*retry-policy-type*> | String | O tipo de política de repetição que pretende utilizar: `default`, `none`, `fixed`, ou `exponential` | 
| <*retry-interval*> | String | O intervalo entre tentativas em que o valor tem de utilizar [formato ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations). O intervalo mínimo predefinido é `PT5S` e o intervalo máximo é `PT1D`. Quando utiliza a política de intervalo exponencial, pode especificar diferentes valores mínimos e máximos. | 
| <*retry-attempts*> | Número inteiro | O número de tentativas de repetição, o que deve ser entre 1 e 90 | 
||||

*Opcional*

| Value | Type | Descrição |
|-------|------|-------------|
| <*minimum-interval*> | String | Para a política de intervalo exponencial, o intervalo menor para o intervalo selecionado aleatoriamente em [formato ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations) | 
| <*maximum-interval*> | String | Para a política de intervalo exponencial, o intervalo maior para o intervalo selecionado aleatoriamente em [formato ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations) | 
|||| 

Aqui estão mais algumas informações sobre os tipos de política diferente.

<a name="default-retry"></a>

### <a name="default"></a>Predefinição

Se não especificar uma política de repetição, a ação utiliza a política predefinida, que é, na verdade, um [política de intervalo exponencial](#exponential-interval) que envia as repetições até quatro aumenta exponencialmente os intervalos são dimensionados pelas 7.5 segundos. O intervalo é colocado entre 5 e 45 segundos. 

Embora não tenham sido explicitamente definida na sua ação ou acionador, eis como a política predefinida se comporta uma ação de HTTP de exemplo:

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

Para especificar que a ação ou acionador não repetir pedidos com falha, defina a <*tipo de política de repetição*> para `none`.

### <a name="fixed-interval"></a>Intervalo fixo

Para especificar que a ação ou acionador aguarda o intervalo especificado antes de enviar o pedido seguinte, defina a <*tipo de política de repetição*> para `fixed`.

*Exemplo*

Esta política de repetição tenta obter as notícias mais recentes duas vezes mais após o primeiro pedido falhou com um atraso de 30 segundos entre cada tentativa de:

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

Para especificar que a ação ou acionador aguarda num intervalo aleatório antes de enviar o pedido seguinte, defina a <*tipo de política de repetição*> para `exponential`. O intervalo aleatório é selecionado a partir de um intervalo de exponencialmente crescente. Opcionalmente, também é possível substituir os intervalos mínimo e máximo de predefinição ao especificar seus próprios intervalos mínimos e máximo.

**Intervalos de variável aleatória**

Esta tabela mostra como o Logic Apps gera uma variável de random uniforme no intervalo especificado para cada novamente até e incluindo o número de tentativas:

| Repetir número | Intervalo mínimo | Intervalo máximo |
|--------------|------------------|------------------|
| 1 | Máx. (0, <*intervalo mínimo*>) | min (intervalo, <*intervalo máximo*>) |
| 2 | Máx. (intervalo, <*intervalo mínimo*>) | min (2 * intervalo, <*intervalo máximo*>) |
| 3 | máximo (2 * intervalo, <*intervalo mínimo*>) | min (4 * intervalo, <*intervalo máximo*>) |
| 4 | máximo (4 * intervalo, <*intervalo mínimo*>) | mínimo (8 * intervalo, <*intervalo máximo*>) |
| .... | .... | .... | 
|||| 

## <a name="catch-and-handle-failures-with-the-runafter-property"></a>Detetar e processar falhas com a propriedade RunAfter

Cada ação da aplicação lógica declara as ações que devem ser concluído antes desta ação inicia, semelhante a como especificar a ordem dos passos no fluxo de trabalho. Uma definição de ação, o **runAfter** propriedade define esta ordem e é um objeto que descreve quais ações e os Estados de ação a executar a ação.

Por predefinição, todas as ações que adicionar no Estruturador da aplicação lógica são definidas para executar depois do passo anterior, quando o resultado do passo anterior for **bem-sucedido**. No entanto, pode personalizar o **runAfter** valor, de modo que ações são disparados quando as ações anteriores resultam **com falhas**, **ignorados**, ou alguma combinação dos seguintes valores. Por exemplo, para adicionar um item para um tópico do Service Bus específico após um específico **Insert_Row** ação falhar, poderia usar este exemplo **runAfter** definição:

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

O **runAfter** estiver definida para ser executada quando o **Insert_Row** é o estado da ação **falha**. Para executar a ação, se o estado da ação **bem-sucedido**, **com falhas**, ou **ignorados**, usar esta sintaxe:

```json
"runAfter": {
        "Insert_Row": [
            "Failed", "Succeeded", "Skipped"
        ]
    }
```

> [!TIP]
> Ações que executarem e concluir com êxito após uma ação anterior falhou, são marcados como **bem-sucedido**. Este comportamento significa que, se com êxito catch todas as falhas num fluxo de trabalho, a execução propriamente dito é marcado como **bem-sucedido**.

<a name="scopes"></a>

## <a name="evaluate-actions-with-scopes-and-their-results"></a>Avaliar as ações com âmbitos e seus resultados

Semelhante ao executar passos depois de ações individuais com o **runAfter** propriedade, é possível agrupar ações dentro de um [âmbito](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md). Pode utilizar âmbitos de quando deseja agrupar ações, avaliar o estado agregado de âmbito e executar ações com base no Estado logicamente. Depois de todas as ações num âmbito termine a execução, o escopo em si obtém seu próprio Estado. 

Para verificar o estado de um âmbito, pode utilizar os mesmos critérios que usar para verificar o estado de execução de uma aplicação lógica, como **bem-sucedido**, **falha**e assim por diante. 

Por predefinição, quando tiver êxito ações de âmbito, estado do escopo é marcado **bem-sucedido**. Se a ação final num âmbito resulta como **falhada** ou **Aborted**, estado de âmbito está marcada como **falha**. 

Para capturar exceções numa **falhada** âmbito e as ações de execução que lidar com esses erros, pode utilizar o **runAfter** propriedade para que **falhou** âmbito. Dessa forma, se *qualquer* ações no âmbito da falham e utilizar os **runAfter** propriedade para esse âmbito, pode criar uma ação única para detectar falhas.

Para limites em âmbitos, consulte [limites e configuração](../logic-apps/logic-apps-limits-and-config.md).

### <a name="get-context-and-results-for-failures"></a>Obter o contexto e os resultados para falhas

Embora a deteção de falhas de um âmbito é útil, também poderá contexto para o ajudar a compreender exatamente quais ações falha além de quaisquer erros ou códigos de estado que foram devolvidos. O `@result()` expressão fornece o contexto sobre o resultado de todas as ações num âmbito.

O `@result()` expressão aceita um parâmetro único (nome de âmbito) e retorna uma matriz de todos os ação resultados de dentro dele. Estes objetos de ação incluem os mesmos atributos como a  **\@actions()** objeto, como a ação a hora de início, hora de fim, estado, entradas, IDs de correlação e saídas. Para enviar o contexto para todas as ações que falharam num âmbito, pode facilmente emparelhar um  **\@result()** funcionar com uma **runAfter** propriedade.

Para executar uma ação para cada ação num âmbito que tem um **falhada** resultado, e para filtrar a matriz de resultados e as ações com falha, pode emparelhar  **\@result()** com um **[ Filtrar matrizes](../connectors/connectors-native-query.md)** ação e um [ **para cada** ](../logic-apps/logic-apps-control-flow-loops.md) loop. Pode demorar a matriz de resultado filtrado e executar uma ação para cada falha utilizando o **para cada** loop. 

Eis um exemplo, seguido de uma explicação detalhada, que envia um pedido de HTTP POST com o corpo de resposta para todas as ações que falhou no âmbito "My_Scope":

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

Aqui está um passo a passo detalhada que descreve o que acontece neste exemplo:

1. Para obter o resultado de todas as ações de dentro "My_Scope", o **matriz de filtro** ação utiliza esta expressão de filtro: `@result('My_Scope')`

2. A condição para **matriz de filtro** alguma `@result()` item que tem o Estado igual a **falha**. Esta condição filtra a matriz que tenha todos os ação resultados de "My_Scope" para baixo para uma matriz com apenas os resultados da ação com falha.

3. Efetuar uma **para cada** loop ação no *matriz filtrado* saídas. Este passo executa uma ação para cada resultado da ação com falha que anteriormente foi filtrado.

   Se uma única ação no âmbito da falha, as ações no **para cada** loop executado apenas uma vez. 
   Várias ações falhadas fazer com que uma ação por falha.

4. Enviar um HTTP POST sobre o **para cada** item corpo de resposta, o que é o `@item()['outputs']['body']` expressão. 

   O `@result()` forma de item é o mesmo que o `@actions()` formatar e pode ser analisado da mesma forma.

5. Incluem dois cabeçalhos personalizados com o nome da ação com falhas (`@item()['name']`) e a executa o cliente, ID de controlo (`@item()['clientTrackingId']`).

Para referência, eis um exemplo de uma única `@result()` item, que mostra a **name**, **corpo**, e **clientTrackingId** propriedades que são analisadas no anterior exemplo. Fora um **para cada** ação, `@result()` retorna uma matriz desses objetos.

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

Para efetuar diferente padrões de manipulação de exceção, pode utilizar as expressões descritas anteriormente neste artigo. Pode optar por executar uma única ação fora do âmbito que aceita a matriz filtrada completa de falhas de manipulação de exceção e remover as **para cada** ação. Também pode incluir outras propriedades úteis a partir da  **\@result()** resposta conforme descrita anteriormente.

## <a name="azure-diagnostics-and-metrics"></a>Métricas e diagnósticos do Azure

Os padrões anteriores são excelentes maneira de lidar com erros e exceções dentro de uma execução, mas também pode identificar e responder a erros independentes da execução propriamente dito. 
[Diagnóstico do Azure](../logic-apps/logic-apps-monitor-your-logic-apps.md) fornece uma forma simple de enviar todos os eventos de fluxo de trabalho, incluindo todos os Estados de execução e a ação, para uma conta de armazenamento do Azure ou de um hub de eventos criada com os Hubs de eventos do Azure. 

Para avaliar os Estados de execução, pode monitorizar os registos e métricas ou publique-os em qualquer ferramenta de monitoramento que preferir. Uma opção de potencial é para todos os eventos por meio dos Hubs de eventos para transmitir em fluxo [do Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/). No Stream Analytics, pode escrever consultas em direto com base em quaisquer anomalias, médias ou falhas dos registos de diagnóstico. Pode utilizar o Stream Analytics para enviar informações para outras origens de dados, tais como filas, tópicos, SQL, Azure Cosmos DB ou do Power BI.

## <a name="next-steps"></a>Passos Seguintes

* [Veja como um cliente baseia-se com o Azure Logic Apps de tratamento de erros](../logic-apps/logic-apps-scenario-error-and-exception-handling.md)
* [Encontre mais exemplos de aplicações lógicas e cenários](../logic-apps/logic-apps-examples-and-scenarios.md)

<!-- References -->
[retryPolicyMSDN]: https://docs.microsoft.com/rest/api/logic/actions-and-triggers#Anchor_9
