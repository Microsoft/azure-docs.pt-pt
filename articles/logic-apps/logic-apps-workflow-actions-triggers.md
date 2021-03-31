---
title: Referência de esquema para tipos de gatilho e de ação
description: Guia de referência de Schema para definição de fluxo de trabalho Detonador de linguagem e tipos de ação em Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: jonfan, logicappspm
ms.topic: conceptual
ms.date: 09/22/2020
ms.custom: devx-track-js
ms.openlocfilehash: ea4a4a47e91e88c00ca8a4e886d0372a24482907
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98784313"
---
# <a name="schema-reference-guide-for-trigger-and-action-types-in-azure-logic-apps"></a>Guia de referência de Schema para tipos de gatilho e ação em Azure Logic Apps

Esta referência descreve os tipos gerais utilizados para identificar gatilhos e ações na definição subjacente do fluxo de trabalho da sua aplicação lógica, que é descrito e validado pela Linguagem de Definição de [Fluxo de Trabalho.](../logic-apps/logic-apps-workflow-definition-language.md) Para encontrar gatilhos e ações específicas do conector que pode utilizar nas suas aplicações lógicas, consulte a lista na visão geral dos [Conectores](/connectors/).

<a name="triggers-overview"></a>

## <a name="triggers-overview"></a>Desencadeia visão geral

Cada fluxo de trabalho inclui um gatilho, que define as chamadas que instantaneam e iniciam o fluxo de trabalho. Aqui estão as categorias gerais de gatilho:

* Um gatilho *de sondagens,* que verifica o ponto final de um serviço em intervalos regulares

* Um gatilho *de impulso,* que cria uma subscrição de um ponto final e fornece um *URL de retorno para* que o ponto final possa notificar o gatilho quando o evento especificado acontece ou os dados estão disponíveis. Em seguida, o gatilho aguarda a resposta do ponto final antes de disparar.

Os gatilhos têm estes elementos de alto nível, embora alguns sejam opcionais:  
  
```json
"<trigger-name>": {
   "type": "<trigger-type>",
   "inputs": { "<trigger-inputs>" },
   "recurrence": { 
      "frequency": "<time-unit>",
      "interval": <number-of-time-units>
   },
   "conditions": [ "<array-with-conditions>" ],
   "runtimeConfiguration": { "<runtime-config-options>" },
   "splitOn": "<splitOn-expression>",
   "operationOptions": "<operation-option>"
},
```

*Obrigatório*

| Valor | Tipo | Description | 
|-------|------|-------------| 
| <*gatilho nome*> | String | O nome do gatilho | 
| <*tipo de gatilho*> | String | O tipo de gatilho como "Http" ou "ApiConnection" | 
| <*entradas de gatilho*> | Objeto JSON | As entradas que definem o comportamento do gatilho | 
| <*unidade de tempo*> | String | A unidade de tempo que descreve com que frequência o gatilho dispara: "Segundo", "Minuto", "Hora", "Dia", "Semana", "Mês" | 
| <*número de tempo-unidades*> | Número inteiro | Um valor que especifica quantas vezes o gatilho dispara com base na frequência, que é o número de unidades de tempo para esperar até que o gatilho volte a disparar. <p>Aqui estão os intervalos mínimos e máximos: <p>- Mês: 1-16 meses </br>- Dia: 1-500 dias </br>- Hora: 1-12.000 horas </br>- Minuto: 1-72.000 minutos </br>- Segundo: 1-9.999,999 segundos<p>Por exemplo, se o intervalo for 6, e a frequência for "Mês", a recorrência é a cada 6 meses. | 
|||| 

*Opcional*

