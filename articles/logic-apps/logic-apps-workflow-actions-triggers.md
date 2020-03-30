---
title: Referência de esquema para tipos de gatilho e ação
description: Guia de referência schema para o gatilho de definição de fluxo de trabalho e tipos de ação em Aplicativos lógicos Azure
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 01/19/2020
ms.openlocfilehash: 18e9c9d330ffb8cc4e284fc649cff0840ec2c82c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79270371"
---
# <a name="schema-reference-guide-for-trigger-and-action-types-in-azure-logic-apps"></a>Guia de referência schema para tipos de gatilho e ação em Aplicativos Lógicos Azure

Esta referência descreve os tipos gerais utilizados para identificar gatilhos e ações na definição subjacente de fluxo de trabalho da sua aplicação lógica, que é descrita e validada pela [Linguagem definição](../logic-apps/logic-apps-workflow-definition-language.md)de fluxo de trabalho . Para encontrar gatilhos e ações específicas de conector que possa utilizar nas suas aplicações lógicas, consulte a lista sob a visão geral dos [Conectores](https://docs.microsoft.com/connectors/).

<a name="triggers-overview"></a>

## <a name="triggers-overview"></a>Triggers visão geral

Cada fluxo de trabalho inclui um gatilho, que define as chamadas que instantaneamente iniciam o fluxo de trabalho. Aqui estão as categorias gerais de gatilho:

* Um gatilho *de sondagens,* que verifica o ponto final de um serviço em intervalos regulares

* Um gatilho de *pressão,* que cria uma subscrição de um ponto final e fornece um *URL de callback* para que o ponto final possa notificar o gatilho quando o evento especificado acontecer ou os dados estiverem disponíveis. O gatilho aguarda então a resposta do ponto final antes de disparar.

Os gatilhos têm estes elementos de nível superior, embora alguns sejam opcionais:  
  
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

*Necessário*

| Valor | Tipo | Descrição | 
|-------|------|-------------| 
| <*nome do gatilho*> | Cadeia | O nome do gatilho | 
| <*tipo de gatilho*> | Cadeia | O tipo de gatilho, como "Http" ou "ApiConnection" | 
| <*trigger-inputs*> | Objeto JSON | As inputs que definem o comportamento do gatilho | 
| <*unidade de tempo*> | Cadeia | A unidade de tempo que descreve com que frequência o gatilho dispara: "Segundo", "Minuto", "Hora", "Dia", "Semana", "Mês" | 
| <*número de unidades de tempo*> | Número inteiro | Um valor que especifica com que frequência o gatilho dispara com base na frequência, que é o número de unidades de tempo para esperar até que o gatilho volte a disparar. <p>Aqui estão os intervalos mínimos e máximos: <p>- Mês: 1-16 meses </br>- Dia: 1-500 dias </br>- Hora: 1-12.000 horas </br>- Minuto: 1-72.000 minutos </br>- Segundo: 1-9.999,999 segundos<p>Por exemplo, se o intervalo for 6, e a frequência for "Mês", a recorrência é a cada 6 meses. | 
|||| 

*Opcional*

| Valor | Tipo | Descrição | 
|-------|------|-------------| 
| <*matriz-com-condições*> | Matriz | Uma matriz que contém uma ou mais [condições](#trigger-conditions) que determinam se devem ou não executar o fluxo de trabalho. Disponível apenas para gatilhos. | 
| <*prazo-config-opções*> | Objeto JSON | Pode alterar o comportamento do `runtimeConfiguration` tempo de execução do gatilho, definindo propriedades. Para mais informações, consulte as definições de [configuração do Tempo de Execução](#runtime-config-options). | 
| <*splitOn-expressão*> | Cadeia | Para os gatilhos que devolvem uma matriz, pode especificar uma expressão que [divide ou *debatcha* ](#split-on-debatch) itens de matriz em várias instâncias de fluxo de trabalho para processamento. | 
| <*operação-opção*> | Cadeia | Pode alterar o comportamento padrão `operationOptions` definindo a propriedade. Para mais informações, consulte [opções de operação.](#operation-options) | 
|||| 

## <a name="trigger-types-list"></a>Lista de tipos de gatilho

Cada tipo de gatilho tem uma interface e inputs diferentes que definem o comportamento do gatilho. 

### <a name="built-in-triggers"></a>Gatilhos incorporados

| Tipo de acionador | Descrição | 
|--------------|-------------| 
| [**HTTP**](#http-trigger) | Cheques ou *sondagens* qualquer ponto final. Este ponto final deve estar em conformidade com um contrato específico de gatilho, quer utilizando um padrão assíncrono "202" quer devolvendo uma matriz. | 
| [**HTTPWebhook**](#http-webhook-trigger) | Cria um ponto final calivel para a sua aplicação lógica, mas chama o URL especificado para registar ou não registar. |
| [**Recorrência**](#recurrence-trigger) | Incêndios baseados num horário definido. Pode definir uma data e hora futuras para disparar este gatilho. Com base na frequência, também pode especificar horários e dias para executar o seu fluxo de trabalho. | 
| [**Pedido**](#request-trigger)  | Cria um ponto final calivel para a sua aplicação lógica e também é conhecido como um gatilho "manual". Por exemplo, consulte [Call, trigger ou nest workflows com pontos finais HTTP](../logic-apps/logic-apps-http-endpoint.md). | 
||| 

### <a name="managed-api-triggers"></a>Gatilhos de API geridos

| Tipo de acionador | Descrição | 
|--------------|-------------| 
| [**ApiConnection**](#apiconnection-trigger) | Verifica ou *sondagens* um ponto final utilizando [APIs geridos pela Microsoft](../connectors/apis-list.md). | 
| [**ApiConnectionWebhook**](#apiconnectionwebhook-trigger) | Cria um ponto final calivel para a sua aplicação lógica, ligando para [apis geridos pela Microsoft](../connectors/apis-list.md) para subscrever e cancelar a subscrição. | 
||| 

## <a name="triggers---detailed-reference"></a>Acionadores – Referência detalhada

<a name="apiconnection-trigger"></a>

### <a name="apiconnection-trigger"></a>Gatilho APIConnection  

Este gatilho verifica ou *dispara* um ponto final utilizando [APIs geridos pela Microsoft](../connectors/apis-list.md) para que os parâmetros para este gatilho possam diferir com base no ponto final. Muitas secções nesta definição de gatilho são opcionais. O comportamento do gatilho depende se as secções estão ou não incluídas.

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

*Necessário*

| Valor | Tipo | Descrição |
|-------|------|-------------|
| <*APIConnection_trigger_name*> | Cadeia | O nome do gatilho |
| <*nome de ligação*> | Cadeia | O nome para a ligação à API gerida que o fluxo de trabalho utiliza |
| <*método-tipo*> | Cadeia | O método HTTP para comunicar com a API gerida: "GET", "PUT", "POST", "PATCH", "DELETE" |
| <*api-operação*> | Cadeia | A operação da API para chamar |
| <*unidade de tempo*> | Cadeia | A unidade de tempo que descreve com que frequência o gatilho dispara: "Segundo", "Minuto", "Hora", "Dia", "Semana", "Mês" |
| <*número de unidades de tempo*> | Número inteiro | Um valor que especifica com que frequência o gatilho dispara com base na frequência, que é o número de unidades de tempo para esperar até que o gatilho volte a disparar. <p>Aqui estão os intervalos mínimos e máximos: <p>- Mês: 1-16 meses </br>- Dia: 1-500 dias </br>- Hora: 1-12.000 horas </br>- Minuto: 1-72.000 minutos </br>- Segundo: 1-9.999,999 segundos<p>Por exemplo, se o intervalo for 6, e a frequência for "Mês", a recorrência é a cada 6 meses. |
||||

*Opcional*

| Valor | Tipo | Descrição | 
|-------|------|-------------| 
| <*retry-comportamento*> | Objeto JSON | Personaliza o comportamento de retry para falhas intermitentes, que têm o código de estado de 408, 429 e 5XX, e quaisquer exceções à conectividade. Para mais informações, consulte [as políticas de Retry](../logic-apps/logic-apps-exception-handling.md#retry-policies). | 
| <*parâmetros de consulta*> | Objeto JSON | Quaisquer parâmetros de consulta para incluir com a chamada da API. Por exemplo, `"queries": { "api-version": "2018-01-01" }` o `?api-version=2018-01-01` objeto adiciona à chamada. | 
| <*max-runs*> | Número inteiro | Por defeito, as instâncias de fluxo de trabalho são executadas ao mesmo tempo (simultaneamente ou paralelamente) até ao [limite de predefinição](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Para alterar este limite, estabelecendo um novo <*contagem*> valor, consulte alterar a [conmoeda](#change-trigger-concurrency)do gatilho . | 
| <*max-runs-queue*> | Número inteiro | Quando o seu fluxo de trabalho já está a executar o `runtimeConfiguration.concurrency.runs` número máximo de casos, que pode alterar com base na propriedade, quaisquer novas correções são colocadas nesta fila até ao [limite de predefinição](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Para alterar o limite de predefinição, consulte alterar o limite de [corridas](#change-waiting-runs)de espera . | 
| <*splitOn-expressão*> | Cadeia | Para os gatilhos que devolvem matrizes, esta expressão refere a matriz a utilizar para que possa criar e executar uma instância de fluxo de trabalho para cada item de matriz, em vez de usar um laço "para cada". <p>Por exemplo, esta expressão representa um item na matriz devolvida dentro do conteúdo corporal do gatilho:`@triggerbody()?['value']` |
| <*operação-opção*> | Cadeia | Pode alterar o comportamento padrão `operationOptions` definindo a propriedade. Para mais informações, consulte [opções de operação.](#operation-options) |
||||

*Saídas*
 
| Elemento | Tipo | Descrição |
|---------|------|-------------|
| cabeçalhos | Objeto JSON | Os cabeçalhos da resposta |
| body | Objeto JSON | O corpo da resposta |
| código de estado | Número inteiro | O código de estado da resposta |
|||| 

*Exemplo*

Esta definição de gatilho verifica o e-mail todos os dias dentro da caixa de entrada para uma conta do Office 365 Outlook:

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

Este gatilho envia um pedido de subscrição para um ponto final utilizando um [API gerido pela Microsoft,](../connectors/apis-list.md)fornece um URL de *callback* para onde o ponto final pode enviar uma resposta, e aguarda que o ponto final responda. Para mais informações, consulte [as subscrições do Endpoint.](#subscribe-unsubscribe)

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

*Necessário*

| Valor | Tipo | Descrição | 
|-------|------|-------------| 
| <*nome de ligação*> | Cadeia | O nome para a ligação à API gerida que o fluxo de trabalho utiliza | 
| <*conteúdo corporal*> | Objeto JSON | Qualquer conteúdo de mensagem para enviar como carga útil para a API gerida | 
|||| 

*Opcional*

| Valor | Tipo | Descrição | 
|-------|------|-------------| 
| <*retry-comportamento*> | Objeto JSON | Personaliza o comportamento de retry para falhas intermitentes, que têm o código de estado de 408, 429 e 5XX, e quaisquer exceções à conectividade. Para mais informações, consulte [as políticas de Retry](../logic-apps/logic-apps-exception-handling.md#retry-policies). | 
| <*parâmetros de consulta*> | Objeto JSON | Quaisquer parâmetros de consulta para incluir com a chamada da API <p>Por exemplo, `"queries": { "api-version": "2018-01-01" }` o `?api-version=2018-01-01` objeto adiciona à chamada. | 
| <*max-runs*> | Número inteiro | Por defeito, as instâncias de fluxo de trabalho são executadas ao mesmo tempo (simultaneamente ou paralelamente) até ao [limite de predefinição](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Para alterar este limite, estabelecendo um novo <*contagem*> valor, consulte alterar a [conmoeda](#change-trigger-concurrency)do gatilho . | 
| <*max-runs-queue*> | Número inteiro | Quando o seu fluxo de trabalho já está a executar o `runtimeConfiguration.concurrency.runs` número máximo de casos, que pode alterar com base na propriedade, quaisquer novas correções são colocadas nesta fila até ao [limite de predefinição](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Para alterar o limite de predefinição, consulte alterar o limite de [corridas](#change-waiting-runs)de espera . | 
| <*splitOn-expressão*> | Cadeia | Para os gatilhos que devolvem matrizes, esta expressão refere a matriz a utilizar para que possa criar e executar uma instância de fluxo de trabalho para cada item de matriz, em vez de usar um laço "para cada". <p>Por exemplo, esta expressão representa um item na matriz devolvida dentro do conteúdo corporal do gatilho:`@triggerbody()?['value']` |
| <*operação-opção*> | Cadeia | Pode alterar o comportamento padrão `operationOptions` definindo a propriedade. Para mais informações, consulte [opções de operação.](#operation-options) | 
|||| 

*Exemplo*

Esta definição de gatilho subscreve o Office 365 Outlook API, fornece um URL de callback para o ponto final da API, e aguarda que o ponto final responda quando um novo e-mail chegar.

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

Este gatilho envia um pedido para o ponto final http ou HTTPS especificado com base no calendário de recorrência especificado. Em seguida, o gatilho verifica a resposta para determinar se o fluxo de trabalho funciona.

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

*Necessário*

| Propriedade | Valor | Tipo | Descrição |
|----------|-------|------|-------------|
| `method` | <*método-tipo*> | Cadeia | O método a utilizar para o envio do pedido de saída: "GET", "PUT", "POST", "PATCH" ou "DELETE" |
| `uri` | <*HTTP-ou-HTTPS-endpoint-URL*> | Cadeia | O URL de ponto final HTTP ou HTTPS onde pretende enviar o pedido de saída. Tamanho máximo da corda: 2 KB <p>Para um serviço ou recurso Azure, esta sintaxe URI inclui o ID de recurso e o caminho para o recurso a que pretende aceder. |
| `frequency` | <*unidade de tempo*> | Cadeia | A unidade de tempo que descreve com que frequência o gatilho dispara: "Segundo", "Minuto", "Hora", "Dia", "Semana", "Mês" |
| `interval` | <*número de unidades de tempo*> | Número inteiro | Um valor que especifica com que frequência o gatilho dispara com base na frequência, que é o número de unidades de tempo para esperar até que o gatilho volte a disparar. <p>Aqui estão os intervalos mínimos e máximos: <p>- Mês: 1-16 meses </br>- Dia: 1-500 dias </br>- Hora: 1-12.000 horas </br>- Minuto: 1-72.000 minutos </br>- Segundo: 1-9.999,999 segundos<p>Por exemplo, se o intervalo for 6, e a frequência for "Mês", a recorrência é a cada 6 meses. |
|||||

*Opcional*

| Propriedade | Valor | Tipo | Descrição |
|----------|-------|------|-------------|
| `headers` | <*conteúdo de cabeçalho*> | Objeto JSON | Quaisquer cabeçalhos que você precisa incluir com o pedido <p>Por exemplo, para definir o idioma e o tipo: <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` |
| `queries` | <*parâmetros de consulta*> | Objeto JSON | Quaisquer parâmetros de consulta que você precisa usar no pedido <p>Por exemplo, `"queries": { "api-version": "2018-01-01" }` o `?api-version=2018-01-01` objeto adiciona ao pedido. |
| `body` | <*conteúdo corporal*> | Objeto JSON | O conteúdo da mensagem para enviar como carga útil com o pedido |
| `authentication` | <*autenticação-tipo-e-propriedade-valores*> | Objeto JSON | O modelo de autenticação que o pedido utiliza para autenticar pedidos de saída. Para mais informações, consulte [Adicionar autenticação a chamadas de saída](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound). Além do `authority` Scheduler, a propriedade é suportada. Quando não especificado, o `https://management.azure.com/`valor predefinido é, mas pode utilizar um valor diferente. |
| `retryPolicy` > `type` | <*retry-comportamento*> | Objeto JSON | Personaliza o comportamento de retry para falhas intermitentes, que têm o código de estado de 408, 429 e 5XX, e quaisquer exceções à conectividade. Para mais informações, consulte [as políticas de Retry](../logic-apps/logic-apps-exception-handling.md#retry-policies). |
| `runs` | <*max-runs*> | Número inteiro | Por defeito, as instâncias de fluxo de trabalho são executadas ao mesmo tempo (simultaneamente ou paralelamente) até ao [limite de predefinição](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Para alterar este limite, estabelecendo um novo <*contagem*> valor, consulte alterar a [conmoeda](#change-trigger-concurrency)do gatilho . |
| `maximumWaitingRuns` | <*max-runs-queue*> | Número inteiro | Quando o seu fluxo de trabalho já está a executar o `runtimeConfiguration.concurrency.runs` número máximo de casos, que pode alterar com base na propriedade, quaisquer novas correções são colocadas nesta fila até ao [limite de predefinição](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Para alterar o limite de predefinição, consulte alterar o limite de [corridas](#change-waiting-runs)de espera . |
| `operationOptions` | <*operação-opção*> | Cadeia | Pode alterar o comportamento padrão `operationOptions` definindo a propriedade. Para mais informações, consulte [opções de operação.](#operation-options) |
|||||

*Saídas*

| Elemento | Tipo | Descrição |
|---------|------|-------------| 
| cabeçalhos | Objeto JSON | Os cabeçalhos da resposta | 
| body | Objeto JSON | O corpo da resposta | 
| código de estado | Número inteiro | O código de estado da resposta | 
|||| 

*Requisitos para pedidos de entrada*

Para funcionar bem com a sua aplicação lógica, o ponto final deve estar em conformidade com um padrão ou contrato específico do gatilho, e reconhecer estas propriedades:  
  
| Resposta | Necessário | Descrição | 
|----------|----------|-------------| 
| Código de estado | Sim | O código de estado "200 OK" começa a ser executado. Qualquer outro código de estado não começa uma fuga. | 
| Cabeçalho de retry-after | Não | O número de segundos até que a sua aplicação lógica volte a ser o ponto final | 
| Cabeçalho de localização | Não | O URL para ligar no próximo intervalo de votação. Se não especificado, o URL original é utilizado. | 
|||| 

*Comportamentos de exemplo para diferentes pedidos*

| Código de estado | Voltar a tentar depois | Comportamento | 
|-------------|-------------|----------|
| 200 | {nenhum} | Executar o fluxo de trabalho e, em seguida, verificar novamente se há mais dados após a recorrência definida. | 
| 200 | 10 segundos | Executar o fluxo de trabalho e, em seguida, verificar novamente se há mais dados após 10 segundos. |  
| 202 | 60 segundos | Não desencadeie o fluxo de trabalho. A próxima tentativa acontece num minuto, sujeito à recorrência definida. Se a recorrência definida for inferior a um minuto, o cabeceamento de volta tem precedência. Caso contrário, a recorrência definida é utilizada. | 
| 400 | {nenhum} | Mau pedido, não faça sonorização. Se `retryPolicy` não for definido, então a política de incumprimento é utilizada. Após o número de tentativas ter sido atingido, o gatilho verifica novamente os dados após a recorrência definida. | 
| 500 | {nenhum}| Erro do servidor, não faça o fluxo de trabalho. Se `retryPolicy` não for definido, então a política de incumprimento é utilizada. Após o número de tentativas ter sido atingido, o gatilho verifica novamente os dados após a recorrência definida. | 
|||| 

<a name="http-webhook-trigger"></a>

### <a name="httpwebhook-trigger"></a>Gatilho HTTPWebhook  

Este gatilho torna a sua aplicação lógica callable através da criação de um ponto final que pode registar uma subscrição, ligando para o URL de ponto final especificado. Quando cria este gatilho no seu fluxo de trabalho, um pedido de saída faz a chamada para registar a subscrição. Assim, o gatilho pode começar a ouvir eventos. Quando uma operação torna este gatilho inválido, um pedido de saída faz automaticamente a chamada para cancelar a subscrição. Para mais informações, consulte [as subscrições do Endpoint.](#subscribe-unsubscribe)

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

Alguns valores, como <*tipo de* método `"subscribe"` `"unsubscribe"`>, estão disponíveis tanto para os objetos como para os objetos.

*Necessário*

| Valor | Tipo | Descrição | 
|-------|------|-------------| 
| <*método-tipo*> | Cadeia | O método HTTP a utilizar para o pedido de subscrição: "GET", "PUT", "POST", "PATCH" ou "DELETE" | 
| <*endpoint-subscribe-URL*> | Cadeia | O URL do ponto final para enviar o pedido de subscrição | 
|||| 

*Opcional*

| Valor | Tipo | Descrição | 
|-------|------|-------------| 
| <*método-tipo*> | Cadeia | O método HTTP a utilizar para o pedido de cancelamento: "GET", "PUT", "POST", "PATCH" ou "DELETE" | 
| <*endpoint-unsubscribe-URL*> | Cadeia | O URL do ponto final para enviar o pedido de cancelamento | 
| <*conteúdo corporal*> | Cadeia | Qualquer conteúdo de mensagem para enviar no pedido de subscrição ou cancelamento | 
| <*tipo de autenticação*> | Objeto JSON | O modelo de autenticação que o pedido utiliza para autenticar pedidos de saída. Para mais informações, consulte [Adicionar autenticação a chamadas de saída](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound). |
| <*retry-comportamento*> | Objeto JSON | Personaliza o comportamento de retry para falhas intermitentes, que têm o código de estado de 408, 429 e 5XX, e quaisquer exceções à conectividade. Para mais informações, consulte [as políticas de Retry](../logic-apps/logic-apps-exception-handling.md#retry-policies). | 
| <*max-runs*> | Número inteiro | Por defeito, as instâncias de fluxo de trabalho funcionam ao mesmo tempo (simultaneamente ou paralelamente) até ao limite de [predefinição](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Para alterar este limite, estabelecendo um novo <*contagem*> valor, consulte alterar a [conmoeda](#change-trigger-concurrency)do gatilho . | 
| <*max-runs-queue*> | Número inteiro | Quando o seu fluxo de trabalho já está a executar o `runtimeConfiguration.concurrency.runs` número máximo de casos, que pode alterar com base na propriedade, quaisquer novas correções são colocadas nesta fila até ao [limite de predefinição](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Para alterar o limite de predefinição, consulte alterar o limite de [corridas](#change-waiting-runs)de espera . | 
| <*operação-opção*> | Cadeia | Pode alterar o comportamento padrão `operationOptions` definindo a propriedade. Para mais informações, consulte [opções de operação.](#operation-options) | 
|||| 

*Saídas* 

| Elemento | Tipo | Descrição |
|---------|------|-------------| 
| cabeçalhos | Objeto JSON | Os cabeçalhos da resposta | 
| body | Objeto JSON | O corpo da resposta | 
| código de estado | Número inteiro | O código de estado da resposta | 
|||| 

*Exemplo*

Este gatilho cria uma subscrição do ponto final especificado, fornece um URL de callback único, e aguarda por artigos de tecnologia recentemente publicados.

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

Este gatilho funciona com base no calendário de recorrência especificado e fornece uma maneira fácil de criar um fluxo de trabalho regularmente em execução.

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

*Necessário*

| Valor | Tipo | Descrição | 
|-------|------|-------------| 
| <*unidade de tempo*> | Cadeia | A unidade de tempo que descreve com que frequência o gatilho dispara: "Segundo", "Minuto", "Hora", "Dia", "Semana", "Mês" | 
| <*número de unidades de tempo*> | Número inteiro | Um valor que especifica com que frequência o gatilho dispara com base na frequência, que é o número de unidades de tempo para esperar até que o gatilho volte a disparar. <p>Aqui estão os intervalos mínimos e máximos: <p>- Mês: 1-16 meses </br>- Dia: 1-500 dias </br>- Hora: 1-12.000 horas </br>- Minuto: 1-72.000 minutos </br>- Segundo: 1-9.999,999 segundos<p>Por exemplo, se o intervalo for 6, e a frequência for "Mês", a recorrência é a cada 6 meses. | 
|||| 

*Opcional*

| Valor | Tipo | Descrição | 
|-------|------|-------------| 
| <*início-data-hora-com-formato-YYYY-MM-DDThh:mm:ss*> | Cadeia | A data e a hora de início neste formato: <p>YYYY-MM-DDThh:mms se especificar um fuso horário <p>-ou- <p>YYYY-MM-DDThh:mm:ssZ se não especificar um fuso horário <p>Assim, por exemplo, se quiser 18 de setembro de 2017 às 14:00, especifique "2017-09-18T14:00:00" e especifique um fuso horário como "Pacific Standard Time", ou especifique "2017-09-18T14:00:00Z" sem fuso horário. <p>**Nota:** Esta hora de início tem um máximo de 49 anos no futuro e deve seguir a especificação de [data ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations) no [formato de data utc,](https://en.wikipedia.org/wiki/Coordinated_Universal_Time)mas sem [uma compensação UTC](https://en.wikipedia.org/wiki/UTC_offset). Se não especificar um fuso horário, deve adicionar a letra "Z" no final sem espaços. Este "Z" refere-se ao [tempo náutico](https://en.wikipedia.org/wiki/Nautical_time)equivalente . <p>Para horários simples, a hora de início é a primeira ocorrência, enquanto para horários complexos, o gatilho não dispara tão cedo quanto a hora de início. Para mais informações sobre datas e horários de início, consulte [Criar e agendar tarefas regulares.](../connectors/connectors-native-recurrence.md) | 
| <*fuso horário*> | Cadeia | Aplica-se apenas quando especifica um tempo de início porque este gatilho não aceita [compensação UTC](https://en.wikipedia.org/wiki/UTC_offset). Especifique o fuso horário que pretende aplicar. | 
| <*marca de uma ou mais horas*> | Matriz inteiro ou inteiro | Se especificar "Day" ou "Week" para `frequency`, pode especificar um ou mais inteiros de 0 a 23, separados por vírgulas, como as horas do dia em que pretende executar o fluxo de trabalho. <p>Por exemplo, se especificar "10", "12" e "14", terá 10:00, 12:00 e 14 horas como marca a hora. | 
| <*marcas de um ou mais minutos*> | Matriz inteiro ou inteiro | Se especificar "Day" ou "Week" para `frequency`, pode especificar um ou mais inteiros de 0 a 59, separados por vírgulas, como os minutos da hora em que pretende executar o fluxo de trabalho. <p>Por exemplo, pode especificar "30" como a marca de minutos e usando o exemplo anterior para horas do dia, você recebe 10:30 am, 12:30 pm e 14:30. | 
| weekDays | String ou string array | Se especificar "Semana" `frequency`para , pode especificar um ou mais dias, separados por vírgulas, quando quiser executar o fluxo de trabalho: "Segunda-feira", "terça", "quarta-feira", "quinta-feira", "sexta-feira", "sábado" e "domingo" | 
| <*max-runs*> | Número inteiro | Por defeito, as instâncias de fluxo de trabalho funcionam ao mesmo tempo (simultaneamente ou paralelamente) até ao limite de [predefinição](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Para alterar este limite, estabelecendo um novo <*contagem*> valor, consulte alterar a [conmoeda](#change-trigger-concurrency)do gatilho . | 
| <*max-runs-queue*> | Número inteiro | Quando o seu fluxo de trabalho já está a executar o `runtimeConfiguration.concurrency.runs` número máximo de casos, que pode alterar com base na propriedade, quaisquer novas correções são colocadas nesta fila até ao [limite de predefinição](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Para alterar o limite de predefinição, consulte alterar o limite de [corridas](#change-waiting-runs)de espera . | 
| <*operação-opção*> | Cadeia | Pode alterar o comportamento padrão `operationOptions` definindo a propriedade. Para mais informações, consulte [opções de operação.](#operation-options) | 
|||| 

*Exemplo 1*

Este gatilho básico de recorrência é executado diariamente:

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

Pode especificar a data e a hora de início para disparar o gatilho. Este gatilho de recorrência começa na data especificada e dispara diariamente:

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

Este gatilho de recorrência começa no dia 9 de setembro de 2017 às 14:00, e dispara semanalmente todas as segundas-feiras às 10:30, 12:30 e 14:30 hora padrão do Pacífico:

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

Para mais informações e exemplos para este gatilho, consulte [Criar e agendar tarefas regulares.](../connectors/connectors-native-recurrence.md)

<a name="request-trigger"></a>

### <a name="request-trigger"></a>Gatilho de pedido

Este gatilho torna a sua aplicação lógica callable criando um ponto final que pode aceitar pedidos de entrada. Para este gatilho, forneça um esquema JSON que descreva e valide a carga útil ou as inputs que o gatilho recebe do pedido de entrada. O esquema também facilita as propriedades do gatilho para a referência de ações posteriores no fluxo de trabalho.

Para chamar este gatilho, `listCallbackUrl` deve utilizar a API, descrita no Serviço de [Fluxo de Trabalho REST API](https://docs.microsoft.com/rest/api/logic/workflows). Para aprender a usar este gatilho como ponto final http, consulte [Call, trigger ou nest workflows com pontos finais HTTP](../logic-apps/logic-apps-http-endpoint.md).

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

*Necessário*

| Valor | Tipo | Descrição | 
|-------|------|-------------| 
| <*nome de propriedade*> | Cadeia | O nome de uma propriedade no esquema JSON, que descreve a carga útil | 
| <*tipo de propriedade*> | Cadeia | O tipo da propriedade | 
|||| 

*Opcional*

| Valor | Tipo | Descrição | 
|-------|------|-------------| 
| <*método-tipo*> | Cadeia | O método que os pedidos de entrada devem usar para chamar a sua app lógica: "GET", "PUT", "POST", "PATCH", "DELETE" |
| <*parâmetro relativo-caminho-para-aceite*> | Cadeia | O caminho relativo para o parâmetro que o URL do seu ponto final pode aceitar | 
| <*propriedades necessárias*> | Matriz | Uma ou mais propriedades que requerem valores | 
| <*max-runs*> | Número inteiro | Por defeito, as instâncias de fluxo de trabalho funcionam ao mesmo tempo (simultaneamente ou paralelamente) até ao limite de [predefinição](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Para alterar este limite, estabelecendo um novo <*contagem*> valor, consulte alterar a [conmoeda](#change-trigger-concurrency)do gatilho . | 
| <*max-runs-queue*> | Número inteiro | Quando o seu fluxo de trabalho já está a executar o `runtimeConfiguration.concurrency.runs` número máximo de casos, que pode alterar com base na propriedade, quaisquer novas correções são colocadas nesta fila até ao [limite de predefinição](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Para alterar o limite de predefinição, consulte alterar o limite de [corridas](#change-waiting-runs)de espera . | 
| <*operação-opção*> | Cadeia | Pode alterar o comportamento padrão `operationOptions` definindo a propriedade. Para mais informações, consulte [opções de operação.](#operation-options) | 
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

Para qualquer gatilho, e apenas gatilhos, pode incluir uma matriz que contém uma ou mais expressões para condições que determinam se o fluxo de trabalho deve ser executado. Para adicionar `conditions` a propriedade a um gatilho no seu fluxo de trabalho, abra a sua aplicação lógica no editor de visualização de código.

Por exemplo, pode especificar que um gatilho dispara apenas quando um site devolve um `conditions` erro interno do servidor, referindo o código de estado do gatilho na propriedade:

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

Por padrão, um gatilho dispara apenas depois de obter uma resposta de "200 OK". Quando uma expressão refere o código de estado de um gatilho, o comportamento padrão do gatilho é substituído. Por isso, se quiser que o gatilho dispare por mais de um código de estado, como o código de estado "200" e "201", deve incluir esta expressão como sua condição:

`@or(equals(triggers().code, 200),equals(triggers().code, 201))` 

<a name="split-on-debatch"></a>

## <a name="trigger-multiple-runs"></a>Várias execuções do acionador

Se o gatilho devolver uma matriz para a sua aplicação lógica processar, às vezes um loop "para cada" pode demorar muito tempo a processar cada item de matriz. Em vez disso, pode usar a propriedade **SplitOn** no seu gatilho para *desbatchar* a matriz. O debatching divide os itens da matriz e inicia uma nova instância de fluxo de trabalho que corre para cada item de matriz. Esta abordagem é útil, por exemplo, quando se pretende fazer sondagens a um ponto final que possa devolver vários novos itens entre intervalos de sondagens. Para o número máximo de itens de matriz que **splitOn** pode processar numa única aplicação lógica, consulte [Limites e configuração](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). 

> [!NOTE]
> Não podes usar o **SplitOn** com um padrão de resposta sincronizado. Qualquer fluxo de trabalho que utilize **splitOn** e inclua uma ação de `202 ACCEPTED` resposta corre assíncronamente e envia imediatamente uma resposta.
>
> Quando a moeda do gatilho está ativada, o [limite SplitOn](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits) é significativamente reduzido. Se o número de itens exceder este limite, a capacidade SplitOn é desativada.
 
Se o ficheiro Swagger do seu gatilho descrever uma carga útil que é uma matriz, a propriedade **SplitOn** é adicionada automaticamente ao seu gatilho. Caso contrário, adicione esta propriedade dentro da carga de resposta que tem a matriz que pretende desemgregar.

*Exemplo*

Suponha que tenha uma API que dereta esta resposta: 
  
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

A sua aplicação lógica só `Rows`precisa do conteúdo da matriz, para que possa criar um gatilho como este exemplo:

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
> Se usar `SplitOn` o comando, não pode obter as propriedades que estão fora da matriz. Assim, por exemplo, não é `status` possível obter a propriedade na resposta devolvida da API.
> 
> Para evitar uma `Rows` falha se a propriedade não `?` existir, este exemplo utiliza o operador.

A sua definição `@triggerBody().name` de fluxo `name` de trabalho `"customer-name-one"` pode agora `"customer-name-two"` ser usada para obter os valores, que são a partir da primeira e da segunda execução. Assim, as saídas do gatilho parecem-se com estes exemplos:

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

As Aplicações Lógicas Azure fornecem vários tipos de ação - cada um com diferentes inputs que definem o comportamento único de uma ação. As ações têm estes elementos de alto nível, embora alguns sejam opcionais:

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

*Necessário*

| Valor | Tipo | Descrição | 
|-------|------|-------------|
| <*nome de ação*> | Cadeia | O nome da ação | 
| <*tipo de ação*> | Cadeia | O tipo de ação, por exemplo, "Http" ou "ApiConnection"| 
| <*nome de entrada*> | Cadeia | O nome para uma entrada que define o comportamento da ação | 
| <*valor de entrada*> | Vários | O valor de entrada, que pode ser uma cadeia, inteiro, objeto JSON, e assim por diante | 
| <*anterior-gatilho-ou-ação-status*> | Objeto JSON | O nome e o estado resultante para o gatilho ou ação que deve ser executado imediatamente antes que esta ação atual possa ser executada | 
|||| 

*Opcional*

| Valor | Tipo | Descrição | 
|-------|------|-------------|
| <*retry-comportamento*> | Objeto JSON | Personaliza o comportamento de retry para falhas intermitentes, que têm o código de estado de 408, 429 e 5XX, e quaisquer exceções à conectividade. Para mais informações, consulte as políticas de Retry. | 
| <*prazo-config-opções*> | Objeto JSON | Para algumas ações, você pode mudar o comportamento `runtimeConfiguration` da ação no tempo de execução, definindo propriedades. Para mais informações, consulte as definições de [configuração do Tempo de Execução](#runtime-config-options). | 
| <*operação-opção*> | Cadeia | Para algumas ações, pode alterar o `operationOptions` comportamento padrão definindo a propriedade. Para mais informações, consulte [opções de operação.](#operation-options) | 
|||| 

## <a name="action-types-list"></a>Lista de tipos de ação

Aqui estão alguns tipos de ação comumente usados: 

* [Tipos de ação incorporados,](#built-in-actions) como estes exemplos e muito mais: 

  * [**HTTP**](#http-action) para chamar pontos finais sobre HTTP ou HTTPS

  * [**Resposta**](#response-action) para responder a pedidos

  * [**Execute o Código JavaScript**](#run-javascript-code) para executar snippets de código JavaScript

  * [**Função**](#function-action) para chamar funções azure

  * Ações de operação de dados como [**Aderir,**](#join-action) [**Compor,**](#compose-action) [**Tabela,**](#table-action) [**Selecionar,**](#select-action)e outras que criam ou transformam dados de várias inputs

  * [**Fluxo de trabalho**](#workflow-action) para chamar outra lógica de fluxo de trabalho de app

* Tipos de ação geridos pela [API,](#managed-api-actions) tais como [**ApiConnection**](#apiconnection-action) e [**ApiConnectionWebHook**](#apiconnectionwebhook-action) que ligam para vários conectores e APIs geridos pela Microsoft, por exemplo, Azure Service Bus, Office 365 Outlook, Power BI, Azure Blob Storage, OneDrive, GitHub, e muito mais

* [Controle tipos](#control-workflow-actions) de ação de fluxo de trabalho como [**Se,**](#if-action) [**Foreach,**](#foreach-action) [**Switch,**](#switch-action) [**Scope,**](#scope-action)e [**Até**](#until-action), que contêm outras ações e ajudam a organizar a execução do fluxo de trabalho

<a name="built-in-actions"></a>

### <a name="built-in-actions"></a>Ações incorporadas

| Tipo de ação | Descrição | 
|-------------|-------------| 
| [**Compor**](#compose-action) | Cria uma única saída a partir de inputs, que podem ter vários tipos. | 
| [**Executar código JavaScript**](#run-javascript-code) | Executar os snippets de código JavaScript que se encaixem em critérios específicos. Para obter requisitos de código e mais informações, consulte [Adicionar e executar códigos com código inline](../logic-apps/logic-apps-add-run-inline-code.md). |
| [**Função**](#function-action) | Chama uma Função Azure. | 
| [**HTTP**](#http-action) | Chama um ponto final http. | 
| [**Junte-se**](#join-action) | Cria uma cadeia de todos os itens de uma matriz e separa esses itens com um caráter delimitador especificado. | 
| [**Parse JSON**](#parse-json-action) | Cria fichas fáceis de utilizar a partir de propriedades em conteúdo JSON. Pode então fazer referência a essas propriedades, incluindo os tokens na sua aplicação lógica. | 
| [**Consulta**](#query-action) | Cria uma matriz a partir de itens em outra matriz com base numa condição ou filtro. | 
| [**Resposta**](#response-action) | Cria uma resposta a uma chamada ou pedido de entrada. | 
| [**Selecione**](#select-action) | Cria uma matriz com objetos JSON transformando itens de outra matriz com base no mapa especificado. | 
| [**Tabela**](#table-action) | Cria uma tabela CSV ou HTML a partir de uma matriz. | 
| [**Terminar**](#terminate-action) | Para um fluxo de trabalho ativa. | 
| [**Wait**](#wait-action) | Pausa o seu fluxo de trabalho por uma duração especificada ou até à data e hora especificadas. | 
| [**Fluxo de trabalho**](#workflow-action) | Nifica um fluxo de trabalho dentro de outro fluxo de trabalho. | 
||| 

<a name="managed-api-actions"></a>

### <a name="managed-api-actions"></a>Ações geridas da API

| Tipo de ação | Descrição | 
|-------------|-------------|  
| [**ApiConnection**](#apiconnection-action) | Chama um ponto final http utilizando um [API gerido pela Microsoft](../connectors/apis-list.md). | 
| [**ApiConnectionWebhook**](#apiconnectionwebhook-action) | Funciona como http Webhook mas usa um [API gerido pela Microsoft](../connectors/apis-list.md). | 
||| 

<a name="control-workflow-actions"></a>

### <a name="control-workflow-actions"></a>Controlar as ações de fluxo de trabalho

Estas ações ajudam-no a controlar a execução do fluxo de trabalho e incluem outras ações. De fora de uma ação de fluxo de trabalho de controlo, pode fazer referência diretamente a ações no interior dessa ação de fluxo de trabalho. Por exemplo, se `Http` tiver uma ação dentro de `@body('Http')` um âmbito, pode fazer referência à expressão de qualquer lugar no fluxo de trabalho. No entanto, as ações que existem dentro de uma ação de fluxo de trabalho de controlo só podem "correr atrás" de outras ações que se encontram na mesma estrutura de fluxo de trabalho de controlo.

| Tipo de ação | Descrição | 
|-------------|-------------| 
| [**ForEach**](#foreach-action) | Execute as mesmas ações em loop para cada item de uma matriz. | 
| [**Se**](#if-action) | Executar ações com base em se a condição especificada é verdadeira ou falsa. | 
| [**Âmbito de aplicação**](#scope-action) | Executar ações com base no estado do grupo a partir de um conjunto de ações. | 
| [**Comutador**](#switch-action) | Executar ações organizadas em casos em que valores de expressões, objetos ou fichas correspondem aos valores especificados por cada caso. | 
| [**Até**](#until-action) | Executar ações em loop até que a condição especificada seja verdadeira. | 
|||  

## <a name="actions---detailed-reference"></a>Ações - Referência detalhada

<a name="apiconnection-action"></a>

### <a name="apiconnection-action"></a>Ação APIConnection

Esta ação envia um pedido http para uma [API gerida pela Microsoft](../connectors/apis-list.md) e requer informações sobre a API e parâmetros mais uma referência a uma ligação válida. 

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

*Necessário*

| Valor | Tipo | Descrição | 
|-------|------|-------------| 
| <*nome de ação*> | Cadeia | O nome da ação fornecida pelo conector | 
| <*nome api*> | Cadeia | O nome da API gerida pela Microsoft que é usada para a ligação | 
| <*método-tipo*> | Cadeia | O método HTTP para chamar a API: "GET", "PUT", "POST", "PATCH" ou "DELETE" | 
| <*api-operação*> | Cadeia | A operação da API para chamar | 
|||| 

*Opcional*

| Valor | Tipo | Descrição | 
|-------|------|-------------| 
| <*outra acção-específico-input-properties*> | Objeto JSON | Quaisquer outras propriedades de entrada que se apliquem a esta ação específica | 
| <*retry-comportamento*> | Objeto JSON | Personaliza o comportamento de retry para falhas intermitentes, que têm o código de estado de 408, 429 e 5XX, e quaisquer exceções à conectividade. Para mais informações, consulte [as políticas de Retry](../logic-apps/logic-apps-exception-handling.md#retry-policies). | 
| <*parâmetros de consulta*> | Objeto JSON | Quaisquer parâmetros de consulta para incluir com a chamada da API. <p>Por exemplo, `"queries": { "api-version": "2018-01-01" }` o `?api-version=2018-01-01` objeto adiciona à chamada. | 
| <*outras propriedades específicas de ação*> | Objeto JSON | Quaisquer outras propriedades que se apliquem a esta ação específica | 
|||| 

*Exemplo*

Esta definição descreve o Envio de uma ação **de e-mail** para o conector Do Office 365 Outlook, que é um API gerido pela Microsoft: 

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

### <a name="apiconnectionwebhook-action"></a>Ação APIConnectionWebhook

Esta ação envia um pedido de subscrição sobre http para um ponto final usando um [API gerido pela Microsoft,](../connectors/apis-list.md)fornece um URL de *callback* para onde o ponto final pode enviar uma resposta, e aguarda que o ponto final responda. Para mais informações, consulte [as subscrições do Endpoint.](#subscribe-unsubscribe)

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

Alguns valores, como <*tipo de* método `"subscribe"` `"unsubscribe"`>, estão disponíveis tanto para os objetos como para os objetos.

*Necessário*

| Valor | Tipo | Descrição | 
|-------|------|-------------| 
| <*nome de ação*> | Cadeia | O nome da ação fornecida pelo conector | 
| <*método-tipo*> | Cadeia | O método HTTP a utilizar para subscrever ou cancelar a subscrição de um ponto final: "GET", "PUT", "POST", "PATCH" ou "DELETE" | 
| <*api-subscrever-URL*> | Cadeia | O URI a utilizar para subscrever a API | 
|||| 

*Opcional*

| Valor | Tipo | Descrição | 
|-------|------|-------------| 
| <*api-unsubscribe-URL*> | Cadeia | O URI a utilizar para a anulação da Subscrição da API | 
| <*conteúdo de cabeçalho*> | Objeto JSON | Quaisquer cabeçalhos para enviar o pedido <p>Por exemplo, para definir o idioma e escrever num pedido: <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` |
| <*conteúdo corporal*> | Objeto JSON | Qualquer conteúdo de mensagem para enviar no pedido | 
| <*tipo de autenticação*> | Objeto JSON | O modelo de autenticação que o pedido utiliza para autenticar pedidos de saída. Para mais informações, consulte [Adicionar autenticação a chamadas de saída](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound). |
| <*retry-comportamento*> | Objeto JSON | Personaliza o comportamento de retry para falhas intermitentes, que têm o código de estado de 408, 429 e 5XX, e quaisquer exceções à conectividade. Para mais informações, consulte [as políticas de Retry](../logic-apps/logic-apps-exception-handling.md#retry-policies). | 
| <*parâmetros de consulta*> | Objeto JSON | Quaisquer parâmetros de consulta para incluir com a chamada da API <p>Por exemplo, `"queries": { "api-version": "2018-01-01" }` o `?api-version=2018-01-01` objeto adiciona à chamada. | 
| <*outra acção-específico-input-properties*> | Objeto JSON | Quaisquer outras propriedades de entrada que se apliquem a esta ação específica | 
| <*outras propriedades específicas de ação*> | Objeto JSON | Quaisquer outras propriedades que se apliquem a esta ação específica | 
|||| 

Também pode especificar limites numa ação **ApiConnectionWebhook** da mesma forma que os [limites assíncronos HTTP](#asynchronous-limits).

<a name="compose-action"></a>

### <a name="compose-action"></a>Componha a ação

Esta ação cria uma única saída a partir de várias inputs, incluindo expressões. Tanto a saída como as inputs podem ter qualquer tipo que as Aplicações Lógicas Azure suportem de forma nativa, tais como matrizes, objetos JSON, XML e binários. Em seguida, pode utilizar a produção da ação noutras ações. 

```json
"Compose": {
   "type": "Compose",
   "inputs": "<inputs-to-compose>",
   "runAfter": {}
},
```

*Necessário* 

| Valor | Tipo | Descrição | 
|-------|------|-------------| 
| <*inputs-para-compor*> | Qualquer | As inputs para criar uma única saída | 
|||| 

*Exemplo 1*

<!-- markdownlint-disable MD038 -->
Esta definição `abcdefg ` de ação funde-se `1234`com um espaço de trailing e o valor:
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

Esta definição de ação funde uma variável de cadeia que contém `abcdefg` e uma variável inteiro que contém: `1234`

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

Esta ação executa um corte de código JavaScript e devolve os resultados através de um `Result` símbolo que as ações posteriores podem referir.

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

*Necessário*

| Valor | Tipo | Descrição |
|-------|------|-------------|
| <*JavaScript-code-snippet*> | Varia | O código JavaScript que pretende executar. Para obter requisitos de código e mais informações, consulte [Adicionar e executar códigos com código inline](../logic-apps/logic-apps-add-run-inline-code.md). <p>No `code` atributo, o seu código pode usar `workflowContext` o objeto apenas para leitura como entrada. Este objeto tem subpropriedades que dão ao seu código acesso aos resultados do gatilho e ações anteriores no seu fluxo de trabalho. Para obter mais `workflowContext` informações sobre o objeto, consulte o [gatilho de referência e os resultados de ação no seu código](../logic-apps/logic-apps-add-run-inline-code.md#workflowcontext). |
||||

*Obrigatório em alguns casos*

O `explicitDependencies` atributo especifica que pretende incluir explicitamente os resultados do gatilho, ações anteriores ou ambos como dependências para o seu código. Para obter mais informações sobre a adição destas dependências, consulte [Adicionar parâmetros para código inline](../logic-apps/logic-apps-add-run-inline-code.md#add-parameters). 

Para `includeTrigger` o atributo, `true` pode `false` especificar ou valores.

| Valor | Tipo | Descrição |
|-------|------|-------------|
| <*ações anteriores*> | Matriz de cadeias | Uma matriz com os vossos nomes de ação especificados. Utilize os nomes de ação que aparecem na sua definição de fluxo de trabalho onde os nomes de ação usam sublinha (_), não espaços (" "). |
||||

*Exemplo 1*

Esta ação executa código que obtém o nome da \<sua aplicação lógica e devolve o texto "Olá mundo do nome da aplicação lógica>" como resultado. Neste exemplo, o código refere o nome do fluxo `workflowContext.workflow.name` de trabalho acedendo à propriedade através do objeto apenas `workflowContext` para leitura. Para obter mais `workflowContext` informações sobre a utilização do objeto, consulte o [gatilho de referência e a ação resulta no seu código](../logic-apps/logic-apps-add-run-inline-code.md#workflowcontext).

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

Esta ação executa código numa aplicação lógica que dispara quando um novo e-mail chega a uma conta do Office 365 Outlook. A aplicação lógica também usa uma ação de e-mail de aprovação de envio que reencaminha o conteúdo do e-mail recebido juntamente com um pedido de aprovação.

O código extrai os endereços de `Body` e-mail da propriedade `SelectedOption` do gatilho e devolve os endereços juntamente com o valor da propriedade da ação de aprovação. A ação inclui explicitamente a ação de `explicitDependencies`  >  `actions` envio de e-mail como uma dependência do atributo.

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

Esta ação chama uma [função Azure](../azure-functions/functions-create-first-azure-function.md)previamente criada.

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

*Necessário*

| Valor | Tipo | Descrição | 
|-------|------|-------------|  
| <*Azure-function-ID*> | Cadeia | O ID de recurso para a função Azure que pretende ligar. Aqui está o formato para este valor:<p>"/subscrições/<*Azure-subscrição-ID*>/recursosGroups/<Grupo de *recursos Azure-grupo*>/fornecedores/Microsoft.Web/sites/<*Azure-function-app-name*>/functions/<*Azure-function-name*>" | 
| <*método-tipo*> | Cadeia | O método HTTP a utilizar para chamar a função: "GET", "PUT", "POST", "PATCH" ou "DELETE" <p>Se não especificado, o padrão é o método "POST". | 
||||

*Opcional*

| Valor | Tipo | Descrição | 
|-------|------|-------------|  
| <*conteúdo de cabeçalho*> | Objeto JSON | Quaisquer cabeçalhos para enviar com a chamada <p>Por exemplo, para definir o idioma e escrever num pedido: <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` |
| <*conteúdo corporal*> | Objeto JSON | Qualquer conteúdo de mensagem para enviar no pedido | 
| <*parâmetros de consulta*> | Objeto JSON | Quaisquer parâmetros de consulta para incluir com a chamada da API <p>Por exemplo, `"queries": { "api-version": "2018-01-01" }` o `?api-version=2018-01-01` objeto adiciona à chamada. | 
| <*outra acção-específico-input-properties*> | Objeto JSON | Quaisquer outras propriedades de entrada que se apliquem a esta ação específica | 
| <*outras propriedades específicas de ação*> | Objeto JSON | Quaisquer outras propriedades que se apliquem a esta ação específica | 
||||

Ao guardar a sua aplicação lógica, o motor Logic Apps executa estas verificações na função referenciada:

* O seu fluxo de trabalho deve ter acesso à função.

* O seu fluxo de trabalho só pode utilizar um gatilho HTTP padrão ou um gatilho genérico do webhook JSON. 

  O motor Logic Apps obtém e caches o URL do gatilho, que é usado no tempo de execução. No entanto, se qualquer operação invalidar o URL em cache, a ação **função** falha no tempo de funcionamento. Para corrigir este problema, guarde novamente a aplicação lógica para que a aplicação lógica obtenha e cache o URL do gatilho novamente.

* A função não pode ter nenhuma rota definida.

* Apenas são permitidos níveis de autorização "função" e "anónimos". 

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

Esta ação envia um pedido ao ponto final especificado http ou HTTPS e verifica a resposta para determinar se o fluxo de trabalho funciona.

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

*Necessário*

| Propriedade | Valor | Tipo | Descrição |
|----------|-------|------|-------------|
| `method` | <*método-tipo*> | Cadeia | O método a utilizar para o envio do pedido de saída: "GET", "PUT", "POST", "PATCH" ou "DELETE" |
| `uri` | <*HTTP-ou-HTTPS-endpoint-URL*> | Cadeia | O URL de ponto final HTTP ou HTTPS onde pretende enviar o pedido de saída. Tamanho máximo da corda: 2 KB <p>Para um serviço ou recurso Azure, esta sintaxe URI inclui o ID de recurso e o caminho para o recurso a que pretende aceder. |
|||||

*Opcional*

| Propriedade | Valor | Tipo | Descrição |
|----------|-------|------|-------------|
| `headers` | <*conteúdo de cabeçalho*> | Objeto JSON | Quaisquer cabeçalhos que você precisa incluir com o pedido <p>Por exemplo, para definir o idioma e o tipo: <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` |
| `queries` | <*parâmetros de consulta*> | Objeto JSON | Quaisquer parâmetros de consulta que você precisa usar no pedido <p>Por exemplo, `"queries": { "api-version": "2018-01-01" }` o `?api-version=2018-01-01` objeto adiciona à chamada. |
| `body` | <*conteúdo corporal*> | Objeto JSON | O conteúdo da mensagem para enviar como carga útil com o pedido |
| `authentication` | <*autenticação-tipo-e-propriedade-valores*> | Objeto JSON | O modelo de autenticação que o pedido utiliza para autenticar pedidos de saída. Para mais informações, consulte [Adicionar autenticação a chamadas de saída](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound). Além do `authority` Scheduler, a propriedade é suportada. Quando não especificado, o `https://management.azure.com/`valor predefinido é, mas pode utilizar um valor diferente. |
| `retryPolicy` > `type` | <*retry-comportamento*> | Objeto JSON | Personaliza o comportamento de retry para falhas intermitentes, que têm o código de estado de 408, 429 e 5XX, e quaisquer exceções à conectividade. Para mais informações, consulte [as políticas de Retry](../logic-apps/logic-apps-exception-handling.md#retry-policies). |
| <*outra acção-específico-input-properties*> | <*input-propriedade*> | Objeto JSON | Quaisquer outras propriedades de entrada que se apliquem a esta ação específica |
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

Esta ação cria uma cadeia de todos os itens de uma matriz e separa esses itens com o caracteres delimitador especificados. 

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

*Necessário*

| Valor | Tipo | Descrição | 
|-------|------|-------------| 
| <*matriz*> | Matriz | A matriz ou expressão que fornece os itens de origem. Se especificar uma expressão, encerre essa expressão com citações duplas. | 
| <*delimitador*> | Cadeia de caracteres únicos | O personagem que separa cada item na corda | 
|||| 

*Exemplo*

Suponha que tenha uma variável "myIntegerArray" previamente criada que contém esta matriz inteiro: 

`[1,2,3,4]` 

Esta definição de ação obtém `variables()` os valores da variável utilizando a função numa expressão e cria esta cadeia com esses valores, que são separados por uma vírposta:`"1,2,3,4"`

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

### <a name="parse-json-action"></a>Ação Parse JSON

Esta ação cria campos ou *fichas* fáceis de utilizar das propriedades em conteúdo JSON. Em seguida, pode aceder a essas propriedades na sua aplicação lógica utilizando os tokens em vez disso. Por exemplo, quando pretende utilizar a saída da JSON a partir de serviços como o Azure Service Bus e o Azure Cosmos DB, pode incluir esta ação na sua aplicação lógica para que possa fazer referência mais facilmente aos dados nessa saída.

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

*Necessário*

| Valor | Tipo | Descrição | 
|-------|------|-------------| 
| <*JSON-source*> | Objeto JSON | O conteúdo jSON que você quer analisar | 
| <*JSON-schema*> | Objeto JSON | O esquema JSON que descreve o conteúdo jSON subjacente, que a ação utiliza para analisar o conteúdo jSON de origem. <p>**Dica**: Em Aplicações LógicaS Designer, pode fornecer o esquema ou fornecer uma carga útil de amostra para que a ação possa gerar o esquema. | 
|||| 

*Exemplo*

Esta definição de ação cria estes símbolos que pode usar no seu fluxo de trabalho, mas apenas em ações que decorrem após a ação **da Parse JSON:**

`FirstName`, `LastName`e`Email`

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

Neste exemplo, a propriedade "conteúdo" especifica o conteúdo jSON para a ação de análise. Também pode fornecer este conteúdo JSON como a carga útil da amostra para gerar o esquema.

```json
"content": {
   "Member": { 
      "FirstName": "Sophie",
      "LastName": "Owen",
      "Email": "Sophie.Owen@contoso.com"
   }
},
```

A propriedade "schema" especifica o esquema JSON utilizado para descrever o conteúdo json:

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

Esta ação cria uma matriz a partir de itens em outra matriz com base numa condição ou filtro especificado.

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

*Necessário*

| Valor | Tipo | Descrição | 
|-------|------|-------------| 
| <*matriz*> | Matriz | A matriz ou expressão que fornece os itens de origem. Se especificar uma expressão, encerre essa expressão com citações duplas. |
| <*condição-ou-filtro*> | Cadeia | A condição utilizada para filtrar itens na matriz de origem <p>**Nota:** Se nenhum valore satisfaz a condição, então a ação cria uma matriz vazia. |
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

Esta ação cria a carga útil para a resposta a um pedido http. 

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

*Necessário*

| Valor | Tipo | Descrição | 
|-------|------|-------------| 
| <*código de estado de resposta*> | Número inteiro | O código de estado HTTP que é enviado para o pedido de entrada. O código padrão é "200 OK", mas o código pode ser qualquer código de estado válido que comece com 2xx, 4xx ou 5xx, mas não com 3xxx. | 
|||| 

*Opcional*

| Valor | Tipo | Descrição | 
|-------|------|-------------| 
| <*cabeçalhos de resposta*> | Objeto JSON | Um ou mais cabeçalhos para incluir com a resposta | 
| <*corpo de resposta*> | Vários | O corpo de resposta, que pode ser uma corda, objeto JSON, ou mesmo conteúdo binário de uma ação anterior | 
|||| 

*Exemplo*

Esta definição de ação cria uma resposta a um pedido http com o código de estado especificado, corpo de mensagem e cabeçalhos de mensagem:

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

* O seu fluxo de trabalho só pode utilizar a ação **Resposta** quando o fluxo de trabalho começa com um gatilho de pedido HTTP, o que significa que o seu fluxo de trabalho deve ser acionado por um pedido HTTP.

* O seu fluxo de trabalho pode utilizar a ação **Resposta** em qualquer *lugar, exceto* dentro de loops **Foreach,** **até** loops, incluindo laços sequenciais, e ramos paralelos. 

* O pedido original http recebe a resposta do seu fluxo de trabalho apenas quando todas as ações necessárias pela ação **Resposta** estiverem terminadas dentro do prazo de [pedido http](../logic-apps/logic-apps-limits-and-config.md#request-limits).

  No entanto, se o seu fluxo de trabalho chama outra aplicação lógica como um fluxo de trabalho aninhado, o fluxo de trabalho dos pais aguarda até que o fluxo de trabalho aninhado termine, não importa quanto tempo passe antes que o fluxo de trabalho aninhada termine.

* Quando o seu fluxo de trabalho utiliza a ação **Resposta** e um padrão de resposta sincronizado, o fluxo de trabalho também não pode usar o comando **splitOn** na definição do gatilho porque esse comando cria várias execuções. Verifique se este caso é utilizado quando o método PUT for utilizado e, se for verdade, devolva uma resposta de "pedido mau".

  Caso contrário, se o seu fluxo de trabalho utilizar o comando **splitOn** e uma ação **de resposta,** o fluxo de trabalho funciona assíncronamente e devolve imediatamente uma resposta "202 ACCEPTED".

* Quando a execução do seu fluxo de trabalho chega à ação **resposta,** mas o pedido de entrada já recebeu uma resposta, a ação **de Resposta** é marcada como "Falhada" devido ao conflito. E como resultado, a sua aplicação lógica também está marcada com o estado "Falhado".

<a name="select-action"></a>

### <a name="select-action"></a>Selecione ação

Esta ação cria uma matriz com objetos JSON transformando itens de outra matriz com base no mapa especificado. A matriz de saída e a matriz de origem têm sempre o mesmo número de itens. Embora não possa alterar o número de objetos na matriz de saída, pode adicionar ou remover propriedades e seus valores através desses objetos. A `select` propriedade especifica pelo menos um par de valor-chave que define o mapa para transformar itens na matriz de origem. Um par de valor-chave representa uma propriedade e o seu valor em todos os objetos da matriz de saída.

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

*Necessário* 

| Valor | Tipo | Descrição | 
|-------|------|-------------| 
| <*matriz*> | Matriz | A matriz ou expressão que fornece os itens de origem. Certifique-se de que encerra uma expressão com citações duplas. <p>**Nota:** Se a matriz de origem estiver vazia, a ação cria uma matriz vazia. | 
| <*nome-chave*> | Cadeia | O nome da propriedade atribuído ao resultado da *expressão* <> <p>Para adicionar uma nova propriedade em todos os objetos da matriz de saída, forneça um> de <*nome-chave* para essa propriedade e uma *expressão* <> para o valor da propriedade. <p>Para remover uma propriedade de todos os objetos da matriz, omita o <*nome-chave*> para essa propriedade. | 
| <*expressão*> | Cadeia | A expressão que transforma o item na matriz de origem e atribui o resultado a <*nome-chave*> | 
|||| 

A ação **Select** cria uma matriz como saída, pelo que qualquer ação que queira utilizar esta saída deve aceitar uma matriz, ou deve converter a matriz no tipo que a ação do consumidor aceita. Por exemplo, para converter a matriz de saída numa corda, pode passar essa matriz para a ação **Compor** e, em seguida, referenciar a saída da ação **Compor** nas suas outras ações.

*Exemplo*

Esta definição de ação cria uma matriz de objetoS JSON a partir de uma matriz inteiro. A ação iterates através da matriz de origem, `@item()` obtém cada valor inteiro`number`usando a expressão, e atribui cada valor à propriedade " em cada objeto JSON:

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

Para utilizar esta saída de matriz noutras ações, passe esta saída para uma ação **de composição:**

```json
"Compose": {
   "type": "Compose",
   "inputs": "@body('Select')",
   "runAfter": {
      "Select": [ "Succeeded" ]
   }
},
```

Em seguida, pode utilizar a saída da ação **Compor** nas suas outras ações, por exemplo, o Office 365 Outlook - Envie uma ação por **e-mail:**

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

Esta ação cria uma tabela CSV ou HTML a partir de uma matriz. Para matrizes com objetos JSON, esta ação cria automaticamente os cabeçalhos da coluna a partir dos nomes de propriedade dos objetos. Para matrizes com outros tipos de dados, deve especificar os cabeçalhos e valores da coluna. Por exemplo, esta matriz inclui as propriedades "ID" e "Product_Name" que esta ação pode usar para os nomes do cabeçalho da coluna:

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

*Necessário* 

| Valor | Tipo | Descrição | 
|-------|------|-------------| 
| \<CSV *ou*> HTML| Cadeia | O formato para a tabela que pretende criar | 
| <*matriz*> | Matriz | A matriz ou expressão que fornece os itens de origem para a tabela <p>**Nota:** Se a matriz de origem estiver vazia, a ação cria uma mesa vazia. | 
|||| 

*Opcional*

Para especificar ou personalizar cabeçalhos `columns` e valores da coluna, utilize a matriz. Quando `header-value` os pares têm o mesmo nome cabeçalho, os seus valores aparecem na mesma coluna sob o nome do cabeçalho. Caso contrário, cada cabeçalho único define uma coluna única.

| Valor | Tipo | Descrição | 
|-------|------|-------------| 
| <*nome da coluna*> | Cadeia | O nome do cabeçalho para uma coluna | 
| <*valor coluna*> | Qualquer | O valor naquela coluna | 
|||| 

*Exemplo 1*

Suponha que tenha uma variável "myItemArray" previamente criada que contém atualmente esta matriz:

`[ {"ID": 0, "Product_Name": "Apples"}, {"ID": 1, "Product_Name": "Oranges"} ]`

Esta definição de ação cria uma tabela CSV da variável "myItemArray". A expressão usada `from` pela propriedade obtém a matriz de `variables()` "myItemArray" utilizando a função:

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

Esta definição de ação cria uma tabela HTML da variável "myItemArray". A expressão usada `from` pela propriedade obtém a matriz de `variables()` "myItemArray" utilizando a função:

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

Esta definição de ação cria uma tabela HTML da variável "myItemArray". No entanto, este exemplo sobrepõe-se aos nomes de cabeçalho da coluna predefinida com "Stock_ID" e "Descrição", e adiciona a palavra "Orgânico" aos valores da coluna "Descrição".

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

### <a name="terminate-action"></a>Cessar a ação

Esta ação interrompe a corrida para uma instância de fluxo de trabalho, cancela quaisquer ações em curso, ignora quaisquer ações restantes e devolve o estado especificado. Por exemplo, pode utilizar a ação **'Terminate'** quando a sua aplicação lógica deve sair completamente de um estado de erro. Esta ação não afeta ações já concluídas e não pode aparecer dentro de **Foreach** e **Até** loops, incluindo loops sequenciais.

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

*Necessário*

| Valor | Tipo | Descrição | 
|-------|------|-------------| 
| <*estado*> | Cadeia | O estatuto de regresso para a corrida: "Falhado", "Cancelado" ou "Bem sucedido" |
|||| 

*Opcional*

As propriedades do objeto "runStatus" aplicam-se apenas quando a propriedade "runStatus" é definida para o estado "Falhado".

| Valor | Tipo | Descrição | 
|-------|------|-------------| 
| <*código de erro ou nome*> | Cadeia | O código ou nome do erro |
| <*mensagem de erro*> | Cadeia | A mensagem ou texto que descreve o erro e quaisquer ações que o utilizador da aplicação possa tomar | 
|||| 

*Exemplo*

Esta definição de ação para uma execução de fluxo de trabalho, define o estado de execução para "Failed", e devolve o estado, um código de erro e uma mensagem de erro:

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

Esta ação interrompe a execução do fluxo de trabalho para o intervalo especificado ou até ao tempo especificado, mas não ambos.

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

*Necessário*

| Valor | Tipo | Descrição | 
|-------|------|-------------| 
| <*número de unidades*> | Número inteiro | Para a ação **Delay,** o número de unidades para esperar | 
| <*intervalo*> | Cadeia | Para a ação **de atraso,** o intervalo para esperar: "Segundo", "Minuto", "Hora", "Dia", "Semana", "Mês" | 
| <*data-hora carimbo*> | Cadeia | Para o **atraso até à** ação, a data e a hora para retomar a execução. Este valor deve utilizar o formato de [data utc](https://en.wikipedia.org/wiki/Coordinated_Universal_Time). | 
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

Esta ação chama outra aplicação lógica previamente criada, o que significa que você pode incluir e reutilizar outros fluxos de trabalho de aplicações lógicas. Também pode utilizar as saídas da criança ou app lógica *aninhada* em ações que seguem a aplicação lógica aninhada, desde que a aplicação lógica infantil retorne uma resposta.

O motor Logic Apps verifica o acesso ao gatilho que pretende ligar, por isso certifique-se de que pode aceder a esse gatilho. Além disso, a aplicação lógica aninhada deve cumprir estes critérios:

* Um gatilho torna a aplicação lógica aninhada chamada, como um [gatilho request](#request-trigger) ou [HTTP](#http-trigger)

* A mesma subscrição Azure que a sua app lógica-mãe

* Para utilizar as saídas da app lógica aninhada na sua aplicação lógica parental, a aplicação lógica aninhada deve ter uma ação [de Resposta](#response-action)

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

*Necessário*

| Valor | Tipo | Descrição | 
|-------|------|-------------| 
| <*aninhado-lógica-app-nome*> | Cadeia | O nome da aplicação lógica que quer ligar | 
| <*nome do gatilho*> | Cadeia | O nome para o gatilho na aplicação lógica aninhada que quer chamar | 
| <*Id de subscrição azure*> | Cadeia | O ID de subscrição azure para a app lógica aninhada |
| <*Grupo de recursos azure*> | Cadeia | O nome do grupo de recursos Azure para a app lógica aninhada |
| <*aninhado-lógica-app-nome*> | Cadeia | O nome da aplicação lógica que quer ligar |
||||

*Opcional*

| Valor | Tipo | Descrição | 
|-------|------|-------------|  
| <*conteúdo de cabeçalho*> | Objeto JSON | Quaisquer cabeçalhos para enviar com a chamada | 
| <*conteúdo corporal*> | Objeto JSON | Qualquer conteúdo de mensagem para enviar com a chamada | 
||||

*Saídas*

As saídas desta ação variam em função da ação resposta da aplicação lógica aninhada. Se a aplicação lógica aninhada não incluir uma ação de Resposta, as saídas estão vazias.

*Exemplo*

Após a ação "Start_search" terminar com sucesso, esta definição de ação de fluxo de trabalho chama outra aplicação lógica chamada "Get_product_information", que passa nas inputs especificadas:

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

## <a name="control-workflow-action-details"></a>Controlar detalhes de ação de fluxo de trabalho

<a name="foreach-action"></a>

### <a name="foreach-action"></a>Foreach action

Esta ação em loop iterates através de uma matriz e executa ações em cada item de matriz. Por predefinição, o laço "para cada" passa em paralelo até um número máximo de voltas. Para este máximo, consulte [Limites e config](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Aprenda [a criar loops "para cada".](../logic-apps/logic-apps-control-flow-loops.md#foreach-loop)

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

*Necessário* 

| Valor | Tipo | Descrição | 
|-------|------|-------------| 
| <*ação-1... n*> | Cadeia | Os nomes das ações que funcionam em cada item de matriz | 
| <*definição de ação-1... n*> | Objeto JSON | As definições das ações que funcionam | 
| <*para cada expressão*> | Cadeia | A expressão que refere cada item na matriz especificada | 
|||| 

*Opcional*

| Valor | Tipo | Descrição | 
|-------|------|-------------| 
| <*contar*> | Número inteiro | Por predefinição, as iterações em loop "para cada" funcionam ao mesmo tempo (simultaneamente ou paralelamente) até ao limite de [predefinição](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Para alterar este limite, estabelecendo um novo <*contagem*> valor, consulte alterar a conmoeda do [loop "para cada".](#change-for-each-concurrency) | 
| <*operação-opção*> | Cadeia | Para executar um laço "para cada um" sequencialmente, em vez de em paralelo, definir `1`<*opção de operação*> `Sequential` ou <*contagem*> para, mas não para ambos. Para mais informações, consulte [correr "para cada" loops sequencialmente](#sequential-for-each). | 
|||| 

*Exemplo*

Este loop "para cada" envia um e-mail para cada item da matriz, que contém anexos a partir de um e-mail de entrada. O loop envia um e-mail, incluindo o anexo, a uma pessoa que revê o anexo.

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

Para especificar apenas uma matriz que é passada como saída do gatilho, esta expressão obtém o <*matriz de>* do corpo do gatilho. Para evitar uma falha se a matriz não `?` existir, a expressão utiliza o operador:

`@triggerBody()?['<array-name>']` 

<a name="if-action"></a>

### <a name="if-action"></a>Se a ação

Esta ação, que é uma *declaração condicional,* avalia uma expressão que representa uma condição e gere um ramo diferente com base no facto de a condição ser verdadeira ou falsa. Se a condição for verdadeira, a condição é marcada com o estatuto de "Sucesso". Aprenda [a criar declarações condicionais.](../logic-apps/logic-apps-control-flow-conditional-statement.md)

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

| Valor | Tipo | Descrição | 
|-------|------|-------------| 
| <*condição*> | Objeto JSON | A condição, que pode ser uma expressão, para avaliar | 
| <*ação-1*> | Objeto JSON | A ação a decorrer quando <*condição*> avalia a verdade | 
| <*definição de ação*> | Objeto JSON | A definição para a ação | 
| <*ação-2*> | Objeto JSON | A ação a decorrer quando <*condição*> avalia a falsa | 
|||| 

As ações `actions` nos `else` ou objetos obtêm estes estatutos:

* "Bem sucedido" quando correm e têm sucesso
* "Falhado" quando correm e falham
* "Saltou" quando o respetivo ramo não funciona

*Exemplo*

Esta condição especifica que quando a variável inteiro tem um valor superior a zero, o fluxo de trabalho verifica um website. Se a variável for zero ou menos, o fluxo de trabalho verifica um site diferente.

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
| "expressão":@parameters" (<*tem SpecialAction*>')" | Apenas para expressões booleanas, a condição passa por qualquer valor que avalie a verdade. <p>Para converter outros tipos em Boolean, `empty()` `equals()`utilize estas funções: ou . | 
| "expressão":@greater" (ações (<*ação*>')output.value, parâmetros ('<*limiar*>')". | Para as funções de comparação, a ação só funciona quando a saída de <*ação*> for superior ao *limiar* <> valor. | 
| "expressão":@or" (ações <*ação*>').output.value, parâmetros *(">* limiar <"),), menos ("ações("<> de ação").output.value, 100)". *same-action* | Para as funções lógicas e a criação de expressões booleanas aninhadas, a ação funciona quando a saída de <*ação*> é mais do que o *limiar* <> valor ou menos de 100. | 
| "expressão":@equals" (ação <ação *action*>').outputs.errors), 0)" | Pode utilizar funções de matriz para verificar se a matriz tem algum itens. A ação funciona `errors` quando a matriz está vazia. | 
||| 

<a name="scope-action"></a>

### <a name="scope-action"></a>Ação de âmbito

Esta ação agrupa logicamente as ações em *âmbitos*, que obtêm o seu próprio estatuto após as ações nesse âmbito terminarem em execução. Em seguida, pode utilizar o estado do âmbito para determinar se outras ações são executadas. Aprenda [a criar âmbitos.](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)

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

*Necessário*

| Valor | Tipo | Descrição | 
|-------|------|-------------|  
| <*interior-ação-1... n*> | Objeto JSON | Uma ou mais ações que correm dentro do âmbito |
| <*inputs de ação*> | Objeto JSON | Os inputs para cada ação |
|||| 

<a name="switch-action"></a>

### <a name="switch-action"></a>Ação Switch

Esta ação, também conhecida como declaração de *mudança,* organiza outras ações em *casos,* e atribui um valor a cada caso, exceto para o caso predefinido se existir. Quando o seu fluxo de trabalho funciona, a ação **Switch** compara o valor de uma expressão, objeto ou símbolo com os valores especificados para cada caso. Se **a** ação switch encontrar um caso correspondente, o seu fluxo de trabalho executa apenas as ações para esse caso. Cada vez que a ação da **Switch** corre, existe apenas um caso de correspondência ou não existem fósforos. Se não existirem correspondências, a ação **switch** executa as ações predefinidas. Aprenda [a criar declarações de switch](../logic-apps/logic-apps-control-flow-switch-statement.md).

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

*Necessário*

| Valor | Tipo | Descrição | 
|-------|------|-------------| 
| <*expressão-objeto-ou-token*> | Varia | A expressão, objeto JSON, ou símbolo para avaliar | 
| <*nome de ação*> | Cadeia | O nome da ação para concorrer para o caso correspondente | 
| <*definição de ação*> | Objeto JSON | A definição para a ação a correr para o caso correspondente | 
| <*valor correspondente*> | Varia | O valor a comparar com o resultado avaliado | 
|||| 

*Opcional*

| Valor | Tipo | Descrição | 
|-------|------|-------------| 
| <*padrão-nome de ação*> | Cadeia | O nome da ação padrão a executar quando não existe nenhum caso correspondente | 
| <*definição de ação por defeito*> | Objeto JSON | A definição para a ação a decorrer quando não existe nenhum caso correspondente | 
|||| 

*Exemplo*

Esta definição de ação avalia se a pessoa que responde ao e-mail do pedido de aprovação selecionou a opção "Aprovar" ou a opção "Rejeitar". Com base nesta escolha, a ação da **Switch** executa as ações para o caso correspondente, que é enviar outro e-mail para o responder, mas com formulação diferente em cada caso. 

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

Esta ação em loop contém ações que funcionam até que a condição especificada seja verdadeira. O loop verifica a condição como o último passo depois de todas as outras ações terem corrido. Pode incluir mais do que `"actions"` uma ação no objeto, e a ação deve definir pelo menos um limite. Aprenda [a criar loops "até".](../logic-apps/logic-apps-control-flow-loops.md#until-loop) 

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

| Valor | Tipo | Descrição | 
|-------|------|-------------| 
| <*nome de ação*> | Cadeia | O nome para a ação que quer correr dentro do loop | 
| <*tipo de ação*> | Cadeia | O tipo de ação que quer executar | 
| <*inputs de ação*> | Vários | Os inputs para a ação para correr | 
| <*condição*> | Cadeia | A condição ou expressão para avaliar depois de todas as ações no loop terminar em execução | 
| <*contagem de loop*> | Número inteiro | O limite para o maior número de loops que a ação pode executar. Para obter mais informações sobre o limite padrão e limite máximo, consulte [Limites e configuração para Aplicações Lógicas Azure](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). | 
| <*loop-timeout*> | Cadeia | O limite no tempo mais longo que o loop pode correr. O `timeout` valor `PT1H`predefinido é , que é o [formato ISO 8601](https://en.wikipedia.org/wiki/ISO_8601)necessário . |
|||| 

*Exemplo*

Esta definição de ação em loop envia um pedido http para o URL especificado até que uma destas condições seja satisfeita:

* O pedido obtém uma resposta com o código de estado "200 OK".
* O loop já correu 60 vezes.
* O loop já dura há uma hora.

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

Os gatilhos e ações baseados no Webhook não verificam regularmente pontos finais, mas aguardam por eventos ou dados específicos nesses pontos finais. Estes gatilhos e ações *subscrevem* os pontos finais fornecendo um URL de *callback* onde o ponto final pode enviar respostas.

A `subscribe` chamada acontece quando o fluxo de trabalho muda de alguma forma, por exemplo, quando as credenciais são renovadas, ou quando os parâmetros de entrada mudam para um gatilho ou ação. Esta chamada utiliza os mesmos parâmetros que as ações padrão http. 

A `unsubscribe` chamada ocorre automaticamente quando uma operação torna o gatilho ou a ação inválidos, por exemplo:

* Apagar ou desativar o gatilho. 
* Apagar ou desativar o fluxo de trabalho. 
* Apagar ou desativar a subscrição. 

Para suportar estas `@listCallbackUrl()` chamadas, a expressão devolve um "URL de callback" único para o gatilho ou ação. Este URL representa um identificador único para os pontos finais que utilizam a API REST do serviço. Os parâmetros para esta função são os mesmos que o gatilho ou a ação do webhook.

<a name="asynchronous-limits"></a>

## <a name="change-asynchronous-duration"></a>Alterar a duração assíncrona

Para ambos os gatilhos e ações, você pode limitar a duração do padrão `limit.timeout` assíncrono a um intervalo de tempo específico, adicionando a propriedade. Assim, se a ação não tiver terminado quando o intervalo caduca, o estado da ação é marcado como `Cancelled` com o `ActionTimedOut` código. A `timeout` propriedade utiliza o [formato ISO 8601.](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations)

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

## <a name="runtime-configuration-settings"></a>Definições de configuração do tempo de execução

Pode alterar o comportamento padrão de tempo de `runtimeConfiguration` execução para gatilhos e ações adicionando estas propriedades ao gatilho ou definição de ação.

| Propriedade | Tipo | Descrição | Gatilho ou ação | 
|----------|------|-------------|-------------------| 
| `runtimeConfiguration.concurrency.runs` | Número inteiro | Alterar o [*limite de predefinição*](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits) do número de casos de fluxo de trabalho que podem ser executados ao mesmo tempo (simultaneamente ou em paralelo). Ajustar este valor pode ajudar a limitar o número de pedidos que os sistemas de backend recebem. <p>Definir `runs` a `1` propriedade para funcionar da `operationOptions` mesma `SingleInstance`forma que definir a propriedade para . Você pode definir qualquer uma das propriedades, mas não as duas. <p>Para alterar o limite de predefinição, consulte alterar a [conmoeda](#change-trigger-concurrency) do gatilho ou [as instâncias do Gatilho sequencialmente](#sequential-trigger). | Todos os gatilhos | 
| `runtimeConfiguration.concurrency.maximumWaitingRuns` | Número inteiro | Altere o [*limite padrão*](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits) no número de casos de fluxo de trabalho que devem esperar para ser executados quando a sua aplicação lógica já estiver a executar as instâncias simultâneas máximas. <p>Para alterar o limite de predefinição, consulte alterar o limite de [corridas](#change-waiting-runs)de espera . | Todos os gatilhos | 
| `runtimeConfiguration.concurrency.repetitions` | Número inteiro | Alterar o [*limite de predefinição*](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits) do número de iterações de loop "para cada" que podem ser executados ao mesmo tempo (simultaneamente ou em paralelo). <p>Definir `repetitions` a `1` propriedade para funcionar da `operationOptions` mesma `SingleInstance`forma que definir a propriedade para . Você pode definir qualquer uma das propriedades, mas não as duas. <p>Para alterar o limite de predefinição, consulte [alterar "para cada" conmoeda](#change-for-each-concurrency) ou [executar ciclos "para cada" circuitos sequencialmente](#sequential-for-each). | Ação: <p>[Foreach](#foreach-action) | 
| `runtimeConfiguration.paginationPolicy.minimumItemCount` | Número inteiro | Para ações específicas que suportem e têm paginação ligada, este valor especifica o número *mínimo* de resultados a recuperar. <p>Para ligar a paginação, consulte [Obter dados em massa, itens ou resultados usando paginação](../logic-apps/logic-apps-exceed-default-page-size-with-pagination.md) | Ação: Variada |
| `runtimeConfiguration.secureData.properties` | Matriz | Em muitos gatilhos e ações, estas configurações escondem inputs, saídas ou ambos do histórico de execução da aplicação lógica. <p>Para saber mais sobre a salvaguarda destes dados, consulte [as inputs e saídas do Ocultar a partir da história da execução](../logic-apps/logic-apps-securing-a-logic-app.md#secure-data-code-view). | A maioria dos gatilhos e ações |
| `runtimeConfiguration.staticResult` | Objeto JSON | Para ações que suportam e têm a `staticResult` definição de resultado [estático](../logic-apps/test-logic-apps-mock-data-static-results.md) ligada, o objeto tem estes atributos: <p>- `name`, que refere o nome de definição de resultado `staticResults` estático da ação atual, `definition` que aparece dentro do atributo no atributo do fluxo de trabalho da aplicação lógica. Para mais informações, consulte [Resultados Estáticos - Referência de Schema para Linguagem](../logic-apps/logic-apps-workflow-definition-language.md#static-results)de Definição de Fluxo de Trabalho . <p> - `staticResultOptions`, que especifica se os `Enabled` resultados estáticos são ou não para a ação atual. <p>Para ativar resultados estáticos, consulte [aplicações lógicas de teste com dados falsos, configurando resultados estáticos](../logic-apps/test-logic-apps-mock-data-static-results.md) | Ação: Variada |
||||| 

<a name="operation-options"></a>

## <a name="operation-options"></a>Opções de operação

Pode alterar o comportamento padrão para gatilhos e ações com a `operationOptions` propriedade na definição de gatilho ou ação.

| Opção de operação | Tipo | Descrição | Gatilho ou ação | 
|------------------|------|-------------|-------------------| 
| `DisableAsyncPattern` | Cadeia | Executar ações baseadas em HTTP sincronizadamente, em vez de assincronicamente. <p><p>Para definir esta opção, consulte [as ações de Corrida sincronizadamente](#asynchronous-patterns). | Ações: <p>[ApiConnection,](#apiconnection-action) <br>[HTTP,](#http-action) <br>[Resposta](#response-action) | 
| `OptimizedForHighThroughput` | Cadeia | Alterar o [limite de predefinição](../logic-apps/logic-apps-limits-and-config.md#throughput-limits) do número de execuções de ação por 5 minutos até ao [limite máximo](../logic-apps/logic-apps-limits-and-config.md#throughput-limits). <p><p>Para definir esta opção, consulte [Correr no modo de alta potência](#run-high-throughput-mode). | Todas as ações | 
| `Sequential` | Cadeia | Executar iterações em loop "para cada" um de cada vez, em vez de todos ao mesmo tempo em paralelo. <p>Esta opção funciona da `runtimeConfiguration.concurrency.repetitions` mesma `1`forma que definir a propriedade para . Você pode definir qualquer uma das propriedades, mas não as duas. <p><p>Para definir esta opção, consulte [correr "para cada" loops sequencialmente](#sequential-for-each).| Ação: <p>[Foreach](#foreach-action) | 
| `SingleInstance` | Cadeia | Executar o gatilho para cada instância lógica de aplicação sequencialmente e esperar que a execução anteriormente ativa termine antes de desencadear a próxima instância de aplicação lógica. <p><p>Esta opção funciona da `runtimeConfiguration.concurrency.runs` mesma `1`forma que definir a propriedade para . Você pode definir qualquer uma das propriedades, mas não as duas. <p>Para definir esta opção, consulte [as instâncias do Gatilho sequencialmente](#sequential-trigger). | Todos os gatilhos | 
||||

<a name="change-trigger-concurrency"></a>

### <a name="change-trigger-concurrency"></a>Alterar simultaneidade dos acionadores

Por padrão, as instâncias de fluxo de aplicações lógicas são executadas ao mesmo tempo (simultaneamente ou em paralelo). Este comportamento significa que cada instância de gatilho dispara antes que a instância de fluxo de trabalho anteriormente ativa termine de funcionar. No entanto, o número de casos de funcionamento simultâneo tem um [limite de predefinição](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Quando o número de casos de fluxo de trabalho em funcionamento simultâneo atinge este limite, quaisquer outras novas instâncias devem esperar para ser executadas. Este limite ajuda a controlar o número de pedidos que os sistemas de backend recebem.

Para alterar o limite padrão, pode utilizar o editor de visão de código ou o Logic `runtimeConfiguration.concurrency.runs` Apps Designer porque alterar a definição de condivisa através do designer adiciona ou atualiza a propriedade na definição de gatilho subjacente e vice-versa. Esta propriedade controla o número máximo de casos de fluxo de trabalho que podem ser executados em paralelo. Aqui ficam algumas considerações para quando pretende ativar o controlo da moeda:

* Quando a moeda está ativada, o [limite SplitOn](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits) é significativamente reduzido para conjuntos de [debatching](#split-on-debatch). Se o número de itens exceder este limite, a capacidade SplitOn é desativada.

* Enquanto a moeda está ativada, uma instância de aplicações lógicas de longa duração pode fazer com que novas instâncias de aplicações lógicas entrem num estado de espera. Este estado impede que as Aplicações Lógicas Azure criem novos casos e acontecem mesmo quando o número de execuções simultâneas é inferior ao número máximo especificado de execuções simultâneas.

  * Para interromper este estado, cancele as primeiras instâncias que ainda estão *em execução.*

    1. No menu da sua aplicação lógica, selecione **Visão Geral**.

    1. Na secção **de história runs,** selecione a primeira instância que ainda está em execução, por exemplo:

       ![Selecione a primeira instância de execução](./media/logic-apps-workflow-actions-triggers/waiting-runs.png)

       > [!TIP]
       > Para ver apenas as instâncias que ainda estão em execução, abra a lista **All** e selecione **Running**.

    1. Sob **a execução da aplicação Logic,** selecione **Cancelar executar**.

       ![Encontre a primeira instância de execução](./media/logic-apps-workflow-actions-triggers/cancel-run.png)

  * Para contornar esta possibilidade, adicione um tempo de descanso a qualquer ação que possa manter estas corridas. Se estiver a trabalhar no editor de códigos, consulte [alterar a duração assíncrona.](#asynchronous-limits) Caso contrário, se estiver a usar o designer, siga estes passos:

    1. Na sua aplicação lógica, na ação em que pretende adicionar um tempo limite, no canto superior direito, selecione o botão elipses **(...**) e, em seguida, selecione **Definições**.

       ![Definições de ação aberta](./media/logic-apps-workflow-actions-triggers/action-settings.png)

    1. No tempo **de tempo,** especifique a duração do tempo no [formato ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations).

       ![Especificar a duração do tempo fora](./media/logic-apps-workflow-actions-triggers/timeout.png)

* Para executar a sua aplicação lógica sequencialmente, detete tea concurrency do gatilho, `1` quer utilizando o editor de visualização de códigos ou o designer. Certifique-se de que também não define `operationOptions` a `SingleInstance` propriedade do gatilho no editor de visualização de código. Caso contrário, obtém-se um erro de validação. Para mais informações, consulte [os casos de Trigger sequencialmente](#sequential-trigger).

#### <a name="edit-in-code-view"></a>Editar na vista de código 

Na definição subjacente ao `runtimeConfiguration.concurrency.runs` gatilho, adicione a propriedade, `1` que `50`pode ter um valor que vai de .

Aqui está um exemplo que limita a simultâneo 10 instâncias:

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

Para mais informações, consulte as definições de [configuração do Tempo de Execução](#runtime-config-options).

#### <a name="edit-in-logic-apps-designer"></a>Editar em Logic Apps Designer

1. No canto superior direito do gatilho, selecione o botão elipses (**...**) e, em seguida, selecione **Definições**.

1. Sob **controlo de moeda,** dectolimite para **on**. **Limit** 

1. Arraste o **grau de parallelismo** para o valor que desejar. Para executar a sua aplicação lógica sequencialmente, arraste o valor do slider para **1**.

<a name="change-for-each-concurrency"></a>

### <a name="change-for-each-concurrency"></a>Alterar a conmoeda "para cada"

Por predefinição, as iterações em loop "para cada" funcionam ao mesmo tempo (simultaneamente ou em paralelo). Este comportamento significa que cada iteração começa a funcionar antes que a iteração anterior termine de funcionar. No entanto, o número de iterações em execução em simultâneo tem um [limite de padrão](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Quando o número de iterações em execução simultaneamente atinge este limite, quaisquer outras iterações devem esperar para correr.

Para alterar o limite padrão, pode utilizar o editor de visão de código ou o Logic `runtimeConfiguration.concurrency.repetitions` Apps Designer porque alterar a definição de condivisa através do designer adiciona ou atualiza a propriedade na definição de ação subjacente "para cada" e vice-versa. Esta propriedade controla o número máximo de iterações que podem ser executadas em paralelo.

> [!NOTE] 
> Se definir a ação "para cada" para executar sequencialmente, quer usando o designer ou o editor `operationOptions` de `Sequential` visualização de código, não detete a propriedade da ação no editor de visualização de código. Caso contrário, obtém-se um erro de validação. Para mais informações, consulte [correr "para cada" loops sequencialmente](#sequential-for-each).

#### <a name="edit-in-code-view"></a>Editar na vista de código 

Na definição subjacente "para cada", `runtimeConfiguration.concurrency.repetitions` adicione ou atualize o imóvel, que pode ter um valor que varia de `1` e `50`.

Aqui está um exemplo que limita a simultâneo corre para 10 iterações:

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

Para mais informações, consulte as definições de [configuração do Tempo de Execução](#runtime-config-options).

#### <a name="edit-in-logic-apps-designer"></a>Editar em Logic Apps Designer

1. No **Para cada** ação, a partir do canto superior direito, selecione o botão elipses (**...**) e, em seguida, selecione **Definições**.

1. Sob **controlo de moeda,** coloque **o Controlo de Moeda** sintetmente **ligado**.

1. Arraste o **grau de parallelismo** para o valor que desejar. Para executar a sua aplicação lógica sequencialmente, arraste o valor do slider para **1**.

<a name="change-waiting-runs"></a>

### <a name="change-waiting-runs-limit"></a>Alterar limite de corridas de espera

Por padrão, as instâncias de fluxo de aplicações lógicas são executadas ao mesmo tempo (simultaneamente ou em paralelo). Este comportamento significa que cada instância de gatilho dispara antes que a instância de fluxo de trabalho anteriormente ativa termine de funcionar. No entanto, o número de casos de funcionamento simultâneo tem um [limite de predefinição](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Quando o número de casos de fluxo de trabalho em funcionamento simultâneo atinge este limite, quaisquer outras novas instâncias devem esperar para ser executadas.

O número de corridas de espera também tem um [limite de predefinição](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Quando o número de corridas de espera atinge este limite, o motor Logic Apps já não aceita novas corridas. Os gatilhos de pedido e webhook devolvem 429 erros, e os gatilhos recorrentes começam a ignorar as tentativas de sondagens.

Não só pode [alterar o limite de predefinição da moeda](#change-trigger-concurrency)do gatilho , como também pode alterar o limite de predefinição das corridas de espera. Na definição subjacente ao `runtimeConfiguration.concurrency.maximumWaitingRuns` gatilho, adicione a propriedade, `1` que `100`pode ter um valor que vai de .

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

Para mais informações, consulte as definições de [configuração do Tempo de Execução](#runtime-config-options).

<a name="sequential-trigger"></a>

### <a name="trigger-instances-sequentially"></a>Casos de gatilho sequencialmente

Para executar cada instância de fluxo de aplicações lógica apenas após a instância anterior terminar de funcionar, detetete o gatilho para correr sequencialmente. Você pode usar o editor de visão de código ou Logic Apps Designer `runtimeConfiguration.concurrency.runs` porque mudar a configuração da concurrency através do designer também adiciona ou atualiza a propriedade na definição de gatilho subjacente e vice-versa.

> [!NOTE] 
> Quando definir um gatilho para executar sequencialmente, quer utilizando o designer ou o editor de `operationOptions` visualização de código, não detete a propriedade do gatilho `Sequential` no editor de visualização de códigos. Caso contrário, obtém-se um erro de validação. 

#### <a name="edit-in-code-view"></a>Editar na vista de código

Na definição do gatilho, coloque qualquer uma destas propriedades, mas não ambas. 

Desloque a `runtimeConfiguration.concurrency.runs` propriedade para: `1`

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

Desloque a `operationOptions` propriedade para: `SingleInstance`

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

Para mais informações, consulte as definições de [configuração do Tempo de Execução](#runtime-config-options) e [as opções de Funcionamento](#operation-options).

#### <a name="edit-in-logic-apps-designer"></a>Editar em Logic Apps Designer

1. No canto superior direito do gatilho, selecione o botão elipses (**...**) e, em seguida, selecione **Definições**.

1. Sob **controlo de moeda,** dectolimite para **on**. **Limit** 

1. Arraste o **grau de deslizador de paralelismo** para o número `1`. 

<a name="sequential-for-each"></a>

### <a name="run-for-each-loops-sequentially"></a>Executar loops "para cada" loops sequencialmente

Para executar uma iteração em loop "para cada" apenas após a iteração anterior terminar a execução, detete toda a ação "para cada" para correr sequencialmente. Você pode usar o editor de visão de código ou Logic Apps Designer porque `runtimeConfiguration.concurrency.repetitions` mudar a conmoeda da ação através do designer também adiciona ou atualiza a propriedade na definição de ação subjacente e vice-versa.

> [!NOTE] 
> Quando definir uma ação "para cada" para executar sequencialmente, quer usando o designer ou editor `operationOptions` de `Sequential` visualização de código, não detete a propriedade da ação no editor de visualização de código. Caso contrário, obtém-se um erro de validação. 

#### <a name="edit-in-code-view"></a>Editar na vista de código

Na definição de ação, coloque qualquer uma destas propriedades, mas não ambas. 

Desloque a `runtimeConfiguration.concurrency.repetitions` propriedade para: `1`

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

Desloque a `operationOptions` propriedade para: `Sequential`

```json
"For_each" {
   "type": "Foreach",
   "actions": { "<actions-to-run>" },
   "foreach": "<for-each-expression>",
   "runAfter": {},
   "operationOptions": "Sequential"
}
```

Para mais informações, consulte as definições de [configuração do Tempo de Execução](#runtime-config-options) e [as opções de Funcionamento](#operation-options).

#### <a name="edit-in-logic-apps-designer"></a>Editar em Logic Apps Designer

1. No canto superior direito de **cada** ação, selecione o botão elipses (**...**) e, em seguida, selecione **Definições**.

1. Sob **controlo de moeda,** coloque **o Controlo de Moeda** sintetmente **ligado**.

1. Arraste o **grau de deslizador de paralelismo** para o número `1`.

<a name="asynchronous-patterns"></a>

### <a name="run-actions-synchronously"></a>Executar ações sincronizadamente

Por padrão, todas as ações baseadas em HTTP seguem o padrão padrão de funcionamento assíncrono. Este padrão especifica que quando uma ação baseada em HTTP envia um pedido para o ponto final especificado, o servidor remoto envia de volta uma resposta "202 ACCEPTED". Esta resposta significa que o servidor aceitou o pedido de processamento. O motor Logic Apps continua a verificar o URL especificado pelo cabeçalho de localização da resposta até que o processamento pare, o que é qualquer resposta não-202.

No entanto, os pedidos têm um limite de tempo, por isso, para ações de longa `operationOptions` duração, pode desativar o comportamento assíncrono adicionando e definindo a propriedade sob `DisableAsyncPattern` as inputs da ação.

```json
"<some-long-running-action>": {
   "type": "Http",
   "inputs": { "<action-inputs>" },
   "operationOptions": "DisableAsyncPattern",
   "runAfter": {}
}
```

Para mais informações, consulte [opções de operação.](#operation-options)

<a name="run-high-throughput-mode"></a>

### <a name="run-in-high-throughput-mode"></a>Executar em modo de alta potência

Para uma definição de aplicação lógica única, o número de ações que executam a cada 5 minutos tem um [limite padrão](../logic-apps/logic-apps-limits-and-config.md#throughput-limits). Para elevar este [maximum](../logic-apps/logic-apps-limits-and-config.md#throughput-limits) limite ao máximo `operationOptions` possível, coloque o imóvel para `OptimizedForHighThroughput`. Esta definição coloca a sua aplicação lógica no modo "alta potência".

> [!NOTE]
> O modo de alta potência está na pré-visualização. Também pode distribuir uma carga de trabalho por mais do que uma aplicação lógica, se necessário.

```json
"<action-name>": {
   "type": "<action-type>",
   "inputs": { "<action-inputs>" },
   "operationOptions": "OptimizedForHighThroughput",
   "runAfter": {}
}
```

<a name="authenticate-triggers-actions"></a>

## <a name="authenticate-triggers-and-actions"></a>Autenticar gatilhos e ações

OS pontos finais HTTP e HTTPS suportam diferentes tipos de autenticação. Com base no gatilho ou ação que utiliza para efetuar chamadas ou pedidos de acesso a estes pontos finais, pode selecionar entre diferentes gamas de tipos de autenticação. Para mais informações, consulte [Adicionar autenticação a chamadas de saída](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound).

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre a linguagem de [definição](../logic-apps/logic-apps-workflow-definition-language.md) de fluxo de trabalho