| Valor | Tipo | Description | 
|-------|------|-------------| 
| <*matriz com condições*> | Matriz | Uma matriz que contém uma ou mais [condições](#trigger-conditions) que determinam se devem executar o fluxo de trabalho. Disponível apenas para gatilhos. | 
| <*runtime-config-opções*> | Objeto JSON | Pode alterar o comportamento do tempo de execução do gatilho definindo `runtimeConfiguration` propriedades. Para obter mais informações, consulte [as definições de configuração do tempo de execução](#runtime-config-options). | 
| <*splitOn-expressão*> | String | Para os gatilhos que devolvem uma matriz, pode especificar uma expressão que [divide ou *debate*](#split-on-debatch) itens de matriz em múltiplas instâncias de fluxo de trabalho para processamento. | 
| <*opção de operação*> | String | Pode alterar o comportamento padrão definindo a `operationOptions` propriedade. Para mais informações, consulte [as opções de Operação.](#operation-options) | 
|||| 

## <a name="trigger-types-list"></a>Lista de tipos de gatilho

Cada tipo de gatilho tem uma interface e entradas diferentes que definem o comportamento do gatilho. 

### <a name="built-in-triggers"></a>Gatilhos incorporados

| Tipo de acionador | Description | 
|--------------|-------------| 
| [**HTTP**](#http-trigger) | Verifica ou *sonda* qualquer ponto final. Este ponto final deve estar em conformidade com um contrato de gatilho específico, utilizando um `202` padrão assíncrona ou devolvendo uma matriz. | 
| [**HTTPWebhook**](#http-webhook-trigger) | Cria um ponto final callable para a sua aplicação lógica, mas chama o URL especificado para registar ou não registar. |
| [**Recorrência**](#recurrence-trigger) | Incêndios baseados num horário definido. Pode definir uma data e hora futuras para disparar este gatilho. Com base na frequência, também pode especificar horas e dias para executar o seu fluxo de trabalho. | 
| [**Pedir**](#request-trigger)  | Cria um ponto final callable para a sua aplicação lógica e também é conhecido como um gatilho "manual". Por exemplo, consulte [fluxos de trabalho de chamada, gatilho ou ninho com pontos finais HTTP](../logic-apps/logic-apps-http-endpoint.md). | 
||| 

### <a name="managed-api-triggers"></a>Gatilhos de API geridos

| Tipo de acionador | Description | 
|--------------|-------------| 
| [**ApiConnection**](#apiconnection-trigger) | Verifica ou *sonda* um ponto final utilizando [APIs geridos](../connectors/apis-list.md)pela Microsoft . | 
| [**ApiConnectionWebhook**](#apiconnectionwebhook-trigger) | Cria um ponto final callable para a sua aplicação lógica, chamando [apis geridos](../connectors/apis-list.md) pela Microsoft para subscrever e cancelar a subscrição. | 
||| 

## <a name="triggers---detailed-reference"></a>Acionadores – Referência detalhada

<a name="apiconnection-trigger"></a>

### <a name="apiconnection-trigger"></a>Gatilho APIConnection  

Este gatilho verifica ou *sonda* um ponto final utilizando [APIs geridos](../connectors/apis-list.md) pela Microsoft para que os parâmetros para este gatilho possam diferir com base no ponto final. Muitas secções nesta definição de gatilho são opcionais. O comportamento do gatilho depende se as secções estão ou não incluídas.

```json
"<APIConnection_trigger_name>": {
   "type": "ApiConnection",
   "inputs": {
      "host": {
         "connection": {
            "name": "@parameters('$connections')['<connection-name>']['connectionId']"
         }
      },
      "method": "<method-type>",
      "path": "/<api-operation>",
      "retryPolicy": { "<retry-behavior>" },
      "queries": { "<query-parameters>" }
   },
   "recurrence": { 
      "frequency": "<time-unit>",
      "interval": <number-of-time-units>
   },
   "runtimeConfiguration": {
      "concurrency": {
         "runs": <max-runs>,
         "maximumWaitingRuns": <max-runs-queue>
      }
   },
   "splitOn": "<splitOn-expression>",
   "operationOptions": "<operation-option>"
}
```

*Obrigatório*

| Valor | Tipo | Description |
|-------|------|-------------|
| <*APIConnection_trigger_name*> | String | O nome do gatilho |
| <*nome de conexão*> | String | O nome para a ligação à API gerida que o fluxo de trabalho utiliza |
| <*tipo método*> | String | O método HTTP para comunicação com a API gerida: "GET", "PUT", "POST", "PATCH", "DELETE" |
| <*api-operação*> | String | A operação da API para ligar |
| <*unidade de tempo*> | String | A unidade de tempo que descreve com que frequência o gatilho dispara: "Segundo", "Minuto", "Hora", "Dia", "Semana", "Mês" |
| <*número de tempo-unidades*> | Número inteiro | Um valor que especifica quantas vezes o gatilho dispara com base na frequência, que é o número de unidades de tempo para esperar até que o gatilho volte a disparar. <p>Aqui estão os intervalos mínimos e máximos: <p>- Mês: 1-16 meses </br>- Dia: 1-500 dias </br>- Hora: 1-12.000 horas </br>- Minuto: 1-72.000 minutos </br>- Segundo: 1-9.999,999 segundos<p>Por exemplo, se o intervalo for 6, e a frequência for "Mês", a recorrência é a cada 6 meses. |
||||

*Opcional*

| Valor | Tipo | Description | 
|-------|------|-------------| 
| <*recandidutar comportamento*> | Objeto JSON | Personaliza o comportamento de repetição para falhas intermitentes, que têm o código de estado 408, 429 e 5XX, e quaisquer exceções de conectividade. Para obter mais informações, consulte [as políticas de Retry](../logic-apps/logic-apps-exception-handling.md#retry-policies). | 
| <*parâmetros de consulta*> | Objeto JSON | Quaisquer parâmetros de consulta para incluir com a chamada API. Por exemplo, o `"queries": { "api-version": "2018-01-01" }` objeto adiciona `?api-version=2018-01-01` à chamada. | 
| <*max-runs*> | Número inteiro | Por predefinição, as instâncias de fluxo de trabalho são executadas ao mesmo tempo (simultaneamente ou paralelamente) até ao [limite por defeito](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Para alterar este limite, definindo uma nova *contagem* de <> valor, consulte [Change trigger concurrency](#change-trigger-concurrency). | 
| <*max-runs-fila*> | Número inteiro | Quando o seu fluxo de trabalho já está a executar o número máximo de casos, que pode alterar com base na `runtimeConfiguration.concurrency.runs` propriedade, quaisquer novas correções são colocadas nesta fila até ao [limite predefinido](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Para alterar o limite de predefinição, consulte [o limite de execuções de espera change](#change-waiting-runs). | 
| <*splitOn-expressão*> | String | Para os gatilhos que devolvem matrizes, esta expressão refere a matriz a utilizar para que possa criar e executar uma instância de fluxo de trabalho para cada item de matriz, em vez de usar um laço "para cada um". <p>Por exemplo, esta expressão representa um item na matriz devolvido dentro do conteúdo do corpo do gatilho: `@triggerbody()?['value']` |
| <*opção de operação*> | String | Pode alterar o comportamento padrão definindo a `operationOptions` propriedade. Para mais informações, consulte [as opções de Operação.](#operation-options) |
||||

*Saídas*
 
| Elemento | Tipo | Description |
|---------|------|-------------|
| cabeçalhos | Objeto JSON | Os cabeçalhos da resposta |
| body | Objeto JSON | O corpo da resposta |
| código de estado | Número inteiro | O código de estado da resposta |
|||| 

*Exemplo*

Esta definição de gatilho verifica todos os dias o e-mail dentro da caixa de entrada para uma conta de trabalho ou escola:

```json
"When_a_new_email_arrives": {
   "type": "ApiConnection",
   "inputs": {
      "host": {
         "connection": {
            "name": "@parameters('$connections')['office365']['connectionId']"
         }
      },
      "method": "get",
      "path": "/Mail/OnNewEmail",
      "queries": {
          "fetchOnlyWithAttachment": false,
          "folderPath": "Inbox",
          "importance": "Any",
          "includeAttachments": false
      }
   },
   "recurrence": {
      "frequency": "Day",
      "interval": 1
   }
}
```

<a name="apiconnectionwebhook-trigger"></a>

### <a name="apiconnectionwebhook-trigger"></a>Gatilho ApiConnectionWebhook

Este gatilho envia um pedido de subscrição para um ponto final utilizando uma [API gerida](../connectors/apis-list.md)pela Microsoft, fornece um *URL de retorno* para onde o ponto final pode enviar uma resposta, e aguarda que o ponto final responda. Para mais informações, consulte [as assinaturas Endpoint](#subscribe-unsubscribe).

```json
"<ApiConnectionWebhook_trigger_name>": {
   "type": "ApiConnectionWebhook",
   "inputs": {
      "body": {
          "NotificationUrl": "@{listCallbackUrl()}"
      },
      "host": {
         "connection": {
            "name": "@parameters('$connections')['<connection-name>']['connectionId']"
         }
      },
      "retryPolicy": { "<retry-behavior>" },
      "queries": "<query-parameters>"
   },
   "runTimeConfiguration": {
      "concurrency": {
         "runs": <max-runs>,
         "maximumWaitingRuns": <max-run-queue>
      }
   },
   "splitOn": "<splitOn-expression>",
   "operationOptions": "<operation-option>"
}
```

*Obrigatório*

| Valor | Tipo | Description | 
|-------|------|-------------| 
| <*nome de conexão*> | String | O nome para a ligação à API gerida que o fluxo de trabalho utiliza | 
| <*conteúdo corporal*> | Objeto JSON | Qualquer conteúdo de mensagem para enviar como carga útil para a API gerida | 
||||

*Opcional*

| Valor | Tipo | Description |
|-------|------|-------------|
| <*recandidutar comportamento*> | Objeto JSON | Personaliza o comportamento de repetição para falhas intermitentes, que têm o código de estado 408, 429 e 5XX, e quaisquer exceções de conectividade. Para obter mais informações, consulte [as políticas de Retry](../logic-apps/logic-apps-exception-handling.md#retry-policies). |
| <*parâmetros de consulta*> | Objeto JSON | Quaisquer parâmetros de consulta para incluir com a chamada API <p>Por exemplo, o `"queries": { "api-version": "2018-01-01" }` objeto adiciona `?api-version=2018-01-01` à chamada. |
| <*max-runs*> | Número inteiro | Por predefinição, as instâncias de fluxo de trabalho são executadas ao mesmo tempo (simultaneamente ou paralelamente) até ao [limite por defeito](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Para alterar este limite, definindo uma nova *contagem* de <> valor, consulte [Change trigger concurrency](#change-trigger-concurrency). |
| <*max-runs-fila*> | Número inteiro | Quando o seu fluxo de trabalho já está a executar o número máximo de casos, que pode alterar com base na `runtimeConfiguration.concurrency.runs` propriedade, quaisquer novas correções são colocadas nesta fila até ao [limite predefinido](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Para alterar o limite de predefinição, consulte [o limite de execuções de espera change](#change-waiting-runs). | 
| <*splitOn-expressão*> | String | Para os gatilhos que devolvem matrizes, esta expressão refere a matriz a utilizar para que possa criar e executar uma instância de fluxo de trabalho para cada item de matriz, em vez de usar um laço "para cada um". <p>Por exemplo, esta expressão representa um item na matriz devolvido dentro do conteúdo do corpo do gatilho: `@triggerbody()?['value']` |
| <*opção de operação*> | String | Pode alterar o comportamento padrão definindo a `operationOptions` propriedade. Para mais informações, consulte [as opções de Operação.](#operation-options) | 
|||| 

*Exemplo*

Esta definição de gatilho subscreve a API do Office 365 Outlook, fornece um URL de retorno ao ponto final da API e aguarda que o ponto final responda quando um novo e-mail chegar.

```json
"When_a_new_email_arrives_(webhook)": {
   "type": "ApiConnectionWebhook",
   "inputs": {
      "body": {
         "NotificationUrl": "@{listCallbackUrl()}" 
      },
      "host": {
         "connection": {
            "name": "@parameters('$connections')['office365']['connectionId']"
         }
      },
      "path": "/MailSubscription/$subscriptions",
      "queries": {
          "folderPath": "Inbox",
          "hasAttachment": "Any",
          "importance": "Any"
      }
   },
   "splitOn": "@triggerBody()?['value']"
}
```

<a name="http-trigger"></a>

### <a name="http-trigger"></a>Acionador HTTP

Este gatilho envia um pedido para o ponto final HTTP ou HTTPS especificado com base no calendário de recorrência especificado. Em seguida, o gatilho verifica a resposta para determinar se o fluxo de trabalho funciona. Para obter mais informações, consulte [os pontos finais do serviço call em HTTP ou HTTPS a partir de Azure Logic Apps](../connectors/connectors-native-http.md).

```json
"HTTP": {
   "type": "Http",
   "inputs": {
      "method": "<method-type>",
      "uri": "<HTTP-or-HTTPS-endpoint-URL>",
      "headers": { "<header-content>" },
      "queries": "<query-parameters>",
      "body": "<body-content>",
      "authentication": { "<authentication-type-and-property-values>" },
      "retryPolicy": {
         "type": "<retry-behavior>"
      }
   },
   "recurrence": {
      "frequency": "<time-unit>",
      "interval": <number-of-time-units>
   },
   "runtimeConfiguration": {
      "concurrency": {
         "runs": <max-runs>,
         "maximumWaitingRuns": <max-runs-queue>
      }
   },
   "operationOptions": "<operation-option>"
}
```

*Obrigatório*

| Propriedade | Valor | Tipo | Description |
|----------|-------|------|-------------|
| `method` | <*tipo método*> | String | O método a utilizar para o envio do pedido de saída: "GET", "PUT", "POST", "PATCH" ou "DELETE" |
| `uri` | <*HTTP-ou-HTTPS-ENDpoint-URL*> | String | O URL de ponto final HTTP ou HTTPS onde pretende enviar o pedido de saída. Tamanho máximo da corda: 2 KB <p>Para um serviço ou recurso Azure, esta sintaxe URI inclui o ID de recurso e o caminho para o recurso a que pretende aceder. |
| `frequency` | <*unidade de tempo*> | String | A unidade de tempo que descreve com que frequência o gatilho dispara: "Segundo", "Minuto", "Hora", "Dia", "Semana", "Mês" |
| `interval` | <*número de tempo-unidades*> | Número inteiro | Um valor que especifica quantas vezes o gatilho dispara com base na frequência, que é o número de unidades de tempo para esperar até que o gatilho volte a disparar. <p>Aqui estão os intervalos mínimos e máximos: <p>- Mês: 1-16 meses </br>- Dia: 1-500 dias </br>- Hora: 1-12.000 horas </br>- Minuto: 1-72.000 minutos </br>- Segundo: 1-9.999,999 segundos<p>Por exemplo, se o intervalo for 6, e a frequência for "Mês", a recorrência é a cada 6 meses. |
|||||

*Opcional*

| Propriedade | Valor | Tipo | Description |
|----------|-------|------|-------------|
| `headers` | <*cabeçalho-conteúdo*> | Objeto JSON | Quaisquer cabeçalhos que você precisa incluir com o pedido <p>Por exemplo, para definir a língua e o tipo: <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` |
| `queries` | <*parâmetros de consulta*> | Objeto JSON | Quaisquer parâmetros de consulta que você precisa usar no pedido <p>Por exemplo, o `"queries": { "api-version": "2018-01-01" }` objeto adiciona `?api-version=2018-01-01` ao pedido. |
| `body` | <*conteúdo corporal*> | Objeto JSON | O conteúdo da mensagem a enviar como carga útil com o pedido |
| `authentication` | <*valores de autenticação tipo e propriedade*> | Objeto JSON | O modelo de autenticação que o pedido utiliza para autenticar pedidos de saída. Para obter mais informações, consulte [Adicionar autenticação às chamadas de saída](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound). Além do Scheduler, a `authority` propriedade é suportada. Quando não especificado, o valor predefinido é `https://management.azure.com/` , mas pode utilizar um valor diferente. |
| `retryPolicy` > `type` | <*recandidutar comportamento*> | Objeto JSON | Personaliza o comportamento de repetição para falhas intermitentes, que têm o código de estado 408, 429 e 5XX, e quaisquer exceções de conectividade. Para obter mais informações, consulte [as políticas de Retry](../logic-apps/logic-apps-exception-handling.md#retry-policies). |
| `runs` | <*max-runs*> | Número inteiro | Por predefinição, as instâncias de fluxo de trabalho são executadas ao mesmo tempo (simultaneamente ou paralelamente) até ao [limite por defeito](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Para alterar este limite, definindo uma nova *contagem* de <> valor, consulte [Change trigger concurrency](#change-trigger-concurrency). |
| `maximumWaitingRuns` | <*max-runs-fila*> | Número inteiro | Quando o seu fluxo de trabalho já está a executar o número máximo de casos, que pode alterar com base na `runtimeConfiguration.concurrency.runs` propriedade, quaisquer novas correções são colocadas nesta fila até ao [limite predefinido](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Para alterar o limite de predefinição, consulte [o limite de execuções de espera change](#change-waiting-runs). |
| `operationOptions` | <*opção de operação*> | String | Pode alterar o comportamento padrão definindo a `operationOptions` propriedade. Para mais informações, consulte [as opções de Operação.](#operation-options) |
|||||

*Saídas*

| Elemento | Tipo | Description |
|---------|------|-------------|
| `headers` | Objeto JSON | Os cabeçalhos da resposta |
| `body` | Objeto JSON | O corpo da resposta |
| `status code` | Número inteiro | O código de estado da resposta |
||||

*Requisitos para pedidos de entrada*

Para funcionar bem com a sua aplicação lógica, o ponto final deve estar em conformidade com um padrão ou contrato específico de gatilho, e reconhecer estas propriedades de resposta:

| Propriedade | Necessário | Descrição |
|----------|----------|-------------|
| Código de estado | Yes | O código de estado "200 OK" começa a funcionar. Qualquer outro código de estado não começa a funcionar. |
| Cabeçalho de retíria | No | O número de segundos até que a sua aplicação lógica volte a sondar o ponto final |
| Cabeçalho de localização | No | A URL para ligar no próximo intervalo de votação. Se não for especificado, o URL original é utilizado. |
|||| 

*Exemplo de comportamentos para diferentes pedidos*

| Código de estado | Redaça depois | Comportamento | 
|-------------|-------------|----------|
| 200 | {nenhum} | Executar o fluxo de trabalho e, em seguida, verificar novamente se há mais dados após a recorrência definida. | 
| 200 | 10 segundos | Faça o fluxo de trabalho e, em seguida, verifique novamente se há mais dados após 10 segundos. |  
| 202 | 60 segundos | Não desencadeie o fluxo de trabalho. A próxima tentativa acontece num minuto, sujeito à recorrência definida. Se a recorrência definida for inferior a um minuto, o cabeçalho re-tentar tem precedência. Caso contrário, utiliza-se a recorrência definida. | 
| 400 | {nenhum} | Mau pedido, não faça o fluxo de trabalho. Se não `retryPolicy` for definido, então a política por defeito é utilizada. Após o número de retreições ter sido atingido, o gatilho verifica novamente os dados após a recorrência definida. | 
| 500 | {nenhum}| Erro do servidor, não execute o fluxo de trabalho. Se não `retryPolicy` for definido, então a política por defeito é utilizada. Após o número de retreições ter sido atingido, o gatilho verifica novamente os dados após a recorrência definida. | 
|||| 

<a name="http-webhook-trigger"></a>

### <a name="httpwebhook-trigger"></a>Disparador HTTPWebhook  

Este gatilho torna a sua aplicação lógica callable criando um ponto final que pode registar uma subscrição chamando o URL de ponto final especificado. Quando cria este gatilho no seu fluxo de trabalho, um pedido de saída faz a chamada para registar a subscrição. Assim, o gatilho pode começar a ouvir os acontecimentos. Quando uma operação torna este gatilho inválido, um pedido de saída faz automaticamente a chamada para cancelar a subscrição. Para mais informações, consulte [as assinaturas Endpoint](#subscribe-unsubscribe).

Também pode especificar [limites assíncronos](#asynchronous-limits) num gatilho **HTTPWebhook.** O comportamento do gatilho depende das secções que usa ou omite.

```json
"HTTP_Webhook": {
   "type": "HttpWebhook",
   "inputs": {
      "subscribe": {
         "method": "<method-type>",
         "uri": "<endpoint-subscribe-URL>",
         "headers": { "<header-content>" },
         "body": "<body-content>",
         "authentication": { "<authentication-type>" },
         "retryPolicy": { "<retry-behavior>" }
         },
      },
      "unsubscribe": {
         "method": "<method-type>",
         "url": "<endpoint-unsubscribe-URL>",
         "headers": { "<header-content>" },
         "body": "<body-content>",
         "authentication": { "<authentication-type>" }
      }
   },
   "runTimeConfiguration": {
      "concurrency": {
         "runs": <max-runs>,
         "maximumWaitingRuns": <max-runs-queue>
      }
   },
   "operationOptions": "<operation-option>"
}
```

Alguns valores, como <> *do tipo método,* estão disponíveis tanto para os objetos como para `"subscribe"` os `"unsubscribe"` objetos.

*Obrigatório*

| Valor | Tipo | Description | 
|-------|------|-------------| 
| <*tipo método*> | String | O método HTTP a utilizar para o pedido de subscrição: "GET", "PUT", "POST", "PATCH" ou "DELETE" | 
| <*endpoint-subscreva-URL*> | String | O URL de ponto final para onde enviar o pedido de subscrição | 
|||| 

*Opcional*

| Valor | Tipo | Description | 
|-------|------|-------------| 
| <*tipo método*> | String | O método HTTP a utilizar para o pedido de cancelamento: "GET", "PUT", "POST", "PATCH" ou "DELETE" | 
| <*endpoint-unubscribe-URL*> | String | O URL de ponto final para onde enviar o pedido de cancelamento | 
| <*conteúdo corporal*> | String | Qualquer conteúdo de mensagem para enviar no pedido de subscrição ou cancelamento | 
| <*tipo de autenticação*> | Objeto JSON | O modelo de autenticação que o pedido utiliza para autenticar pedidos de saída. Para obter mais informações, consulte [Adicionar autenticação às chamadas de saída](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound). |
| <*recandidutar comportamento*> | Objeto JSON | Personaliza o comportamento de repetição para falhas intermitentes, que têm o código de estado 408, 429 e 5XX, e quaisquer exceções de conectividade. Para obter mais informações, consulte [as políticas de Retry](../logic-apps/logic-apps-exception-handling.md#retry-policies). | 
| <*max-runs*> | Número inteiro | Por predefinição, as instâncias de fluxo de trabalho são todas executadas ao mesmo tempo (simultaneamente ou paralelamente) até ao [limite por defeito](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Para alterar este limite, definindo uma nova *contagem* de <> valor, consulte [Change trigger concurrency](#change-trigger-concurrency). | 
| <*max-runs-fila*> | Número inteiro | Quando o seu fluxo de trabalho já está a executar o número máximo de casos, que pode alterar com base na `runtimeConfiguration.concurrency.runs` propriedade, quaisquer novas correções são colocadas nesta fila até ao [limite predefinido](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Para alterar o limite de predefinição, consulte [o limite de execuções de espera change](#change-waiting-runs). | 
| <*opção de operação*> | String | Pode alterar o comportamento padrão definindo a `operationOptions` propriedade. Para mais informações, consulte [as opções de Operação.](#operation-options) | 
|||| 

*Saídas* 

| Elemento | Tipo | Description |
|---------|------|-------------| 
| cabeçalhos | Objeto JSON | Os cabeçalhos da resposta | 
| body | Objeto JSON | O corpo da resposta | 
| código de estado | Número inteiro | O código de estado da resposta | 
|||| 

*Exemplo*

Este gatilho cria uma subscrição do ponto final especificado, fornece um URL de retorno único e aguarda artigos de tecnologia recém-publicados.

```json
"HTTP_Webhook": {
   "type": "HttpWebhook",
   "inputs": {
      "subscribe": {
         "method": "POST",
         "uri": "https://pubsubhubbub.appspot.com/subscribe",
         "body": {
            "hub.callback": "@{listCallbackUrl()}",
            "hub.mode": "subscribe",
            "hub.topic": "https://pubsubhubbub.appspot.com/articleCategories/technology"
         },
      },
      "unsubscribe": {
         "method": "POST",
         "url": "https://pubsubhubbub.appspot.com/subscribe",
         "body": {
            "hub.callback": "@{workflow().endpoint}@{listCallbackUrl()}",
            "hub.mode": "unsubscribe",
            "hub.topic": "https://pubsubhubbub.appspot.com/articleCategories/technology"
         }
      }
   }
}
```

<a name="recurrence-trigger"></a>

### <a name="recurrence-trigger"></a>Gatilho de recorrência  

Este gatilho é executado com base no calendário de recorrência especificado e proporciona uma maneira fácil de criar um fluxo de trabalho regularmente em execução.

```json
"Recurrence": {
   "type": "Recurrence",
   "recurrence": {
      "frequency": "<time-unit>",
      "interval": <number-of-time-units>,
      "startTime": "<start-date-time-with-format-YYYY-MM-DDThh:mm:ss>",
      "timeZone": "<time-zone>",
      "schedule": {
         // Applies only when frequency is Day or Week. Separate values with commas.
         "hours": [ <one-or-more-hour-marks> ], 
         // Applies only when frequency is Day or Week. Separate values with commas.
         "minutes": [ <one-or-more-minute-marks> ], 
         // Applies only when frequency is Week. Separate values with commas.
         "weekDays": [ "Monday, Tuesday, Wednesday, Thursday, Friday, Saturday, Sunday" ] 
      }
   },
   "runtimeConfiguration": {
      "concurrency": {
         "runs": <max-runs>,
         "maximumWaitingRuns": <max-runs-queue>
      }
   },
   "operationOptions": "<operation-option>"
}
```

*Obrigatório*

| Valor | Tipo | Description | 
|-------|------|-------------| 
| <*unidade de tempo*> | String | A unidade de tempo que descreve com que frequência o gatilho dispara: "Segundo", "Minuto", "Hora", "Dia", "Semana", "Mês" | 
| <*número de tempo-unidades*> | Número inteiro | Um valor que especifica quantas vezes o gatilho dispara com base na frequência, que é o número de unidades de tempo para esperar até que o gatilho volte a disparar. <p>Aqui estão os intervalos mínimos e máximos: <p>- Mês: 1-16 meses </br>- Dia: 1-500 dias </br>- Hora: 1-12.000 horas </br>- Minuto: 1-72.000 minutos </br>- Segundo: 1-9.999,999 segundos<p>Por exemplo, se o intervalo for 6, e a frequência for "Mês", a recorrência é a cada 6 meses. | 
|||| 

*Opcional*

| Valor | Tipo | Description | 
|-------|------|-------------| 
| <*data-início-hora-com-formato-YYYY-MM-DDThh:mm:mm:ss*> | String | A data e hora de início neste formato: <p>YYYY-MM-DDThh:mm:mm se especificar um fuso horário <p>-ou- <p>YYYY-MM-DDThh:mm:ssZ se não especificar um fuso horário <p>Assim, por exemplo, se quiser 18 de setembro de 2017 às 14:00, então especifique "2017-09-18T14:00:00" e especifique um fuso horário como "Hora Padrão do Pacífico", ou especifique "2017-09-18T14:00:00Z" sem fuso horário. <p>**Nota:** Este horário de início tem um máximo de 49 anos no futuro e deve seguir a [especificação de data ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations) no [formato de data UTC,](https://en.wikipedia.org/wiki/Coordinated_Universal_Time)mas sem [compensação UTC](https://en.wikipedia.org/wiki/UTC_offset). Se não especificar um fuso horário, deve adicionar a letra "Z" no final sem espaços. Este "Z" refere-se ao [tempo náutico](https://en.wikipedia.org/wiki/Nautical_time)equivalente. <p>Para horários simples, a hora de início é a primeira ocorrência, enquanto para horários complexos, o gatilho não dispara tão cedo quanto a hora de início. Para obter mais informações sobre datas e horários de início, consulte [Criar e programar tarefas regulares de execução](../connectors/connectors-native-recurrence.md). | 
| <*fuso horário*> | String | Aplica-se apenas quando especifica uma hora de início porque este gatilho não aceita [a compensação UTC](https://en.wikipedia.org/wiki/UTC_offset). Especifique o fuso horário que pretende aplicar. | 
| <*uma ou mais horas de marca*> | Conjunto inteiro ou inteiro | Se especificar "Dia" ou "Semana" para `frequency` , pode especificar um ou mais inteiros de 0 a 23, separados por vírgulas, como as horas do dia em que pretende executar o fluxo de trabalho. <p>Por exemplo, se especificar "10", "12" e "14", obtém 10:00, 12:00 e 14:00 como as marcas de horas. | 
| <*marcas de um ou mais minutos*> | Conjunto inteiro ou inteiro | Se especificar "Dia" ou "Semana" para `frequency` , pode especificar um ou mais inteiros de 0 a 59, separados por vírgulas, como os minutos da hora em que pretende executar o fluxo de trabalho. <p>Por exemplo, pode especificar "30" como a marca de minutos e usando o exemplo anterior para horas do dia, obtém 10:30 AM, 12:30 pm e 14:30. | 
| weekDays | Matriz de cordas ou cordas | Se especificar "Semana" `frequency` para, pode especificar um ou mais dias, separados por vírgulas, quando pretender executar o fluxo de trabalho: "Segunda", "terça", "quarta", "quinta", "sexta", "sábado" e "domingo" | 
| <*max-runs*> | Número inteiro | Por predefinição, as instâncias de fluxo de trabalho são todas executadas ao mesmo tempo (simultaneamente ou paralelamente) até ao [limite por defeito](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Para alterar este limite, definindo uma nova *contagem* de <> valor, consulte [Change trigger concurrency](#change-trigger-concurrency). | 
| <*max-runs-fila*> | Número inteiro | Quando o seu fluxo de trabalho já está a executar o número máximo de casos, que pode alterar com base na `runtimeConfiguration.concurrency.runs` propriedade, quaisquer novas correções são colocadas nesta fila até ao [limite predefinido](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Para alterar o limite de predefinição, consulte [o limite de execuções de espera change](#change-waiting-runs). | 
| <*opção de operação*> | String | Pode alterar o comportamento padrão definindo a `operationOptions` propriedade. Para mais informações, consulte [as opções de Operação.](#operation-options) | 
|||| 

*Exemplo 1*

Este gatilho básico de recorrência corre diariamente:

```json
"Recurrence": {
   "type": "Recurrence",
   "recurrence": {
      "frequency": "Day",
      "interval": 1
   }
}
```

*Exemplo 2*

Pode especificar uma data de início e hora para disparar o gatilho. Este gatilho de recorrência começa na data especificada e dispara diariamente:

```json
"Recurrence": {
   "type": "Recurrence",
   "recurrence": {
      "frequency": "Day",
      "interval": 1,
      "startTime": "2017-09-18T00:00:00Z"
   }
}
```

*Exemplo 3*

Este gatilho de recorrência começa no dia 9 de setembro de 2017 às 14:00, e dispara semanalmente todas as segundas-feiras às 10:30, 12:30 e 14:30 Hora Padrão do Pacífico:

``` json
"Recurrence": {
   "type": "Recurrence",
   "recurrence": {
      "frequency": "Week",
      "interval": 1,
      "schedule": {
         "hours": [ 10, 12, 14 ],
         "minutes": [ 30 ],
         "weekDays": [ "Monday" ]
      },
      "startTime": "2017-09-07T14:00:00",
      "timeZone": "Pacific Standard Time"
   }
}
```

Para obter mais informações mais exemplos para este gatilho, consulte [Criar e programar tarefas regulares de execução](../connectors/connectors-native-recurrence.md).

<a name="request-trigger"></a>

### <a name="request-trigger"></a>Pedido de gatilho

Este gatilho torna a sua aplicação lógica callable criando um ponto final que pode aceitar pedidos de entrada. Para este gatilho, forneça um esquema JSON que descreva e valida a carga útil ou as entradas que o gatilho recebe do pedido de entrada. O esquema também facilita a referência das propriedades do gatilho a partir de ações posteriores no fluxo de trabalho.

Para chamar este gatilho, deve utilizar a `listCallbackUrl` API, que está descrita no [Serviço de Fluxo de Trabalho REST API](/rest/api/logic/workflows). Para aprender a usar este gatilho como ponto final HTTP, consulte [call, trigger ou nestflows com pontos finais HTTP](../logic-apps/logic-apps-http-endpoint.md).

```json
"manual": {
   "type": "Request",
   "kind": "Http",
   "inputs": {
      "method": "<method-type>",
      "relativePath": "<relative-path-for-accepted-parameter>",
      "schema": {
         "type": "object",
         "properties": { 
            "<property-name>": {
               "type": "<property-type>"
            }
         },
         "required": [ "<required-properties>" ]
      }
   },
   "runTimeConfiguration": {
      "concurrency": {
         "runs": <max-runs>,
         "maximumWaitingRuns": <max-run-queue>
      },
   },
   "operationOptions": "<operation-option>"
}
```

*Obrigatório*

| Valor | Tipo | Description | 
|-------|------|-------------| 
| <*nome da propriedade*> | String | O nome de uma propriedade no esquema JSON, que descreve a carga útil | 
| <*tipo de propriedade*> | String | O tipo da propriedade | 
|||| 

*Opcional*

| Valor | Tipo | Description | 
|-------|------|-------------| 
| <*tipo método*> | String | O método que os pedidos de entrada devem usar para chamar a sua app lógica: "GET", "PUT", "POST", "PATCH", "DELETE" |
| <*relativa-caminho-para-aceito-parâmetro*> | String | O caminho relativo para o parâmetro que o URL do seu ponto final pode aceitar | 
| <*propriedades necessárias*> | Matriz | Uma ou mais propriedades que requerem valores | 
| <*max-runs*> | Número inteiro | Por predefinição, as instâncias de fluxo de trabalho são todas executadas ao mesmo tempo (simultaneamente ou paralelamente) até ao [limite por defeito](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Para alterar este limite, definindo uma nova *contagem* de <> valor, consulte [Change trigger concurrency](#change-trigger-concurrency). | 
| <*max-runs-fila*> | Número inteiro | Quando o seu fluxo de trabalho já está a executar o número máximo de casos, que pode alterar com base na `runtimeConfiguration.concurrency.runs` propriedade, quaisquer novas correções são colocadas nesta fila até ao [limite predefinido](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Para alterar o limite de predefinição, consulte [o limite de execuções de espera change](#change-waiting-runs). | 
| <*opção de operação*> | String | Pode alterar o comportamento padrão definindo a `operationOptions` propriedade. Para mais informações, consulte [as opções de Operação.](#operation-options) | 
|||| 

*Exemplo*

Este gatilho especifica que um pedido de entrada deve utilizar o método HTTP POST para ligar para o gatilho e inclui um esquema que valida a entrada do pedido de entrada:

```json
"manual": {
   "type": "Request",
   "kind": "Http",
   "inputs": {
      "method": "POST",
      "schema": {
         "type": "object",
         "properties": {
            "customerName": {
               "type": "String"
            },
            "customerAddress": { 
               "type": "Object",
               "properties": {
                  "streetAddress": {
                     "type": "string"
                  },
                  "city": {
                     "type": "string"
                  }
               }
            }
         }
      }
   }
}
```

<a name="trigger-conditions"></a>

## <a name="trigger-conditions"></a>Condições do acionador

Para qualquer gatilho, e apenas gatilhos, pode incluir uma matriz que contém uma ou mais expressões para condições que determinam se o fluxo de trabalho deve ser executado. Para adicionar a `conditions` propriedade a um gatilho no seu fluxo de trabalho, abra a sua aplicação lógica no editor de visualização de código.

Por exemplo, pode especificar que um gatilho dispara apenas quando um site retorna um erro interno do servidor, referindo o código de estado do gatilho na `conditions` propriedade:

```json
"Recurrence": {
   "type": "Recurrence",
   "recurrence": {
      "frequency": "Hour",
      "interval": 1
   },
   "conditions": [ {
      "expression": "@equals(triggers().code, 'InternalServerError')"
   } ]
}
```

Por defeito, um gatilho só dispara depois de obter uma resposta "200 OK". Quando uma expressão referencia o código de estado do gatilho, o comportamento padrão do gatilho é substituído. Por isso, se pretender que o gatilho dispare por mais do que um código de estado, como o código de estado "200" e "201", deve incluir esta expressão como a sua condição:

`@or(equals(triggers().code, 200),equals(triggers().code, 201))` 

<a name="split-on-debatch"></a>

## <a name="trigger-multiple-runs"></a>Várias execuções do acionador

Se o seu gatilho devolver um conjunto para a sua aplicação lógica processar, por vezes um laço "para cada um" pode demorar muito tempo a processar cada item de matriz. Em vez disso, pode utilizar a propriedade **SplitOn** no seu gatilho para *debascação* da matriz. A debacação divide os itens de matriz e inicia uma nova instância de fluxo de trabalho que funciona para cada item de matriz. Esta abordagem é útil, por exemplo, quando se pretende sondar um ponto final que pode devolver vários novos itens entre intervalos de votação. Para o número máximo de itens de matriz que **o SplitOn** pode processar numa única aplicação lógica, consulte [Limites e configuração](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). 

> [!NOTE]
> Não podes usar **o SplitOn** com um padrão de resposta sincronizado. Qualquer fluxo de trabalho que utilize **o SplitOn** e inclua uma ação de resposta é assíncroticamente e envia imediatamente uma `202 ACCEPTED` resposta.
>
> Quando a concordância do gatilho é ativada, o [limite SplitOn](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits) é significativamente reduzido. Se o número de itens exceder este limite, a capacidade SplitOn é desativada.
 
Se o ficheiro Swagger do seu gatilho descrever uma carga útil que é uma matriz, a propriedade **SplitOn** é automaticamente adicionada ao seu gatilho. Caso contrário, adicione esta propriedade dentro da carga útil de resposta que tem a matriz que pretende debatch.

*Exemplo*

Suponha que tenha uma API que devolva esta resposta: 
  
```json
{
   "Status": "Succeeded",
   "Rows": [ 
      { 
         "id": 938109380,
         "name": "customer-name-one"
      },
      {
         "id": 938109381,
         "name": "customer-name-two"
      }
   ]
}
```

A sua aplicação lógica só necessita do conteúdo da matriz em `Rows` , para que possa criar um gatilho como este exemplo:

``` json
"HTTP_Debatch": {
   "type": "Http",
    "inputs": {
        "uri": "https://mydomain.com/myAPI",
        "method": "GET"
    },
   "recurrence": {
      "frequency": "Second",
      "interval": 1
    },
    "splitOn": "@triggerBody()?.Rows"
}
```

> [!NOTE]
> Se usar o `SplitOn` comando, não pode obter as propriedades que estão fora da matriz. Assim, para este exemplo, você não pode obter a `status` propriedade na resposta devolvida da API.
> 
> Para evitar uma falha se a `Rows` propriedade não existir, este exemplo utiliza o `?` operador.

A sua definição de fluxo de trabalho pode agora usar `@triggerBody().name` para obter os `name` valores, que são `"customer-name-one"` da primeira e da segunda `"customer-name-two"` execução. Então, as suas saídas de gatilho parecem estes exemplos:

```json
{
   "body": {
      "id": 938109380,
      "name": "customer-name-one"
   }
}
```

```json
{
   "body": {
      "id": 938109381,
      "name": "customer-name-two"
   }
}
```

<a name="actions-overview"></a>

## <a name="actions-overview"></a>Descrição geral de ações

A Azure Logic Apps fornece vários tipos de ação - cada um com diferentes entradas que definem o comportamento único de uma ação. As ações têm estes elementos de alto nível, embora alguns sejam opcionais:

```json
"<action-name>": {
   "type": "<action-type>",
   "inputs": { 
      "<input-name>": { "<input-value>" },
      "retryPolicy": "<retry-behavior>" 
   },
   "runAfter": { "<previous-trigger-or-action-status>" },
   "runtimeConfiguration": { "<runtime-config-options>" },
   "operationOptions": "<operation-option>"
},
```

*Obrigatório*

| Valor | Tipo | Description | 
|-------|------|-------------|
| <*nome de ação*> | String | O nome para a ação | 
| <*tipo de ação*> | String | O tipo de ação, por exemplo, "Http" ou "ApiConnection"| 
| <*nome de entrada*> | String | O nome para uma entrada que define o comportamento da ação | 
| <*valor de entrada*> | Vários | O valor de entrada, que pode ser uma corda, inteiro, objeto JSON, e assim por diante | 
| <*anterior-gatilho ou-ação-status*> | Objeto JSON | O nome e o estado resultante para o gatilho ou ação que deve ser executado imediatamente antes que esta ação atual possa ser executada | 
|||| 

*Opcional*

| Valor | Tipo | Description | 
|-------|------|-------------|
| <*recandidutar comportamento*> | Objeto JSON | Personaliza o comportamento de repetição para falhas intermitentes, que têm o código de estado 408, 429 e 5XX, e quaisquer exceções de conectividade. Para mais informações, consulte as políticas de Retry. | 
| <*runtime-config-opções*> | Objeto JSON | Para algumas ações, pode alterar o comportamento da ação no tempo de execução, definindo `runtimeConfiguration` propriedades. Para obter mais informações, consulte [as definições de configuração do tempo de execução](#runtime-config-options). | 
| <*opção de operação*> | String | Para algumas ações, pode alterar o comportamento padrão definindo a `operationOptions` propriedade. Para mais informações, consulte [as opções de Operação.](#operation-options) | 
|||| 

## <a name="action-types-list"></a>Lista de tipos de ação

Aqui estão alguns tipos de ação comumente usados: 

* [Tipos de ação incorporados,](#built-in-actions) como estes exemplos e muito mais: 

  * [**HTTP**](#http-action) para chamar pontos finais em HTTP ou HTTPS

  * [**Resposta**](#response-action) para responder a pedidos

  * [**Execute o Código JavaScript**](#run-javascript-code) para executar snippets de código JavaScript

  * [**Função**](#function-action) para chamar funções de Azure

  * Ações de operação de dados como [**Join**](#join-action), [**Compose**](#compose-action), [**Table**](#table-action), [**Select**](#select-action), e outras que criam ou transformam dados a partir de várias entradas

  * [**Fluxo de trabalho**](#workflow-action) para chamar outro fluxo de trabalho de aplicações lógicas

* [Tipos de ação geridos da API,](#managed-api-actions) tais como [**ApiConnection**](#apiconnection-action) e [**ApiConnectionWebHook**](#apiconnectionwebhook-action) que ligam para vários conectores e APIs geridos pela Microsoft, por exemplo, Azure Service Bus, Office 365 Outlook, Power BI, Azure Blob Storage, OneDrive, GitHub, entre outros

* [Controle tipos de ação de fluxo de trabalho](#control-workflow-actions) tais como [**If**](#if-action), [**Foreach**](#foreach-action), [**Switch**](#switch-action), [**Scope**](#scope-action), e [**Until**](#until-action), que contêm outras ações e ajudá-lo a organizar a execução do fluxo de trabalho

<a name="built-in-actions"></a>

### <a name="built-in-actions"></a>Ações incorporadas

| Tipo de ação | Description | 
|-------------|-------------| 
| [**Compose**](#compose-action) | Cria uma única saída a partir de entradas, que pode ter vários tipos. | 
| [**Executar código JavaScript**](#run-javascript-code) | Executar os snippets de código JavaScript que se enquadram dentro de critérios específicos. Para obter requisitos de código e mais informações, consulte [Adicionar e executar os snippets de código com código inline](../logic-apps/logic-apps-add-run-inline-code.md). |
| [**Função**](#function-action) | Chama uma Função Azure. | 
| [**HTTP**](#http-action) | Chama um ponto final HTTP. | 
| [**Participar**](#join-action) | Cria uma cadeia a partir de todos os itens de uma matriz e separa esses itens com um carácter delimiter especificado. | 
| [**Parse JSON**](#parse-json-action) | Cria fichas fáceis de usar a partir de propriedades em conteúdo JSON. Em seguida, pode referenciar essas propriedades incluindo os tokens na sua aplicação lógica. | 
| [**Consulta**](#query-action) | Cria uma matriz a partir de itens em outra matriz com base numa condição ou filtro. | 
| [**Resposta**](#response-action) | Cria uma resposta a uma chamada ou pedido de entrada. | 
| [**Selecione**](#select-action) | Cria uma matriz com objetos JSON transformando itens de outra matriz com base no mapa especificado. | 
| [**Tabela**](#table-action) | Cria uma tabela CSV ou HTML a partir de uma matriz. | 
| [**Terminate**](#terminate-action) | Impede um fluxo de trabalho ativamente em funcionamento. | 
| [**Wait**](#wait-action) | Pausa o seu fluxo de trabalho durante uma duração especificada ou até à data e hora especificadas. | 
| [**Fluxo de trabalho**](#workflow-action) | Aninha um fluxo de trabalho dentro de outro fluxo de trabalho. | 
||| 

<a name="managed-api-actions"></a>

### <a name="managed-api-actions"></a>Ações de API geridas

| Tipo de ação | Description | 
|-------------|-------------|  
| [**ApiConnection**](#apiconnection-action) | Chama um ponto final HTTP utilizando uma [API gerida](../connectors/apis-list.md)pela Microsoft. | 
| [**ApiConnectionWebhook**](#apiconnectionwebhook-action) | Funciona como HTTP Webhook mas utiliza uma [API gerida pela Microsoft.](../connectors/apis-list.md) | 
||| 

<a name="control-workflow-actions"></a>

### <a name="control-workflow-actions"></a>Controlar as ações de fluxo de trabalho

Estas ações ajudam-no a controlar a execução do fluxo de trabalho e incluem outras ações. Do lado de fora de uma ação de fluxo de trabalho de controlo, pode referenciar diretamente ações dentro dessa ação de fluxo de trabalho de controlo. Por exemplo, se tiver uma `Http` ação dentro de um âmbito, pode referenciar a expressão de qualquer lugar do fluxo de `@body('Http')` trabalho. No entanto, as ações que existem dentro de uma ação de fluxo de trabalho de controlo só podem "correr atrás" de outras ações que se encontrem na mesma estrutura de fluxo de trabalho de controlo.

| Tipo de ação | Description | 
|-------------|-------------| 
| [**Foreach**](#foreach-action) | Executar as mesmas ações em loop para cada item de uma matriz. | 
| [**Se**](#if-action) | Executar ações com base no facto de a condição especificada ser verdadeira ou falsa. | 
| [**Âmbito**](#scope-action) | Executar ações com base no estado de grupo de um conjunto de ações. | 
| [**Comutador**](#switch-action) | Executar ações organizadas em casos em que valores de expressões, objetos ou fichas correspondam aos valores especificados por cada caso. | 
| [**Até que**](#until-action) | Executar as ações em loop até que a condição especificada seja verdadeira. | 
|||  

## <a name="actions---detailed-reference"></a>Ações - Referência detalhada

<a name="apiconnection-action"></a>

### <a name="apiconnection-action"></a>Ação APIConnection

Esta ação envia um pedido HTTP a uma [API gerida pela Microsoft](../connectors/apis-list.md) e requer informações sobre a API e parâmetros mais uma referência a uma ligação válida. 

``` json
"<action-name>": {
   "type": "ApiConnection",
   "inputs": {
      "host": {
         "connection": {
            "name": "@parameters('$connections')['<api-name>']['connectionId']"
         },
         "<other-action-specific-input-properties>"        
      },
      "method": "<method-type>",
      "path": "/<api-operation>",
      "retryPolicy": "<retry-behavior>",
      "queries": { "<query-parameters>" },
      "<other-action-specific-properties>"
    },
    "runAfter": {}
}
```

*Obrigatório*

| Valor | Tipo | Description | 
|-------|------|-------------| 
| <*nome de ação*> | String | O nome da ação fornecida pelo conector | 
| <*nome api*> | String | O nome da API gerida pela Microsoft que é usada para a ligação | 
| <*tipo método*> | String | O método HTTP para chamar a API: "GET", "PUT", "POST", "PATCH" ou "DELETE" | 
| <*api-operação*> | String | A operação da API para ligar | 
|||| 

*Opcional*

| Valor | Tipo | Description | 
|-------|------|-------------| 
| <*outras propriedades de entrada específicas de ação*> | Objeto JSON | Quaisquer outras propriedades de entrada que se apliquem a esta ação específica | 
| <*recandidutar comportamento*> | Objeto JSON | Personaliza o comportamento de repetição para falhas intermitentes, que têm o código de estado 408, 429 e 5XX, e quaisquer exceções de conectividade. Para obter mais informações, consulte [as políticas de Retry](../logic-apps/logic-apps-exception-handling.md#retry-policies). | 
| <*parâmetros de consulta*> | Objeto JSON | Quaisquer parâmetros de consulta para incluir com a chamada API. <p>Por exemplo, o `"queries": { "api-version": "2018-01-01" }` objeto adiciona `?api-version=2018-01-01` à chamada. | 
| <*outras propriedades específicas de ação*> | Objeto JSON | Quaisquer outras propriedades que se apliquem a esta ação específica | 
|||| 

*Exemplo*

Esta definição descreve o Envio de uma ação **de e-mail** para o conector Do Office 365 Outlook, que é uma API gerida pela Microsoft: 

```json
"Send_an_email": {
   "type": "ApiConnection",
   "inputs": {
      "body": {
         "Body": "Thank you for your membership!",
         "Subject": "Hello and welcome!",
         "To": "Sophie.Owen@contoso.com"
      },
      "host": {
         "connection": {
            "name": "@parameters('$connections')['office365']['connectionId']"
         }
      },
      "method": "POST",
      "path": "/Mail"
    },
    "runAfter": {}
}
```

<a name="apiconnection-webhook-action"></a>

### <a name="apiconnectionwebhook-action"></a>A APIConnectionWebhook ação

Esta ação envia um pedido de subscrição sobre HTTP para um ponto final utilizando uma [API gerida](../connectors/apis-list.md)pela Microsoft, fornece um *URL de retorno* para onde o ponto final pode enviar uma resposta, e aguarda que o ponto final responda. Para mais informações, consulte [as assinaturas Endpoint](#subscribe-unsubscribe).

```json
"<action-name>": {
   "type": "ApiConnectionWebhook",
   "inputs": {
      "subscribe": {
         "method": "<method-type>",
         "uri": "<api-subscribe-URL>",
         "headers": { "<header-content>" },
         "body": "<body-content>",
         "authentication": { "<authentication-type>" },
         "retryPolicy": "<retry-behavior>",
         "queries": { "<query-parameters>" },
         "<other-action-specific-input-properties>"
      },
      "unsubscribe": {
         "method": "<method-type>",
         "uri": "<api-unsubscribe-URL>",
         "headers": { "<header-content>" },
         "body": "<body-content>",
         "authentication": { "<authentication-type>" },
         "<other-action-specific-properties>"
      },
   },
   "runAfter": {}
}
```

Alguns valores, como <> *do tipo método,* estão disponíveis tanto para os objetos como para `"subscribe"` os `"unsubscribe"` objetos.

*Obrigatório*

| Valor | Tipo | Description | 
|-------|------|-------------| 
| <*nome de ação*> | String | O nome da ação fornecida pelo conector | 
| <*tipo método*> | String | O método HTTP a utilizar para subscrever ou não subscrever a partir de um ponto final: "GET", "PUT", "POST", "PATCH" ou "DELETE" | 
| <*api-subscrever-URL*> | String | O URI para usar para subscrever a API | 
|||| 

*Opcional*

| Valor | Tipo | Description | 
|-------|------|-------------| 
| <*api-desubscreva-URL*> | String | O URI para usar para não subscrever a API | 
| <*cabeçalho-conteúdo*> | Objeto JSON | Quaisquer cabeçalhos para enviar o pedido <p>Por exemplo, para definir a língua e o tipo num pedido: <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` |
| <*conteúdo corporal*> | Objeto JSON | Qualquer conteúdo de mensagem para enviar o pedido | 
| <*tipo de autenticação*> | Objeto JSON | O modelo de autenticação que o pedido utiliza para autenticar pedidos de saída. Para obter mais informações, consulte [Adicionar autenticação às chamadas de saída](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound). |
| <*recandidutar comportamento*> | Objeto JSON | Personaliza o comportamento de repetição para falhas intermitentes, que têm o código de estado 408, 429 e 5XX, e quaisquer exceções de conectividade. Para obter mais informações, consulte [as políticas de Retry](../logic-apps/logic-apps-exception-handling.md#retry-policies). | 
| <*parâmetros de consulta*> | Objeto JSON | Quaisquer parâmetros de consulta para incluir com a chamada API <p>Por exemplo, o `"queries": { "api-version": "2018-01-01" }` objeto adiciona `?api-version=2018-01-01` à chamada. | 
| <*outras propriedades de entrada específicas de ação*> | Objeto JSON | Quaisquer outras propriedades de entrada que se apliquem a esta ação específica | 
| <*outras propriedades específicas de ação*> | Objeto JSON | Quaisquer outras propriedades que se apliquem a esta ação específica | 
|||| 

Também pode especificar limites para uma ação **ApiConnectionWebhook** da mesma forma que [os limites http assíncronos](#asynchronous-limits).

<a name="compose-action"></a>

### <a name="compose-action"></a>Composição

Esta ação cria uma única saída a partir de múltiplas entradas, incluindo expressões. Tanto a saída como as entradas podem ter qualquer tipo que a Azure Logic Apps suporta de forma nativa, tais como matrizes, objetos JSON, XML e binário. Em seguida, pode utilizar a produção da ação noutras ações. 

```json
"Compose": {
   "type": "Compose",
   "inputs": "<inputs-to-compose>",
   "runAfter": {}
},
```

*Obrigatório* 

| Valor | Tipo | Description | 
|-------|------|-------------| 
| <*entradas-a-compor*> | Qualquer | As entradas para criar uma única saída | 
|||| 

*Exemplo 1*

<!-- markdownlint-disable MD038 -->
Esta definição de ação funde-se `abcdefg ` com um espaço de fuga e o `1234` valor:
<!-- markdownlint-enable MD038 -->

```json
"Compose": {
   "type": "Compose",
   "inputs": "abcdefg 1234",
   "runAfter": {}
},
```

Aqui está a saída que esta ação cria:

`abcdefg 1234`

*Exemplo 2*

Esta definição de ação funde uma variável de cadeia que contém `abcdefg` e uma variável inteiro que `1234` contém:

```json
"Compose": {
   "type": "Compose",
   "inputs": "@{variables('myString')}@{variables('myInteger')}",
   "runAfter": {}
},
```

Aqui está a saída que esta ação cria:

`"abcdefg1234"`

<a name="run-javascript-code"></a>

### <a name="execute-javascript-code-action"></a>Executar ação do Código JavaScript

Esta ação executa um corte de código JavaScript e devolve os resultados através de um `Result` token que as ações posteriores podem referenciar.

```json
"Execute_JavaScript_Code": {
   "type": "JavaScriptCode",
   "inputs": {
      "code": "<JavaScript-code-snippet>",
      "explicitDependencies": {
         "actions": [ <previous-actions> ],
         "includeTrigger": true
      }
   },
   "runAfter": {}
}
```

*Obrigatório*

| Valor | Tipo | Description |
|-------|------|-------------|
| <*JavaScript-código-snippet*> | Varia | O código JavaScript que pretende executar. Para obter requisitos de código e mais informações, consulte [Adicionar e executar os snippets de código com código inline](../logic-apps/logic-apps-add-run-inline-code.md). <p>No `code` atributo, o seu corte de código pode usar o objeto apenas de leitura `workflowContext` como entrada. Este objeto tem subpropriedades que dão ao seu código acesso aos resultados do gatilho e ações anteriores no seu fluxo de trabalho. Para obter mais informações sobre o `workflowContext` objeto, consulte [o gatilho de referência e os resultados de ação no seu código](../logic-apps/logic-apps-add-run-inline-code.md#workflowcontext). |
||||

*Requerida em alguns casos*

O `explicitDependencies` atributo especifica que pretende incluir explicitamente resultados do gatilho, ações anteriores ou ambas como dependências para o seu corte de código. Para obter mais informações sobre a adição destas dependências, consulte [adicionar parâmetros para código inline](../logic-apps/logic-apps-add-run-inline-code.md#add-parameters). 

Para o `includeTrigger` atributo, pode especificar `true` ou `false` valores.

| Valor | Tipo | Description |
|-------|------|-------------|
| <*ações anteriores*> | Matriz de cadeias | Uma matriz com os seus nomes de ação especificados. Utilize os nomes de ação que aparecem na definição de fluxo de trabalho onde os nomes de ação usam sublinhados (_), e não espaços (" "). |
||||

*Exemplo 1*

Esta ação executa código que obtém o nome da sua aplicação lógica e devolve o texto "Olá mundo \<logic-app-name> de" como resultado. Neste exemplo, o código faz referência ao nome do fluxo de trabalho acedendo à `workflowContext.workflow.name` propriedade através do objeto apenas de `workflowContext` leitura. Para obter mais informações sobre a utilização do `workflowContext` objeto, consulte [o gatilho de referência e os resultados de ação no seu código](../logic-apps/logic-apps-add-run-inline-code.md#workflowcontext).

```json
"Execute_JavaScript_Code": {
   "type": "JavaScriptCode",
   "inputs": {
      "code": "var text = \"Hello world from \" + workflowContext.workflow.name;\r\n\r\nreturn text;"
   },
   "runAfter": {}
}
```

*Exemplo 2*

Esta ação executa código numa aplicação lógica que despoleta quando um novo e-mail chega numa conta de trabalho ou escola. A aplicação lógica também usa uma ação de e-mail de envio que reencaminha o conteúdo do e-mail recebido juntamente com um pedido de aprovação.

O código extrai os endereços de e-mail da propriedade do gatilho `Body` e devolve os endereços juntamente com o `SelectedOption` valor da propriedade da ação de aprovação. A ação inclui explicitamente a ação de envio de email como uma dependência no `explicitDependencies`  >  `actions` atributo.

```json
"Execute_JavaScript_Code": {
   "type": "JavaScriptCode",
   "inputs": {
      "code": "var re = /(([^<>()\\[\\]\\\\.,;:\\s@\"]+(\\.[^<>()\\[\\]\\\\.,;:\\s@\"]+)*)|(\".+\"))@((\\[[0-9]{1,3}\\.[0-9]{1,3}\\.[0-9]{1,3}\\.[0-9]{1,3}])|(([a-zA-Z\\-0-9]+\\.)+[a-zA-Z]{2,}))/g;\r\n\r\nvar email = workflowContext.trigger.outputs.body.Body;\r\n\r\nvar reply = workflowContext.actions.Send_approval_email_.outputs.body.SelectedOption;\r\n\r\nreturn email.match(re) + \" - \" + reply;\r\n;",
      "explicitDependencies": {
         "actions": [
            "Send_approval_email_"
         ]
      }
   },
   "runAfter": {}
}
```



<a name="function-action"></a>

### <a name="function-action"></a>Ação de função

Esta ação chama uma [função Azure](../azure-functions/functions-get-started.md)previamente criada.

```json
"<Azure-function-name>": {
   "type": "Function",
   "inputs": {
     "function": {
        "id": "<Azure-function-ID>"
      },
      "method": "<method-type>",
      "headers": { "<header-content>" },
      "body": { "<body-content>" },
      "queries": { "<query-parameters>" } 
   },
   "runAfter": {}
}
```

*Obrigatório*

| Valor | Tipo | Description | 
|-------|------|-------------|  
| <*Azure-function-ID*> | String | O ID de recursos para a função Azure que deseja chamar. Aqui está o formato para este valor:<p>"/subscrições/<*Azure-subscrição-ID*>/resourceGroups/<*Azure-resource-group*>/providers/Microsoft.Web/sites/<*Azure-function-app-name*>/functions/<*Azure-function-name*>" | 
| <*tipo método*> | String | O método HTTP a utilizar para chamar a função: "GET", "PUT", "POST", "PATCH" ou "DELETE" <p>Se não for especificado, o padrão é o método "POST". | 
||||

*Opcional*

| Valor | Tipo | Description | 
|-------|------|-------------|  
| <*cabeçalho-conteúdo*> | Objeto JSON | Quaisquer cabeçalhos para enviar com a chamada <p>Por exemplo, para definir a língua e o tipo num pedido: <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` |
| <*conteúdo corporal*> | Objeto JSON | Qualquer conteúdo de mensagem para enviar o pedido | 
| <*parâmetros de consulta*> | Objeto JSON | Quaisquer parâmetros de consulta para incluir com a chamada API <p>Por exemplo, o `"queries": { "api-version": "2018-01-01" }` objeto adiciona `?api-version=2018-01-01` à chamada. | 
| <*outras propriedades de entrada específicas de ação*> | Objeto JSON | Quaisquer outras propriedades de entrada que se apliquem a esta ação específica | 
| <*outras propriedades específicas de ação*> | Objeto JSON | Quaisquer outras propriedades que se apliquem a esta ação específica | 
||||

Quando guarda a sua aplicação lógica, o motor Logic Apps executa estas verificações na função referenciada:

* O seu fluxo de trabalho deve ter acesso à função.

* O seu fluxo de trabalho pode utilizar apenas um gatilho HTTP padrão ou um gatilho genérico do webhook JSON. 

  O motor Logic Apps recebe e caches o URL do gatilho, que é usado em tempo de execução. No entanto, se qualquer operação invalidar o URL em cache, a ação **Função** falha no tempo de execução. Para corrigir este problema, guarde novamente a aplicação lógica para que a aplicação lógica fique e cache o URL do gatilho novamente.

* A função não pode ter qualquer rota definida.

* Apenas são permitidos níveis de autorização "funções" e "anónimos". 

*Exemplo*

Esta definição de ação chama a função "GetProductID" previamente criada:

```json
"GetProductID": {
   "type": "Function",
   "inputs": {
     "function": {
        "id": "/subscriptions/<XXXXXXXXXXXXXXXXXXXX>/resourceGroups/myLogicAppResourceGroup/providers/Microsoft.Web/sites/InventoryChecker/functions/GetProductID"
      },
      "method": "POST",
      "headers": { 
          "x-ms-date": "@utcnow()"
       },
      "body": { 
          "Product_ID": "@variables('ProductID')"
      }
   },
   "runAfter": {}
}
```

<a name="http-action"></a>

### <a name="http-action"></a>Ação HTTP

Esta ação envia um pedido ao ponto final http ou HTTPS especificado e verifica a resposta para determinar se o fluxo de trabalho funciona. Para obter mais informações, consulte [os pontos finais do serviço call em HTTP ou HTTPS a partir de Azure Logic Apps](../connectors/connectors-native-http.md).

```json
"HTTP": {
   "type": "Http",
   "inputs": {
      "method": "<method-type>",
      "uri": "<HTTP-or-HTTPS-endpoint-URL>",
      "headers": { "<header-content>" },
      "queries": { "<query-parameters>" },
      "body": "<body-content>",
      "authentication": { "<authentication-type-and-property-values>" },
      "retryPolicy": {
         "type": "<retry-behavior>"
      },
   },
   "runAfter": {}
}
```

*Obrigatório*

| Propriedade | Valor | Tipo | Description |
|----------|-------|------|-------------|
| `method` | <*tipo método*> | String | O método a utilizar para o envio do pedido de saída: "GET", "PUT", "POST", "PATCH" ou "DELETE" |
| `uri` | <*HTTP-ou-HTTPS-ENDpoint-URL*> | String | O URL de ponto final HTTP ou HTTPS onde pretende enviar o pedido de saída. Tamanho máximo da corda: 2 KB <p>Para um serviço ou recurso Azure, esta sintaxe URI inclui o ID de recurso e o caminho para o recurso a que pretende aceder. |
|||||

*Opcional*

| Propriedade | Valor | Tipo | Description |
|----------|-------|------|-------------|
| `headers` | <*cabeçalho-conteúdo*> | Objeto JSON | Quaisquer cabeçalhos que você precisa incluir com o pedido <p>Por exemplo, para definir a língua e o tipo: <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` |
| `queries` | <*parâmetros de consulta*> | Objeto JSON | Quaisquer parâmetros de consulta que você precisa usar no pedido <p>Por exemplo, o `"queries": { "api-version": "2018-01-01" }` objeto adiciona `?api-version=2018-01-01` à chamada. |
| `body` | <*conteúdo corporal*> | Objeto JSON | O conteúdo da mensagem a enviar como carga útil com o pedido |
| `authentication` | <*valores de autenticação tipo e propriedade*> | Objeto JSON | O modelo de autenticação que o pedido utiliza para autenticar pedidos de saída. Para obter mais informações, consulte [Adicionar autenticação às chamadas de saída](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound). Além do Scheduler, a `authority` propriedade é suportada. Quando não especificado, o valor predefinido é `https://management.azure.com/` , mas pode utilizar um valor diferente. |
| `retryPolicy` > `type` | <*recandidutar comportamento*> | Objeto JSON | Personaliza o comportamento de repetição para falhas intermitentes, que têm o código de estado 408, 429 e 5XX, e quaisquer exceções de conectividade. Para obter mais informações, consulte [as políticas de Retry](../logic-apps/logic-apps-exception-handling.md#retry-policies). |
| <*outras propriedades de entrada específicas de ação*> | <*entrada-propriedade*> | Objeto JSON | Quaisquer outras propriedades de entrada que se apliquem a esta ação específica |
| <*outras propriedades específicas de ação*> | <*valor patrimonial*> | Objeto JSON | Quaisquer outras propriedades que se apliquem a esta ação específica |
|||||

*Exemplo*

Esta definição de ação recebe as últimas notícias enviando um pedido para o ponto final especificado:

```json
"HTTP": {
   "type": "Http",
   "inputs": {
      "method": "GET",
      "uri": "https://mynews.example.com/latest"
   }
}
```

<a name="join-action"></a>

### <a name="join-action"></a>Junte-se à ação

Esta ação cria uma cadeia a partir de todos os itens de uma matriz e separa esses itens com o carácter delimitador especificado. 

```json
"Join": {
   "type": "Join",
   "inputs": {
      "from": <array>,
      "joinWith": "<delimiter>"
   },
   "runAfter": {}
}
```

*Obrigatório*

| Valor | Tipo | Description | 
|-------|------|-------------| 
| <*matriz*> | Matriz | A matriz ou expressão que fornece os itens de origem. Se especificar uma expressão, enrôde essa expressão com citações duplas. | 
| <*delimiter*> | Cadeia de caracteres únicos | O personagem que separa cada item na corda | 
|||| 

*Exemplo*

Suponha que tenha uma variável "myIntegerArray" anteriormente criada que contém esta matriz de inteiros: 

`[1,2,3,4]` 

Esta definição de ação obtém os valores da variável utilizando a `variables()` função numa expressão e cria esta cadeia com esses valores, que são separados por uma vírgula: `"1,2,3,4"`

```json
"Join": {
   "type": "Join",
   "inputs": {
      "from": "@variables('myIntegerArray')",
      "joinWith": ","
   },
   "runAfter": {}
}
```

<a name="parse-json-action"></a>

### <a name="parse-json-action"></a>Ação parse JSON

Esta ação cria campos ou *fichas* fáceis de utilizar a partir das propriedades em conteúdo JSON. Em seguida, pode aceder a essas propriedades na sua aplicação lógica utilizando os tokens. Por exemplo, quando pretender utilizar a saída JSON de serviços como a Azure Service Bus e a Azure Cosmos DB, pode incluir esta ação na sua aplicação lógica para que possa fazer referência mais facilmente aos dados nessa saída.

```json
"Parse_JSON": {
   "type": "ParseJson",
   "inputs": {
      "content": "<JSON-source>",
         "schema": { "<JSON-schema>" }
      },
      "runAfter": {}
},
```

*Obrigatório*

| Valor | Tipo | Description | 
|-------|------|-------------| 
| <*Fonte JSON*> | Objeto JSON | O conteúdo JSON que você quer analisar | 
| <*JSON-schema*> | Objeto JSON | O esquema JSON que descreve o conteúdo JSON subjacente, que a ação utiliza para analisar o conteúdo JSON de origem. <p>**Dica**: No Design de Aplicações Lógicas, pode fornecer o esquema ou fornecer uma carga útil da amostra para que a ação possa gerar o esquema. | 
|||| 

*Exemplo*

Esta definição de ação cria estes tokens que você pode usar no seu fluxo de trabalho, mas apenas em ações que funcionam seguindo a ação **Parse JSON:**

`FirstName`, `LastName` e `Email`

```json
"Parse_JSON": {
   "type": "ParseJson",
   "inputs": {
      "content": {
         "Member": {
            "Email": "Sophie.Owen@contoso.com",
            "FirstName": "Sophie",
            "LastName": "Owen"
         }
      },
      "schema": {
         "type": "object",
         "properties": {
            "Member": {
               "type": "object",
               "properties": {
                  "Email": {
                     "type": "string"
                  },
                  "FirstName": {
                     "type": "string"
                  },
                  "LastName": {
                     "type": "string"
                  }
               }
            }
         }
      }
   },
   "runAfter": { }
},
```

Neste exemplo, a propriedade "content" especifica o conteúdo JSON para a ação de análise. Também pode fornecer este conteúdo JSON como a carga útil da amostra para gerar o esquema.

```json
"content": {
   "Member": { 
      "FirstName": "Sophie",
      "LastName": "Owen",
      "Email": "Sophie.Owen@contoso.com"
   }
},
```

A propriedade "schema" especifica o esquema JSON utilizado para descrever o conteúdo JSON:

```json
"schema": {
   "type": "object",
   "properties": {
      "Member": {
         "type": "object",
         "properties": {
            "FirstName": {
               "type": "string"
            },
            "LastName": {
               "type": "string"
            },
            "Email": {
               "type": "string"
            }
         }
      }
   }
}
```

<a name="query-action"></a>

### <a name="query-action"></a>Ação de consulta

Esta ação cria uma matriz a partir de itens de outra matriz com base numa condição ou filtro especificados.

```json
"Filter_array": {
   "type": "Query",
   "inputs": {
      "from": <array>,
      "where": "<condition-or-filter>"
   },
   "runAfter": {}
}
```

*Obrigatório*

| Valor | Tipo | Description | 
|-------|------|-------------| 
| <*matriz*> | Matriz | A matriz ou expressão que fornece os itens de origem. Se especificar uma expressão, enrôde essa expressão com citações duplas. |
| <*condição ou filtro*> | String | A condição utilizada para filtrar itens na matriz de origem <p>**Nota:** Se nenhum valor satisfaz a condição, a ação cria uma matriz vazia. |
|||| 

*Exemplo*

Esta definição de ação cria uma matriz que tem valores superiores ao valor especificado, que é dois:

```json
"Filter_array": {
   "type": "Query",
   "inputs": {
      "from": [ 1, 3, 0, 5, 4, 2 ],
      "where": "@greater(item(), 2)"
   }
}
```

<a name="response-action"></a>

### <a name="response-action"></a>Ação de resposta  

Esta ação cria a carga útil para a resposta a um pedido HTTP. 

```json
"Response" {
    "type": "Response",
    "kind": "http",
    "inputs": {
        "statusCode": 200,
        "headers": { <response-headers> },
        "body": { <response-body> }
    },
    "runAfter": {}
},
```

*Obrigatório*

| Valor | Tipo | Description | 
|-------|------|-------------| 
| <*código de estado de resposta*> | Número inteiro | O código de estado HTTP que é enviado para o pedido de entrada. O código predefinido é "200 OK", mas o código pode ser qualquer código de estado válido que comece com 2xx, 4xx ou 5xx, mas não com 3xxx. | 
|||| 

*Opcional*

| Valor | Tipo | Description | 
|-------|------|-------------| 
| <*cabeçalhos de resposta*> | Objeto JSON | Um ou mais cabeçalhos para incluir com a resposta | 
| <*resposta-corpo*> | Vários | O corpo de resposta, que pode ser uma corda, objeto JSON, ou mesmo conteúdo binário de uma ação anterior | 
|||| 

*Exemplo*

Esta definição de ação cria uma resposta a um pedido HTTP com o código de estado especificado, o corpo da mensagem e os cabeçalhos de mensagens:

```json
"Response": {
   "type": "Response",
   "inputs": {
      "statusCode": 200,
      "body": {
         "ProductID": 0,
         "Description": "Organic Apples"
      },
      "headers": {
         "x-ms-date": "@utcnow()",
         "content-type": "application/json"
      }
   },
   "runAfter": {}
}
```

*Restrições*

Ao contrário de outras ações, a ação **Resposta** tem restrições especiais: 

* O seu fluxo de trabalho só pode utilizar a ação **Resposta** quando o fluxo de trabalho começa com um gatilho de pedido HTTP, o que significa que o seu fluxo de trabalho deve ser desencadeado por um pedido HTTP.

* O seu fluxo de trabalho pode utilizar a ação **Response** em qualquer *lugar, exceto* dentro de laços **Foreach,** **até** loops, incluindo laços sequenciais e ramos paralelos. 

* O pedido original só obtém a resposta do seu fluxo de trabalho quando todas as ações necessárias pela ação **Resposta** estiverem concluídas dentro do [prazo de tempo HTTP](../logic-apps/logic-apps-limits-and-config.md#http-limits).

  No entanto, se o seu fluxo de trabalho chamar outra aplicação lógica como um fluxo de trabalho aninhado, o fluxo de trabalho dos pais aguarda até que o fluxo de trabalho aninhado termine, não importa quanto tempo passe antes que o fluxo de trabalho aninhado termine.

* Quando o seu fluxo de trabalho utiliza a ação **Resposta** e um padrão de resposta sincronizado, o fluxo de trabalho também não pode usar o comando **splitOn** na definição de gatilho porque esse comando cria múltiplas execuções. Verifique este caso quando o método PUT for utilizado e, se for verdade, devolva uma resposta de "mau pedido".

  Caso contrário, se o seu fluxo de trabalho utilizar o comando **splitOn** e uma ação **de resposta,** o fluxo de trabalho funciona assíncronamente e devolve imediatamente uma resposta "202 ACCEPTED".

* Quando a execução do seu fluxo de trabalho chega à ação **Resposta,** mas o pedido de entrada já recebeu uma resposta, a ação **Resposta** é marcada como "Falhada" devido ao conflito. E como resultado, a sua aplicação lógica também está marcada com o estado de "Falhado".

<a name="select-action"></a>

### <a name="select-action"></a>Selecione ação

Esta ação cria uma matriz com objetos JSON transformando itens de outra matriz com base no mapa especificado. A matriz de saída e a matriz de origem têm sempre o mesmo número de itens. Embora não possa alterar o número de objetos na matriz de saída, pode adicionar ou remover propriedades e os seus valores através desses objetos. A `select` propriedade especifica pelo menos um par de valores-chave que definem o mapa para transformar itens na matriz de origem. Um par de valores-chave representa uma propriedade e o seu valor em todos os objetos na matriz de saída.

```json
"Select": {
   "type": "Select",
   "inputs": {
      "from": <array>,
      "select": { 
          "<key-name>": "<expression>",
          "<key-name>": "<expression>"        
      }
   },
   "runAfter": {}
},
```

*Obrigatório* 

| Valor | Tipo | Description | 
|-------|------|-------------| 
| <*matriz*> | Matriz | A matriz ou expressão que fornece os itens de origem. Certifique-se de que encerra uma expressão com citações duplas. <p>**Nota:** Se a matriz de origem estiver vazia, a ação cria uma matriz vazia. | 
| <*nome-chave*> | String | O nome da propriedade atribuído ao resultado da *expressão* <> <p>Para adicionar uma nova propriedade em todos os objetos na matriz de saída, forneça um> *de <nome-chave* para essa propriedade e uma *expressão* <> para o valor da propriedade. <p>Para remover uma propriedade de todos os objetos da matriz, omita o <*> de nome-chave* para essa propriedade. | 
| <*expressão*> | String | A expressão que transforma o item na matriz de origem e atribui o resultado a <*nome-chave*> | 
|||| 

A ação **Select** cria um conjunto como saída, por isso qualquer ação que queira utilizar esta saída deve aceitar uma matriz, ou deve converter a matriz no tipo que a ação do consumidor aceita. Por exemplo, para converter o conjunto de saída para uma corda, pode passar essa matriz para a ação **Compose** e, em seguida, referenciar a saída da ação **Compose** nas suas outras ações.

*Exemplo*

Esta definição de ação cria uma matriz de objeto JSON a partir de uma matriz inteiro. A ação itera através da matriz de origem, obtém cada valor inteiro usando a `@item()` expressão, e atribui cada valor à `number` " propriedade em cada objeto JSON:

```json
"Select": {
   "type": "Select",
   "inputs": {
      "from": [ 1, 2, 3 ],
      "select": { 
         "number": "@item()" 
      }
   },
   "runAfter": {}
},
```

Aqui está a matriz que esta ação cria:

`[ { "number": 1 }, { "number": 2 }, { "number": 3 } ]`

Para utilizar esta produção de matriz em outras ações, passe esta saída para uma ação **Compose:**

```json
"Compose": {
   "type": "Compose",
   "inputs": "@body('Select')",
   "runAfter": {
      "Select": [ "Succeeded" ]
   }
},
```

Em seguida, pode utilizar a saída da ação **Compose** nas suas outras ações, por exemplo, o **Office 365 Outlook - Envie uma ação por e-mail:**

```json
"Send_an_email": {
   "type": "ApiConnection",
   "inputs": {
      "body": {
         "Body": "@{outputs('Compose')}",
         "Subject": "Output array from Select and Compose actions",
         "To": "<your-email@domain>"
      },
      "host": {
         "connection": {
            "name": "@parameters('$connections')['office365']['connectionId']"
         }
      },
      "method": "post",
      "path": "/Mail"
   },
   "runAfter": {
      "Compose": [ "Succeeded" ]
   }
},
```

<a name="table-action"></a>

### <a name="table-action"></a>Ação de mesa

Esta ação cria uma tabela CSV ou HTML a partir de uma matriz. Para matrizes com objetos JSON, esta ação cria automaticamente os cabeçalhos da coluna a partir dos nomes de propriedade dos objetos. Para matrizes com outros tipos de dados, deve especificar os cabeçalhos e valores da coluna. Por exemplo, esta matriz inclui as propriedades "ID" e "Product_Name" que esta ação pode usar para os nomes dos cabeçalhos da coluna:

`[ {"ID": 0, "Product_Name": "Apples"}, {"ID": 1, "Product_Name": "Oranges"} ]` 

```json
"Create_<CSV | HTML>_table": {
   "type": "Table",
   "inputs": {
      "format": "<CSV | HTML>",
      "from": <array>,
      "columns": [ 
         {
            "header": "<column-name>",
            "value": "<column-value>"
         },
         {
            "header": "<column-name>",
            "value": "<column-value>"
         } 
      ]
   },
   "runAfter": {}
}
```

*Obrigatório* 

| Valor | Tipo | Description | 
|-------|------|-------------| 
| \<CSV *or* HTML>| String | O formato para a tabela que pretende criar | 
| <*matriz*> | Matriz | A matriz ou expressão que fornece os itens de origem para a mesa <p>**Nota:** Se a matriz de origem estiver vazia, a ação cria uma mesa vazia. | 
|||| 

*Opcional*

Para especificar ou personalizar os cabeçalhos e valores das colunas, utilize a `columns` matriz. Quando `header-value` os pares têm o mesmo nome do cabeçalho, os seus valores aparecem na mesma coluna sob esse nome do cabeçalho. Caso contrário, cada cabeçalho único define uma coluna única.

| Valor | Tipo | Description | 
|-------|------|-------------| 
| <*nome de coluna*> | String | O nome do cabeçalho para uma coluna | 
| <*valor da coluna*> | Qualquer | O valor naquela coluna | 
|||| 

*Exemplo 1*

Suponha que tenha uma variável "myItemArray" anteriormente criada que contém atualmente esta matriz:

`[ {"ID": 0, "Product_Name": "Apples"}, {"ID": 1, "Product_Name": "Oranges"} ]`

Esta definição de ação cria uma tabela CSV a partir da variável "myItemArray". A expressão utilizada pela `from` propriedade obtém a matriz de "myItemArray" utilizando a `variables()` função:

```json
"Create_CSV_table": {
   "type": "Table",
   "inputs": {
      "format": "CSV",
      "from": "@variables('myItemArray')"
   },
   "runAfter": {}
}
```

Aqui está a tabela CSV que esta ação cria: 

```
ID,Product_Name 
0,Apples 
1,Oranges 
```

*Exemplo 2*

Esta definição de ação cria uma tabela HTML a partir da variável "myItemArray". A expressão utilizada pela `from` propriedade obtém a matriz de "myItemArray" utilizando a `variables()` função:

```json
"Create_HTML_table": {
   "type": "Table",
   "inputs": {
      "format": "HTML",
      "from": "@variables('myItemArray')"
   },
   "runAfter": {}
}
```

Aqui está a tabela HTML que esta ação cria: 

<table><thead><tr><th>ID</th><th>Product_Name</th></tr></thead><tbody><tr><td>0</td><td>Maçãs</td></tr><tr><td>1</td><td>Laranjas</td></tr></tbody></table>

*Exemplo 3*

Esta definição de ação cria uma tabela HTML a partir da variável "myItemArray". No entanto, este exemplo substitui os nomes de cabeçalho de coluna predefinidos com "Stock_ID" e "Descrição", e adiciona a palavra "Orgânico" aos valores na coluna "Descrição".

```json
"Create_HTML_table": {
   "type": "Table",
   "inputs": {
      "format": "HTML",
      "from": "@variables('myItemArray')",
      "columns": [ 
         {
            "header": "Stock_ID",
            "value": "@item().ID"
         },
         {
            "header": "Description",
            "value": "@concat('Organic ', item().Product_Name)"
         }
      ]
    },
   "runAfter": {}
},
```

Aqui está a tabela HTML que esta ação cria: 

<table><thead><tr><th>Stock_ID</th><th>Descrição</th></tr></thead><tbody><tr><td>0</td><td>Maçãs Orgânicas</td></tr><tr><td>1</td><td>Laranjas Orgânicas</td></tr></tbody></table>

<a name="terminate-action"></a>

### <a name="terminate-action"></a>Ação de fim de ação

Esta ação interrompe a execução para uma instância de fluxo de trabalho, cancela quaisquer ações em curso, ignora quaisquer ações restantes e devolve o estado especificado. Por exemplo, pode utilizar a ação **Terminate** quando a sua aplicação lógica deve sair completamente de um estado de erro. Esta ação não afeta as ações já concluídas e não pode aparecer dentro **de Foreach** e **Até** loops, incluindo loops sequenciais.

```json
"Terminate": {
   "type": "Terminate",
   "inputs": {
       "runStatus": "<status>",
       "runError": {
            "code": "<error-code-or-name>",
            "message": "<error-message>"
       }
   },
   "runAfter": {}
}
```

*Obrigatório*

| Valor | Tipo | Description | 
|-------|------|-------------| 
| <*estado*> | String | O estatuto para voltar para a corrida: "Falhado", "Cancelado", ou "Bem sucedido" |
|||| 

*Opcional*

As propriedades do objeto "runStatus" só se aplicam quando a propriedade "runStatus" está definida como "Falhado".

| Valor | Tipo | Description | 
|-------|------|-------------| 
| <*erro-código ou nome*> | String | O código ou nome do erro |
| <*erro-mensagem*> | String | A mensagem ou texto que descreve o erro e quaisquer ações que o utilizador da aplicação possa tomar | 
|||| 

*Exemplo*

Esta definição de ação para uma execução de fluxo de trabalho, define o estado de execução para "Falhado", e devolve o estado, um código de erro e uma mensagem de erro:

```json
"Terminate": {
    "type": "Terminate",
    "inputs": {
        "runStatus": "Failed",
        "runError": {
            "code": "Unexpected response",
            "message": "The service received an unexpected response. Please try again."
        }
   },
   "runAfter": {}
}
```

<a name="wait-action"></a>

### <a name="wait-action"></a>Ação de espera

Esta ação interrompe a execução do fluxo de trabalho para o intervalo especificado ou até ao tempo especificado, mas não para ambos.

*Intervalo especificado*

```json
"Delay": {
   "type": "Wait",
   "inputs": {
      "interval": {
         "count": <number-of-units>,
         "unit": "<interval>"
      }
   },
   "runAfter": {}
},
```

*Tempo especificado*

```json
"Delay_until": {
   "type": "Wait",
   "inputs": {
      "until": {
         "timestamp": "<date-time-stamp>"
      }
   },
   "runAfter": {}
},
```

*Obrigatório*

| Valor | Tipo | Description | 
|-------|------|-------------| 
| <*número de unidades*> | Número inteiro | Para a ação **Delay,** o número de unidades a aguardar | 
| <*intervalo*> | String | Para a ação **delay,** o intervalo para esperar: "Segundo", "Minuto", "Hora", "Dia", "Semana", "Mês" | 
| <*carimbo de data*> | String | Para o **atraso até** à ação, a data e a hora para retomar a execução. Este valor deve utilizar o formato de hora de [data UTC](https://en.wikipedia.org/wiki/Coordinated_Universal_Time). | 
|||| 

*Exemplo 1*

Esta definição de ação interrompe o fluxo de trabalho durante 15 minutos:

```json
"Delay": {
   "type": "Wait",
   "inputs": {
      "interval": {
         "count": 15,
         "unit": "Minute"
      }
   },
   "runAfter": {}
},
```

*Exemplo 2*

Esta definição de ação interrompe o fluxo de trabalho até ao tempo especificado:

```json
"Delay_until": {
   "type": "Wait",
   "inputs": {
      "until": {
         "timestamp": "2017-10-01T00:00:00Z"
      }
   },
   "runAfter": {}
},
```

<a name="workflow-action"></a>

### <a name="workflow-action"></a>Ação de fluxo de trabalho

Esta ação chama-se outra aplicação lógica previamente criada, o que significa que pode incluir e reutilizar outros fluxos de trabalho de aplicações lógicas. Também pode utilizar as saídas da aplicação lógica infantil ou *aninhada* em ações que seguem a app lógica aninhada, desde que a aplicação lógica infantil devolva uma resposta.

O motor da Logic Apps verifica o acesso ao gatilho que pretende chamar, por isso certifique-se de que pode aceder a esse gatilho. Além disso, a aplicação lógica aninhada deve cumprir estes critérios:

* Um gatilho torna a aplicação lógica aninhada callable, como um [pedido](#request-trigger) ou disparador [HTTP](#http-trigger)

* A mesma subscrição do Azure que a sua aplicação lógica parental

* Para utilizar as saídas da app lógica aninhada na sua app lógica principal, a app lógica aninhada deve ter uma ação [de Resposta](#response-action)

```json
"<nested-logic-app-name>": {
   "type": "Workflow",
   "inputs": {
      "body": { "<body-content" },
      "headers": { "<header-content>" },
      "host": {
         "triggerName": "<trigger-name>",
         "workflow": {
            "id": "/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group>/providers/Microsoft.Logic/<nested-logic-app-name>"
         }
      }
   },
   "runAfter": {}
}
```

*Obrigatório*

| Valor | Tipo | Description | 
|-------|------|-------------| 
| <*aninhado-lógica-app-nome*> | String | O nome para a aplicação lógica que você quer chamar | 
| <*gatilho nome*> | String | O nome para o gatilho na app lógica aninhada que você quer chamar | 
| <*ID de subscrição Azure*> | String | O ID de subscrição do Azure para a app lógica aninhada |
| <*Grupo de recursos Azure*> | String | O nome do grupo de recursos Azure para a app lógica aninhada |
| <*aninhado-lógica-app-nome*> | String | O nome para a aplicação lógica que você quer chamar |
||||

*Opcional*

| Valor | Tipo | Description | 
|-------|------|-------------|  
| <*cabeçalho-conteúdo*> | Objeto JSON | Quaisquer cabeçalhos para enviar com a chamada | 
| <*conteúdo corporal*> | Objeto JSON | Qualquer conteúdo de mensagem para enviar com a chamada | 
||||

*Saídas*

As saídas desta ação variam em função da ação de Resposta da app da lógica aninhada. Se a aplicação lógica aninhada não incluir uma ação de Resposta, as saídas estão vazias.

*Exemplo*

Após o fim da ação "Start_search", esta definição de ação de fluxo de trabalho chama outra aplicação lógica chamada "Get_product_information", que passa nas entradas especificadas:

```json
"actions": {
   "Start_search": { <action-definition> },
   "Get_product_information": {
      "type": "Workflow",
      "inputs": {
         "body": {
            "ProductID": "24601",
         },
         "host": {
            "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/InventoryManager-RG/providers/Microsoft.Logic/Get_product_information",
            "triggerName": "Find_product"
         },
         "headers": {
            "content-type": "application/json"
         }
      },
      "runAfter": { 
         "Start_search": [ "Succeeded" ]
      }
   }
},
```

## <a name="control-workflow-action-details"></a>Controlar detalhes de ação do fluxo de trabalho

<a name="foreach-action"></a>

### <a name="foreach-action"></a>Ação foreach

Esta ação em loop itera através de uma matriz e executa ações em cada item de matriz. Por predefinição, o laço "para cada um" funciona em paralelo até um número máximo de loops. Para este máximo, consulte [Limites e config](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Saiba [como criar laços "para cada um".](../logic-apps/logic-apps-control-flow-loops.md#foreach-loop)

```json
"For_each": {
   "type": "Foreach",
   "actions": { 
      "<action-1>": { "<action-definition-1>" },
      "<action-2>": { "<action-definition-2>" }
   },
   "foreach": "<for-each-expression>",
   "runAfter": {},
   "runtimeConfiguration": {
      "concurrency": {
         "repetitions": <count>
      }
    },
    "operationOptions": "<operation-option>"
}
```

*Obrigatório* 

| Valor | Tipo | Description | 
|-------|------|-------------| 
| <*ação-1... n*> | String | Os nomes das ações que funcionam em cada item de matriz | 
| <*ação-definição-1... n*> | Objeto JSON | As definições das ações que correm | 
| <*para cada expressão*> | String | A expressão que faz referência a cada item na matriz especificada | 
|||| 

*Opcional*

| Valor | Tipo | Description | 
|-------|------|-------------| 
| <*contar*> | Número inteiro | Por predefinição, as iterações em loop "para cada um" funcionam ao mesmo tempo (simultaneamente ou em paralelo) até ao [limite predefinido](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Para alterar este limite, definindo uma nova *contagem* de <> valor, consulte [Alterar a concuência do loop "para cada" loop](#change-for-each-concurrency). | 
| <*opção de operação*> | String | Para executar um loop "para cada um" sequencialmente, em vez de em paralelo, definir <*opção de operação*> `Sequential` ou <*contar*> para , mas não `1` para ambos. Para obter mais informações, consulte ["for each" loops sequencialmente](#sequential-for-each). | 
|||| 

*Exemplo*

Este loop "para cada um" envia um e-mail para cada item na matriz, que contém anexos a partir de um e-mail de entrada. O loop envia um e-mail, incluindo o anexo, a uma pessoa que revê o anexo.

```json
"For_each": {
   "type": "Foreach",
   "actions": {
      "Send_an_email": {
         "type": "ApiConnection",
         "inputs": {
            "body": {
               "Body": "@base64ToString(items('For_each')?['Content'])",
               "Subject": "Review attachment",
               "To": "Sophie.Owen@contoso.com"
                },
            "host": {
               "connection": {
                  "id": "@parameters('$connections')['office365']['connectionId']"
               }
            },
            "method": "post",
            "path": "/Mail"
         },
         "runAfter": {}
      }
   },
   "foreach": "@triggerBody()?['Attachments']",
   "runAfter": {}
}
```

Para especificar apenas uma matriz que é passada como saída do gatilho, esta expressão obtém o *<conjunto de>* matriz do corpo do gatilho. Para evitar uma falha se a matriz não existir, a expressão utiliza o `?` operador:

`@triggerBody()?['<array-name>']` 

<a name="if-action"></a>

### <a name="if-action"></a>Se a ação

Esta ação, que é uma *afirmação condicional,* avalia uma expressão que representa uma condição e dirige um ramo diferente com base no facto de a condição ser verdadeira ou falsa. Se a condição for verdadeira, a condição é marcada com o estatuto de "Bem-sucedido". Saiba [como criar declarações condicionais.](../logic-apps/logic-apps-control-flow-conditional-statement.md)

``` json
"Condition": {
   "type": "If",
   "expression": { "<condition>" },
   "actions": {
      "<action-1>": { "<action-definition>" }
   },
   "else": {
      "actions": {
        "<action-2>": { "<action-definition" }
      }
   },
   "runAfter": {}
}
```

| Valor | Tipo | Description | 
|-------|------|-------------| 
| <*condição*> | Objeto JSON | A condição, que pode ser uma expressão, para avaliar | 
| <*ação-1*> | Objeto JSON | A ação a correr quando <*condição*> avalia a verdade | 
| <*definição de ação*> | Objeto JSON | A definição para a ação | 
| <*ação-2*> | Objeto JSON | A ação a correr quando <*condição*> avalia a falsa | 
|||| 

As ações nos `actions` `else` ou objetos obtêm estes estatutos:

* "Bem sucedido" quando correm e têm sucesso
* "Falhado" quando correm e falham
* "Ignorado" quando o respetivo ramo não funciona

*Exemplo*

Esta condição especifica que quando a variável número inteiro tem um valor superior a zero, o fluxo de trabalho verifica um website. Se a variável for zero ou menos, o fluxo de trabalho verifica um site diferente.

```json
"Condition": {
   "type": "If",
   "expression": {
      "and": [ {
         "greater": [ "@variables('myIntegerVariable')", 0 ] 
      } ]
   },
   "actions": { 
      "HTTP - Check this website": {
         "type": "Http",
         "inputs": {
         "method": "GET",
            "uri": "http://this-url"
         },
         "runAfter": {}
      }
   },
   "else": {
      "actions": {
         "HTTP - Check this other website": {
            "type": "Http",
            "inputs": {
               "method": "GET",
               "uri": "http://this-other-url"
            },
            "runAfter": {}
         }
      }
   },
   "runAfter": {}
}
```  

#### <a name="how-conditions-use-expressions"></a>Como as condições usam expressões

Aqui estão alguns exemplos que mostram como pode usar expressões em condições:
  
| JSON | Resultado | 
|------|--------| 
| "expressão": " @parameters ('<*temSpecialAction*>')" | Apenas para expressões booleanas, a condição passa por qualquer valor que avalie para verdade. <p>Para converter outros tipos em Boolean, utilize estas funções: `empty()` ou `equals()` . | 
| "expressão": " @greater (ação <>').output.value, parâmetros ('<*limiar*>)"  | Para funções de comparação, a ação só funciona quando a produção de <*ação*> é mais do que o *limiar* <> valor. | 
| "expressão": " @or (ação maior(ação  <>').output.value, parâmetros ('<*limiar*>')), menos (ações('<>'.output.value, 100)".  | Para funções lógicas e criação de expressões booleanas aninhadas, a ação corre quando a saída de <*ação*> é mais do que o *limiar* <> valor ou inferior a 100. | 
| "expressão": " @equals (comprimento (ação <>').outputs.errors), 0)" | Pode utilizar funções de matriz para verificar se a matriz tem itens. A ação funciona quando a `errors` matriz está vazia. | 
||| 

<a name="scope-action"></a>

### <a name="scope-action"></a>Ação de âmbito

Esta ação, logicamente, agrupo as ações em *âmbitos*, que obtêm o seu próprio estatuto após as ações nesse âmbito terminarem de funcionar. Em seguida, pode utilizar o estado do âmbito para determinar se outras ações funcionam. Saiba [como criar âmbitos.](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)

```json
"Scope": {
   "type": "Scope",
   "actions": {
      "<inner-action-1>": {
         "type": "<action-type>",
         "inputs": { "<action-inputs>" },
         "runAfter": {}
      },
      "<inner-action-2>": {
         "type": "<action-type>",
         "inputs": { "<action-inputs>" },
         "runAfter": {}
      }
   }
}
```

*Obrigatório*

| Valor | Tipo | Description | 
|-------|------|-------------|  
| <*inner-action-1... n*> | Objeto JSON | Uma ou mais ações que correm dentro do âmbito |
| <*entradas de ação*> | Objeto JSON | As entradas para cada ação |
|||| 

<a name="switch-action"></a>

### <a name="switch-action"></a>Ação Switch

Esta ação, também conhecida como *declaração de comutação,* organiza outras ações em *casos*, e atribui um valor a cada caso, exceto para o caso padrão se existir. Quando o seu fluxo de trabalho é executado, a ação **Switch** compara o valor de uma expressão, objeto ou símbolo com os valores especificados para cada caso. Se a ação **Switch** encontrar um caso correspondente, o seu fluxo de trabalho executa apenas as ações para esse caso. Cada vez que a ação **Switch** é executado, ou apenas existe um caso correspondente ou não existem fósforos. Se não existirem correspondências, a ação **Switch** executa as ações predefinidos. Saiba [como criar declarações de switch](../logic-apps/logic-apps-control-flow-switch-statement.md).

``` json
"Switch": {
   "type": "Switch",
   "expression": "<expression-object-or-token>",
   "cases": {
      "Case": {
         "actions": {
           "<action-name>": { "<action-definition>" }
         },
         "case": "<matching-value>"
      },
      "Case_2": {
         "actions": {
           "<action-name>": { "<action-definition>" }
         },
         "case": "<matching-value>"
      }
   },
   "default": {
      "actions": {
         "<default-action-name>": { "<default-action-definition>" }
      }
   },
   "runAfter": {}
}
```

*Obrigatório*

| Valor | Tipo | Description | 
|-------|------|-------------| 
| <*expressão-objeto-ou-token*> | Varia | A expressão, objeto JSON, ou símbolo para avaliar | 
| <*nome de ação*> | String | O nome da ação a concorrer para o caso correspondente | 
| <*definição de ação*> | Objeto JSON | A definição para a ação a concorrer para o caso correspondente | 
| <*valor correspondente*> | Varia | O valor a comparar com o resultado avaliado | 
|||| 

*Opcional*

| Valor | Tipo | Description | 
|-------|------|-------------| 
| <*nome de ação padrão*> | String | O nome da ação padrão a executar quando não existe caso de correspondência | 
| <*definição de ação padrão*> | Objeto JSON | A definição para a ação a decorrer quando não existe caso de correspondência | 
|||| 

*Exemplo*

Esta definição de ação avalia se a pessoa que responde ao pedido de aprovação e-mail selecionou a opção "Aprovar" ou a opção "Rejeitar". Com base nesta escolha, a ação da **Switch** executa as ações para o caso correspondente, que é enviar outro e-mail para o responder mas com redação diferente em cada caso. 

``` json
"Switch": {
   "type": "Switch",
   "expression": "@body('Send_approval_email')?['SelectedOption']",
   "cases": {
      "Case": {
         "actions": {
            "Send_an_email": { 
               "type": "ApiConnection",
               "inputs": {
                  "Body": "Thank you for your approval.",
                  "Subject": "Response received", 
                  "To": "Sophie.Owen@contoso.com"
               },
               "host": {
                  "connection": {
                     "name": "@parameters('$connections')['office365']['connectionId']"
                  }
               },
               "method": "post",
               "path": "/Mail"
            },
            "runAfter": {}
         },
         "case": "Approve"
      },
      "Case_2": {
         "actions": {
            "Send_an_email_2": { 
               "type": "ApiConnection",
               "inputs": {
                  "Body": "Thank you for your response.",
                  "Subject": "Response received", 
                  "To": "Sophie.Owen@contoso.com"
               },
               "host": {
                  "connection": {
                     "name": "@parameters('$connections')['office365']['connectionId']"
                  }
               },
               "method": "post",
               "path": "/Mail"
            },
            "runAfter": {}     
         },
         "case": "Reject"
      }
   },
   "default": {
      "actions": { 
         "Send_an_email_3": { 
            "type": "ApiConnection",
            "inputs": {
               "Body": "Please respond with either 'Approve' or 'Reject'.",
               "Subject": "Please respond", 
               "To": "Sophie.Owen@contoso.com"
            },
            "host": {
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
   "runAfter": {
      "Send_approval_email": [ 
         "Succeeded"
      ]
   }
}
```

<a name="until-action"></a>

### <a name="until-action"></a>Até a ação

Esta ação em loop contém ações que funcionam até que a condição especificada seja verdadeira. O loop verifica a condição como o último passo depois de todas as outras ações terem sido executadas. Pode incluir mais do que uma ação no `"actions"` objeto, e a ação deve definir pelo menos um limite. Aprenda [a criar laços "até".](../logic-apps/logic-apps-control-flow-loops.md#until-loop) 

```json
 "Until": {
   "type": "Until",
   "actions": {
      "<action-name>": {
         "type": "<action-type>",
         "inputs": { "<action-inputs>" },
         "runAfter": {}
      },
      "<action-name>": {
         "type": "<action-type>",
         "inputs": { "<action-inputs>" },
         "runAfter": {}
      }
   },
   "expression": "<condition>",
   "limit": {
      "count": <loop-count>,
      "timeout": "<loop-timeout>"
   },
   "runAfter": {}
}
```

| Valor | Tipo | Description | 
|-------|------|-------------| 
| <*nome de ação*> | String | O nome para a ação que quer correr dentro do loop | 
| <*tipo de ação*> | String | O tipo de ação que quer executar | 
| <*entradas de ação*> | Vários | As entradas para a ação a correr | 
| <*condição*> | String | A condição ou expressão para avaliar depois de todas as ações no loop terminar correndo | 
| <*contagem de loop*> | Número inteiro | O limite para o maior número de ciclos que a ação pode executar. Para obter mais informações sobre o limite predefinido e o limite máximo, consulte [Limites e configuração para Apps Azure Logic](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). | 
| <*loop-timeout*> | String | O limite do tempo mais longo que o laço pode correr. O `timeout` valor predefinido é , que é o formato `PT1H` [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601)requerido . |
|||| 

> [!NOTE]
> Se a expressão depender da saída de qualquer ação dentro do loop Until, certifique-se de que explica qualquer falha que resulte dessa ação.

*Exemplo*

Esta definição de ação em loop envia um pedido HTTP para o URL especificado até que uma destas condições seja satisfeita:

* O pedido obtém uma resposta com o código de estado "200 OK".
* O loop já correu 60 vezes.
* O ciclo já dura há uma hora.

```json
 "Run_until_loop_succeeds_or_expires": {
    "type": "Until",
    "actions": {
        "HTTP": {
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "http://myurl"
            },
            "runAfter": {}
        }
    },
    "expression": "@equals(outputs('HTTP')['statusCode'], 200)",
    "limit": {
        "count": 60,
        "timeout": "PT1H"
    },
    "runAfter": {}
}
```

<a name="subscribe-unsubscribe"></a>

## <a name="webhooks-and-subscriptions"></a>Webhooks e subscrições

Os gatilhos e ações baseados na Webhook não verificam regularmente os pontos finais, mas aguardam eventos ou dados específicos nesses pontos finais. Estes gatilhos e ações *subscrevem* os pontos finais fornecendo um *URL de retorno* onde o ponto final pode enviar respostas.

A `subscribe` chamada ocorre quando o fluxo de trabalho muda de alguma forma, por exemplo, quando as credenciais são renovadas, ou quando os parâmetros de entrada mudam para um gatilho ou ação. Esta chamada utiliza os mesmos parâmetros que as ações HTTP padrão. 

A `unsubscribe` chamada ocorre automaticamente quando uma operação torna o gatilho ou a ação inválido, por exemplo:

* Apagar ou desativar o gatilho. 
* Apagar ou desativar o fluxo de trabalho. 
* Apagar ou desativar a subscrição. 

Para suportar estas chamadas, a `@listCallbackUrl()` expressão devolve um "URL de retorno" único para o gatilho ou ação. Este URL representa um identificador único para os pontos finais que utilizam a API REST do serviço. Os parâmetros para esta função são os mesmos que o gatilho ou ação do webhook.

<a name="asynchronous-limits"></a>

## <a name="change-asynchronous-duration"></a>Alterar duração assíncronea

Tanto para gatilhos como para ações, pode limitar a duração do padrão assíncronos a um intervalo de tempo específico adicionando a `limit.timeout` propriedade. Assim, se a ação não tiver terminado quando o intervalo terminar, o estado da ação está marcado como `Cancelled` com o `ActionTimedOut` código. A `timeout` propriedade utiliza o formato [ISO 8601.](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations)

``` json
"<trigger-or-action-name>": {
   "type": "Workflow | Webhook | Http | ApiConnectionWebhook | ApiConnection",
   "inputs": {},
   "limit": {
      "timeout": "PT10S"
   },
   "runAfter": {}
}
```

<a name="runtime-config-options"></a>

## <a name="runtime-configuration-settings"></a>Definições de configuração de tempo de execução

Pode alterar o comportamento de tempo de execução padrão para gatilhos e ações adicionando estas `runtimeConfiguration` propriedades à definição de gatilho ou ação.

| Propriedade | Tipo | Description | Gatilho ou ação | 
|----------|------|-------------|-------------------| 
| `runtimeConfiguration.concurrency.runs` | Número inteiro | Alterar o [*limite de predefinição*](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits) no número de casos de fluxo de trabalho que podem ser executados ao mesmo tempo (simultaneamente ou em paralelo). Ajustar este valor pode ajudar a limitar o número de pedidos que os sistemas de backend recebem. <p>Definir o `runs` imóvel para `1` funcionar da mesma forma que definir o `operationOptions` imóvel para `SingleInstance` . Pode definir qualquer uma das propriedades, mas não as duas. <p>Para alterar o limite por defeito, consulte [alterar a conuscência do gatilho](#change-trigger-concurrency) ou desencadear [sequencialmente](#sequential-trigger). | Todos os gatilhos | 
| `runtimeConfiguration.concurrency.maximumWaitingRuns` | Número inteiro | Altere o [*limite padrão*](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits) no número de casos de fluxo de trabalho que devem esperar para executar quando a sua aplicação lógica já está executando as instâncias máximas simultâneas. <p>Para alterar o limite de predefinição, consulte [o limite de execuções de espera change](#change-waiting-runs). | Todos os gatilhos | 
| `runtimeConfiguration.concurrency.repetitions` | Número inteiro | Altere o [*limite padrão*](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits) no número de iterações de loop "para cada" que podem ser executadas ao mesmo tempo (simultaneamente ou em paralelo). <p>Definir o `repetitions` imóvel para `1` funcionar da mesma forma que definir o `operationOptions` imóvel para `SingleInstance` . Pode definir qualquer uma das propriedades, mas não as duas. <p>Para alterar o limite por defeito, consulte [alterar "para cada" concurrency](#change-for-each-concurrency) ou [executar sequencialmente os ciclos "para cada um".](#sequential-for-each) | Ação: <p>[Foreach](#foreach-action) | 
| `runtimeConfiguration.paginationPolicy.minimumItemCount` | Número inteiro | Para ações específicas que suportam e têm a paginação ligada, este valor especifica o número *mínimo* de resultados a recuperar. <p>Para ligar a paginação, consulte [obter dados, itens ou resultados em massa utilizando a paginação](../logic-apps/logic-apps-exceed-default-page-size-with-pagination.md) | Ação: Variada |
| `runtimeConfiguration.secureData.properties` | Matriz | Em muitos gatilhos e ações, estas configurações escondem entradas, saídas ou ambos do histórico de execução da aplicação lógica. <p>Para saber mais sobre a salvaguarda destes dados, consulte [ocultar entradas e saídas da história do run.](../logic-apps/logic-apps-securing-a-logic-app.md#secure-data-code-view) | A maioria dos gatilhos e ações |
| `runtimeConfiguration.staticResult` | Objeto JSON | Para ações que suportam e têm a definição [de resultado estático](../logic-apps/test-logic-apps-mock-data-static-results.md) ligada, o `staticResult` objeto tem estes atributos: <p>- `name`, que faz referência ao nome de definição de resultados estáticos da ação atual, que aparece dentro do `staticResults` atributo no atributo do workflow da sua aplicação `definition` lógica. Para obter mais informações, consulte [resultados estáticos - Referência de esquema para linguagem de definição de fluxo de trabalho.](../logic-apps/logic-apps-workflow-definition-language.md#static-results) <p> - `staticResultOptions`, que especifica se os resultados estáticos são `Enabled` ou não para a ação atual. <p>Para ligar os resultados estáticos, consulte [as aplicações lógicas de teste com dados falsos, configurando resultados estáticos](../logic-apps/test-logic-apps-mock-data-static-results.md) | Ação: Variada |
||||| 

<a name="operation-options"></a>

## <a name="operation-options"></a>Opções de operação

Pode alterar o comportamento padrão para gatilhos e ações com a `operationOptions` propriedade na definição de gatilho ou ação.

| Opção de operação | Tipo | Description | Gatilho ou ação | 
|------------------|------|-------------|-------------------| 
| `DisableAsyncPattern` | String | Executar ações baseadas em HTTP sincronizadamente, em vez de assíncronos. <p><p>Para definir esta opção, consulte [executar as ações de forma sincronizada](#disable-asynchronous-pattern). | Ações: <p>[ApiConnection,](#apiconnection-action) <br>[HTTP](#http-action), <br>[Response](#response-action) | 
| `IncludeAuthorizationHeadersInOutputs` | String | Para aplicações lógicas que [permitem ao Azure Ative Directory Open Authentication (Azure AD OAuth)](../logic-apps/logic-apps-securing-a-logic-app.md#enable-oauth) autorizar o acesso a chamadas de entrada para um ponto final de gatilho baseado em pedidos, inclua o `Authorization` cabeçalho a partir do token de acesso OAuth nas saídas do gatilho. Para obter mais informações, consulte [incluir o cabeçalho 'Autorização' no pedido de saídas de gatilho](../logic-apps/logic-apps-securing-a-logic-app.md#include-auth-header). | Gatilhos: <p>[Pedido,](#request-trigger) <br>[Webhook de HTTP](#http-webhook-trigger) | 
| `Sequential` | String | Executar iterações em loop "para cada um" uma de cada vez, em vez de todas ao mesmo tempo em paralelo. <p>Esta opção funciona da mesma forma que a definição do `runtimeConfiguration.concurrency.repetitions` imóvel para `1` . Pode definir qualquer uma das propriedades, mas não as duas. <p><p>Para definir esta opção, consulte ["Run for each" loops sequencialmente](#sequential-for-each).| Ação: <p>[Foreach](#foreach-action) | 
| `SingleInstance` | String | Executar o gatilho para cada instância de aplicação lógica sequencialmente e aguarde que a execução anteriormente ativa termine antes de desencadear a próxima instância da aplicação lógica. <p><p>Esta opção funciona da mesma forma que a definição do `runtimeConfiguration.concurrency.runs` imóvel para `1` . Pode definir qualquer uma das propriedades, mas não as duas. <p>Para definir esta opção, consulte [as instâncias Trigger sequencialmente](#sequential-trigger). | Todos os gatilhos | 
||||

<a name="change-trigger-concurrency"></a>

### <a name="change-trigger-concurrency"></a>Alterar simultaneidade dos acionadores

Por predefinição, as instâncias de fluxo de aplicações lógicas são executadas ao mesmo tempo (simultaneamente ou em paralelo). Este comportamento significa que cada exemplo de gatilho dispara antes que a instância de fluxo de trabalho anteriormente ativa termine de funcionar. No entanto, o número de instâncias em execução simultânea tem um [limite por defeito](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Quando o número de casos de fluxo de trabalho em execução simultânea atinge este limite, quaisquer outras instâncias novas devem esperar para ser executadas. Este limite ajuda a controlar o número de pedidos que os sistemas de backend recebem.

Quando liga o controlo de concordância do gatilho, as instâncias de disparo são executadas paralelamente até ao [limite predefinido](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Para alterar este limite de concordância padrão, pode utilizar o editor de visualização de código ou o Logic Apps Designer porque alterar a definição de concordância através do designer adiciona ou atualiza a `runtimeConfiguration.concurrency.runs` propriedade na definição de gatilho subjacente e vice-versa. Esta propriedade controla o número máximo de novos casos de fluxo de trabalho que podem funcionar em paralelo.

Aqui ficam algumas considerações para quando pretender permitir a concordância num gatilho:

* Quando a concordância é ativada, o [limite SplitOn](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits) é significativamente reduzido para [as matrizes de debatching](#split-on-debatch). Se o número de itens exceder este limite, a capacidade SplitOn é desativada.

* Não pode desativar a concordância depois de ativar o controlo da conuncy.

* Quando a concurrency está ativada, uma instância lógica de longa duração pode fazer com que novas instâncias de aplicações lógicas entrem num estado de espera. Este estado impede que as Azure Logic Apps criem novos casos e acontece mesmo quando o número de execuções simultâneas é inferior ao número máximo especificado de execuções simultâneas.

  * Para interromper este estado, cancele as primeiras ocorrências que ainda estão *em execução.*

    1. No menu da sua aplicação lógica, selecione **Overview**.

    1. Na secção **história de Runs,** selecione a primeira instância que ainda está em execução, por exemplo:

       ![Selecione a primeira instância de execução](./media/logic-apps-workflow-actions-triggers/waiting-runs.png)

       > [!TIP]
       > Para ver apenas as instâncias que ainda estão em execução, abra a lista **All** e selecione **Running**.

    1. No **âmbito da aplicação Logic,** selecione **Cancelar a execução**.

       ![Encontre o mais antigo exemplo de execução](./media/logic-apps-workflow-actions-triggers/cancel-run.png)

  * Para contornar esta possibilidade, adicione um tempo limite a qualquer ação que possa manter estas corridas. Se estiver a trabalhar no editor de código, consulte [a duração assíncronea change](#asynchronous-limits). Caso contrário, se estiver a usar o designer, siga estes passos:

    1. Na sua aplicação lógica, na ação em que pretende adicionar um intervalo, no canto superior direito, selecione o botão elipses **(...**) e, em seguida, selecione **Definições**.

       ![Abrir configurações de ação](./media/logic-apps-workflow-actions-triggers/action-settings.png)

    1. No **tempo limite**, especifique a duração do tempo limite no formato [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations).

       ![Especificar a duração do tempo limite](./media/logic-apps-workflow-actions-triggers/timeout.png)

* Para executar a sua aplicação lógica de forma sequencial, desaprote a concordância do gatilho `1` quer através da utilização do editor de visualização de códigos ou do designer. Certifique-se de que também não define a propriedade do gatilho `operationOptions` `SingleInstance` no editor de visualização de código. Caso contrário, terá um erro de validação. Para obter mais informações, consulte [os casos do Gatilho sequencialmente](#sequential-trigger).

#### <a name="edit-in-code-view"></a>Editar na vista de código 

Na definição subjacente do gatilho, adicione a `runtimeConfiguration.concurrency.runs` propriedade, que pode ter um valor que vai `1` `50` desde.

Aqui está um exemplo que limita simultaneamente 10 instâncias:

```json
"<trigger-name>": {
   "type": "<trigger-name>",
   "recurrence": {
      "frequency": "<time-unit>",
      "interval": <number-of-time-units>,
   },
   "runtimeConfiguration": {
      "concurrency": {
         "runs": 10
      }
   }
}
```

Para obter mais informações, consulte [as definições de configuração do tempo de execução](#runtime-config-options).

#### <a name="edit-in-logic-apps-designer"></a>Editar em Designer de Apps Lógicas

1. No canto superior direito do gatilho, selecione o botão elipses **(...**) e, em seguida, selecione **Definições**.

1. Sob **controlo de concurrency,** definir **limite** para **on**. 

1. Arraste o **curso de paralelismo** para o valor que deseja. Para executar a sua aplicação lógica sequencialmente, arraste o valor do slider para **1**.

<a name="change-for-each-concurrency"></a>

### <a name="change-for-each-concurrency"></a>Alterar a concordância "para cada um"

Por predefinição, as iterações em loop "para cada um" são executadas ao mesmo tempo (simultaneamente ou paralelamente). Este comportamento significa que cada iteração começa a funcionar antes que a iteração anterior termine de funcionar. No entanto, o número de iterações em execução simultânea tem um [limite por defeito](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Quando o número de iterações em execução simultânea atinge este limite, quaisquer outras iterações devem esperar para ser executadas.

Para alterar o limite padrão, pode utilizar o editor de visualização de código ou o Logic Apps Designer porque alterar a definição de concurrency através do designer adiciona ou atualiza a `runtimeConfiguration.concurrency.repetitions` propriedade na definição de ação subjacente "para cada" definição de ação e vice-versa. Esta propriedade controla o número máximo de iterações que podem ser executadas em paralelo.

> [!NOTE] 
> Se definir a ação "para cada um" para funcionar sequencialmente, quer utilizando o designer quer o editor de visualização de código, não desista da propriedade da ação `operationOptions` para o editor de `Sequential` visualização de código. Caso contrário, terá um erro de validação. Para obter mais informações, consulte ["for each" loops sequencialmente](#sequential-for-each).

#### <a name="edit-in-code-view"></a>Editar na vista de código 

Na definição subjacente "para cada um", adicione ou atualize o `runtimeConfiguration.concurrency.repetitions` imóvel, que pode ter um valor que varia de `1` e `50` .

Aqui está um exemplo que limita as 10 iterações simultâneas:

```json
"For_each" {
   "type": "Foreach",
   "actions": { "<actions-to-run>" },
   "foreach": "<for-each-expression>",
   "runAfter": {},
   "runtimeConfiguration": {
      "concurrency": {
         "repetitions": 10
      }
   }
}
```

Para obter mais informações, consulte [as definições de configuração do tempo de execução](#runtime-config-options).

#### <a name="edit-in-logic-apps-designer"></a>Editar em Designer de Apps Lógicas

1. No **botão Para cada** ação, a partir do canto superior direito, selecione o botão elipses **(...**) e, em seguida, selecione **Definições**.

1. Sob **controlo de concurrency,** desconfiem **do Controlo de Concurrency** **para On**.

1. Arraste o **curso de paralelismo** para o valor que deseja. Para executar a sua aplicação lógica sequencialmente, arraste o valor do slider para **1**.

<a name="change-waiting-runs"></a>

### <a name="change-waiting-runs-limit"></a>Alterar limite de corridas de espera

Por predefinição, as instâncias de fluxo de aplicações lógicas são executadas ao mesmo tempo (simultaneamente ou em paralelo). Este comportamento significa que cada exemplo de gatilho dispara antes que a instância de fluxo de trabalho anteriormente ativa termine de funcionar. No entanto, o número de instâncias em execução simultânea tem um [limite por defeito](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Quando o número de casos de fluxo de trabalho em execução simultânea atinge este limite, quaisquer outras instâncias novas devem esperar para ser executadas.

O número de execuções de espera também tem um [limite por defeito.](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits) Quando o número de corridas de espera atinge este limite, o motor De Aplicações Lógicas já não aceita novas corridas. Pedido e webhook disparam 429 erros, e os gatilhos recorrentes começam a saltar as tentativas de sondagens.

Não só pode [alterar o limite de predefinição no gatilho,](#change-trigger-concurrency)como também pode alterar o limite padrão nas execuções de espera. Na definição subjacente do gatilho, adicione a `runtimeConfiguration.concurrency.maximumWaitingRuns` propriedade, que pode ter um valor que vai `1` `100` desde.

```json
"<trigger-name>": {
   "type": "<trigger-name>",
   "recurrence": {
      "frequency": "<time-unit>",
      "interval": <number-of-time-units>,
   },
   "runtimeConfiguration": {
      "concurrency": {
         "maximumWaitingRuns": 50
      }
   }
}
```

Para obter mais informações, consulte [as definições de configuração do tempo de execução](#runtime-config-options).

<a name="sequential-trigger"></a>

### <a name="trigger-instances-sequentially"></a>Desencadear casos sequencialmente

Para executar cada instância de fluxo de aplicação lógica apenas após o final da instância anterior, desacorra o gatilho para funcionar sequencialmente. Pode utilizar o editor de visualização de código ou o Logic Apps Designer porque alterar a definição de concuência através do designer também adiciona ou atualiza a `runtimeConfiguration.concurrency.runs` propriedade na definição de gatilho subjacente e vice-versa.

> [!NOTE] 
> Quando configurar um gatilho para funcionar sequencialmente, utilizando o designer ou o editor de visualização de código, não desista da propriedade do gatilho `operationOptions` para o editor de `Sequential` visualização de código. Caso contrário, terá um erro de validação. 

#### <a name="edit-in-code-view"></a>Editar na vista de código

Na definição do gatilho, desafine qualquer uma destas propriedades, mas não ambas. 

Definir a `runtimeConfiguration.concurrency.runs` propriedade `1` para:

```json
"<trigger-name>": {
   "type": "<trigger-name>",
   "recurrence": {
      "frequency": "<time-unit>",
      "interval": <number-of-time-units>,
   },
   "runtimeConfiguration": {
      "concurrency": {
         "runs": 1
      }
   }
}
```

*- ou...*

Definir a `operationOptions` propriedade `SingleInstance` para:

```json
"<trigger-name>": {
   "type": "<trigger-name>",
   "recurrence": {
      "frequency": "<time-unit>",
      "interval": <number-of-time-units>,
   },
   "operationOptions": "SingleInstance"
}
```

Para obter mais informações, consulte [as definições de configuração do tempo de execução](#runtime-config-options) e [as opções de Funcionamento](#operation-options).

#### <a name="edit-in-logic-apps-designer"></a>Editar em Designer de Apps Lógicas

1. No canto superior direito do gatilho, selecione o botão elipses **(...**) e, em seguida, selecione **Definições**.

1. Sob **controlo de concurrency,** definir **limite** para **on**. 

1. Arraste o **curso de paralelismo** para o número `1` . 

<a name="sequential-for-each"></a>

### <a name="run-for-each-loops-sequentially"></a>Executar loops "para cada" sequencialmente

Para executar uma iteração em loop "para cada" só após o final da iteração anterior, desacorra a ação "para cada" para funcionar sequencialmente. Você pode usar o editor de visualização de código ou o Logic Apps Designer porque alterar a concordância da ação através do designer também adiciona ou atualiza a `runtimeConfiguration.concurrency.repetitions` propriedade na definição de ação subjacente e vice-versa.

> [!NOTE] 
> Quando definir uma ação "para cada um" para executar sequencialmente, quer utilizando o editor de visualização de código, não desista da propriedade da ação `operationOptions` para o editor de `Sequential` visualização de código. Caso contrário, terá um erro de validação. 

#### <a name="edit-in-code-view"></a>Editar na vista de código

Na definição de ação, desafine qualquer uma destas propriedades, mas não ambas. 

Definir a `runtimeConfiguration.concurrency.repetitions` propriedade `1` para:

```json
"For_each" {
   "type": "Foreach",
   "actions": { "<actions-to-run>" },
   "foreach": "<for-each-expression>",
   "runAfter": {},
   "runtimeConfiguration": {
      "concurrency": {
         "repetitions": 1
      }
   }
}
```

*- ou...*

Definir a `operationOptions` propriedade `Sequential` para:

```json
"For_each" {
   "type": "Foreach",
   "actions": { "<actions-to-run>" },
   "foreach": "<for-each-expression>",
   "runAfter": {},
   "operationOptions": "Sequential"
}
```

Para obter mais informações, consulte [as definições de configuração do tempo de execução](#runtime-config-options) e [as opções de Funcionamento](#operation-options).

#### <a name="edit-in-logic-apps-designer"></a>Editar em Designer de Apps Lógicas

1. No canto superior direito de **cada** ação, selecione o botão elipses **(...**) e, em seguida, selecione **Definições**.

1. Sob **controlo de concurrency,** desconfiem **do Controlo de Concurrency** **para On**.

1. Arraste o **curso de paralelismo** para o número `1` .

<a name="disable-asynchronous-pattern"></a>

### <a name="run-actions-in-a-synchronous-operation-pattern"></a>Executar ações em um padrão de operação sincronizado

Por predefinição, as ações HTTP action e APIConnection em Azure Logic Apps seguem o [*padrão de funcionamento assíncrono*](/azure/architecture/patterns/async-request-reply), enquanto a ação Resposta segue o *padrão de funcionamento sincronizado*. O padrão assíncrono especifica que após uma chamada de ação ou envia um pedido para o ponto final especificado, serviço, sistema ou API, o recetor devolve imediatamente uma resposta ["202 ACCEPTED".](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html#sec10.2.3) Este código confirma que o recetor aceitou o pedido mas ainda não terminou o processamento. A resposta pode incluir um `location` cabeçalho que especifica o URL e um ID de atualização que o chamador pode usar para pesquisar continuamente ou verificar o estado do pedido assíncrona até que o recetor pare de processar e devolva uma resposta de sucesso ["200 OK"](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html#sec10.2.1) ou outra resposta não-202. Para mais informações, consulte [a integração de microserviços Asynchronous que impõe autonomia de microserviços.](/azure/architecture/microservices/design/interservice-communication#synchronous-versus-asynchronous-messaging)

* No Logic App Designer, a ação HTTP, a ações APIConnection e Response têm a **definição de Padrão Assíncrono.** Quando ativado, esta definição especifica que o chamador não espera que o processamento termine e pode passar para a próxima ação, mas continua a verificar o estado até que o processamento pare. Se desativado, esta definição especifica que o chamador aguarda que o processamento termine antes de passar para a ação seguinte. Para encontrar esta definição, siga estes passos:

  1. Na barra de títulos http action, selecione o botão elipses **(...**) que abre as definições da ação.

  1. Encontre a **definição de padrão assíncronos.**

     ![Definição de "Padrão Assíncronos"](./media/logic-apps-workflow-actions-triggers/asynchronous-pattern-setting.png)

* Na definição subjacente da Notação de Objetos JavaScript (JSON), as ações http action e APIConnection seguem implicitamente o padrão de funcionamento assíncrono.

Em alguns cenários, talvez queira uma ação para seguir o padrão sincronizado. Por exemplo, quando utilizar a ação HTTP, é melhor:

* [Evite intervalos de tempo HTTP para tarefas de longa duração](../connectors/connectors-native-http.md#avoid-http-timeouts)
* [Desativar cabeçalhos de localização de verificação](../connectors/connectors-native-http.md#disable-location-header-check)

Nestes casos, pode fazer uma ação sincronizada utilizando estas opções:

* Substitua a versão de sondagem dessa ação por uma versão webhook, se disponível.

* Desative o comportamento assíncronos da ação seguindo qualquer uma das opções:

  * No Design de Aplicativos Lógica, [desligue a **definição de padrão assíncronos**](#turn-off-asynchronous-pattern-setting).

  * Na definição JSON subjacente da ação, [adicione a `"DisableAsyncPattern"` opção de funcionamento](#add-disable-async-pattern-option).

<a name="turn-off-asynchronous-pattern-setting"></a>

#### <a name="turn-off-asynchronous-pattern-setting"></a>Desligue **a definição de padrão assíncronos**

1. No Logic App Designer, na barra de título da ação, selecione o botão elipses **(...**) que abre as definições da ação.

1. Encontre a **definição de padrão assíncronos,** ligue a definição para **desligar** se ativada e selecione **Feito**.

   ![Desligue a definição de "Padrão Assíncronos"](./media/logic-apps-workflow-actions-triggers/disable-asynchronous-pattern-setting.png)

<a name="add-disable-async-pattern-option"></a>

#### <a name="disable-asynchronous-pattern-in-actions-json-definition"></a>Desativar o padrão assíncronos na definição JSON da ação

Na definição JSON subjacente da ação, adicione e desateia a [propriedade "operationOptions"](#operation-options) na `"DisableAsyncPattern"` secção da `"inputs"` ação, por exemplo:

```json
"<some-long-running-action>": {
   "type": "Http",
   "inputs": { "<action-inputs>" },
   "operationOptions": "DisableAsyncPattern",
   "runAfter": {}
}
```

<a name="authenticate-triggers-actions"></a>

## <a name="authenticate-triggers-and-actions"></a>Autenticar gatilhos e ações

Os pontos finais HTTP e HTTPS suportam diferentes tipos de autenticação. Com base no gatilho ou ação que utiliza para escoar chamadas ou pedidos de acesso a estes pontos finais, pode selecionar entre diferentes gamas de tipos de autenticação. Para obter mais informações, consulte [Adicionar autenticação às chamadas de saída](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound).

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre [linguagem de definição de fluxo de trabalho](../logic-apps/logic-apps-workflow-definition-language.md)