---
title: Referência de esquema para tipos de ação e gatilho
description: Guia de referência de esquema para tipos de ação e gatilho de linguagem de definição de fluxo de trabalho em aplicativos lógicos
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 01/19/2020
ms.openlocfilehash: 18e9c9d330ffb8cc4e284fc649cff0840ec2c82c
ms.sourcegitcommit: f97f086936f2c53f439e12ccace066fca53e8dc3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/15/2020
ms.locfileid: "77366226"
---
# <a name="schema-reference-guide-for-trigger-and-action-types-in-azure-logic-apps"></a>Guia de referência de esquema para tipos de ação e gatilho em aplicativos lógicos do Azure

Esta referência descreve os tipos gerais utilizados para identificar gatilhos e ações na definição subjacente de fluxo de trabalho da sua aplicação lógica, que é descrita e validada pela [Linguagem definição](../logic-apps/logic-apps-workflow-definition-language.md)de fluxo de trabalho . Para encontrar gatilhos e ações específicas de conector que possa utilizar nas suas aplicações lógicas, consulte a lista sob a visão geral dos [Conectores](https://docs.microsoft.com/connectors/).

<a name="triggers-overview"></a>

## <a name="triggers-overview"></a>Visão geral de gatilhos

Cada fluxo de trabalho inclui um gatilho, que define as chamadas que instanciam e iniciam o fluxo de trabalho. Aqui estão as categorias gerais de gatilho:

* Um gatilho *de sondagens,* que verifica o ponto final de um serviço em intervalos regulares

* Um gatilho de *pressão,* que cria uma subscrição de um ponto final e fornece um *URL de callback* para que o ponto final possa notificar o gatilho quando o evento especificado acontecer ou os dados estiverem disponíveis. Em seguida, o gatilho aguarda a resposta do ponto de extremidade antes de disparar.

Os gatilhos têm esses elementos de nível superior, embora alguns sejam opcionais:  
  
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
| <> *de nome de gatilho* | Cadeia | O nome do gatilho | 
| <> *tipo gatilho* | Cadeia | O tipo de gatilho, como "http" ou "ApiConnection" | 
| <de *entradas de gatilho*> | Objeto JSON | As entradas que definem o comportamento do gatilho | 
| < *> da unidade de tempo* | Cadeia | A unidade de tempo que descreve a frequência com que o gatilho é acionado: "segundo", "minuto", "hora", "dia", "semana", "mês" | 
| <*número de unidades*> | Número inteiro | Um valor que especifica com que frequência o gatilho é acionado com base na frequência, que é o número de unidades de tempo a aguardar até que o gatilho seja acionado novamente <p>Aqui estão os intervalos mínimos e máximos: <p>- Mês: 1-16 meses </br>- Dia: 1-500 dias </br>- Hora: 1-12.000 horas </br>- Minuto: 1-72.000 minutos </br>- Segundo: 1-9.999,999 segundos<p>Por exemplo, se o intervalo for 6 e a frequência for "month", a recorrência será a cada seis meses. | 
|||| 

*Opcional*

| Valor | Tipo | Descrição | 
|-------|------|-------------| 
| < *> de matriz com condições* | Matriz | Uma matriz que contém uma ou mais [condições](#trigger-conditions) que determinam se devem ou não executar o fluxo de trabalho. Disponível somente para gatilhos. | 
| <*opções de config-config* de tempo de execução> | Objeto JSON | Pode alterar o comportamento do tempo de execução do gatilho, definindo propriedades `runtimeConfiguration`. Para mais informações, consulte as definições de [configuração do Tempo de Execução](#runtime-config-options). | 
| <*dividido> de expressão* | Cadeia | Para os gatilhos que devolvem uma matriz, pode especificar uma expressão que [divide ou *debatcha* ](#split-on-debatch) itens de matriz em várias instâncias de fluxo de trabalho para processamento. | 
| < *> de opção de operação* | Cadeia | Pode alterar o comportamento predefinido definindo a propriedade `operationOptions`. Para mais informações, consulte [opções de operação.](#operation-options) | 
|||| 

## <a name="trigger-types-list"></a>Lista de tipos de gatilho

Cada tipo de gatilho tem uma interface e entradas diferentes que definem o comportamento do gatilho. 

### <a name="built-in-triggers"></a>Gatilhos internos

| Tipo de gatilho | Descrição | 
|--------------|-------------| 
| [**HTTP**](#http-trigger) | Cheques ou *sondagens* qualquer ponto final. Esse ponto de extremidade deve estar em conformidade com um contrato de gatilho específico usando um padrão assíncrono "202" ou retornando uma matriz. | 
| [**HTTPWebhook**](#http-webhook-trigger) | Cria um ponto de extremidade chamável para seu aplicativo lógico, mas chama a URL especificada para registrar ou cancelar o registro. |
| [**Recorrência**](#recurrence-trigger) | Dispara com base em um agendamento definido. Você pode definir uma data e hora futuras para disparar esse gatilho. Com base na frequência, você também pode especificar horas e dias para executar o fluxo de trabalho. | 
| [**Pedido**](#request-trigger)  | Cria um ponto de extremidade que possa ser chamado para seu aplicativo lógico e também é conhecido como gatilho "manual". Por exemplo, consulte [Call, trigger ou nest workflows com pontos finais HTTP](../logic-apps/logic-apps-http-endpoint.md). | 
||| 

### <a name="managed-api-triggers"></a>Gatilhos de API gerenciada

| Tipo de gatilho | Descrição | 
|--------------|-------------| 
| [**ApiConnection**](#apiconnection-trigger) | Verifica ou *sondagens* um ponto final utilizando [APIs geridos pela Microsoft](../connectors/apis-list.md). | 
| [**ApiConnectionWebhook**](#apiconnectionwebhook-trigger) | Cria um ponto final calivel para a sua aplicação lógica, ligando para [apis geridos pela Microsoft](../connectors/apis-list.md) para subscrever e cancelar a subscrição. | 
||| 

## <a name="triggers---detailed-reference"></a>Gatilhos-referência detalhada

<a name="apiconnection-trigger"></a>

### <a name="apiconnection-trigger"></a>Gatilho APIConnection  

Este gatilho verifica ou *dispara* um ponto final utilizando [APIs geridos pela Microsoft](../connectors/apis-list.md) para que os parâmetros para este gatilho possam diferir com base no ponto final. Muitas seções nessa definição de gatilho são opcionais. O comportamento do gatilho depende se as seções estão incluídas ou não.

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
| < *> de nome de ligação* | Cadeia | O nome da conexão com a API gerenciada que o fluxo de trabalho usa |
| <> *tipo método* | Cadeia | O método HTTP para se comunicar com a API gerenciada: "GET", "PUT", "POST", "PATCH", "DELETE" |
| <> *de operação api* | Cadeia | A operação de API para chamar |
| < *> da unidade de tempo* | Cadeia | A unidade de tempo que descreve a frequência com que o gatilho é acionado: "segundo", "minuto", "hora", "dia", "semana", "mês" |
| <*número de unidades*> | Número inteiro | Um valor que especifica com que frequência o gatilho é acionado com base na frequência, que é o número de unidades de tempo a aguardar até que o gatilho seja acionado novamente <p>Aqui estão os intervalos mínimos e máximos: <p>- Mês: 1-16 meses </br>- Dia: 1-500 dias </br>- Hora: 1-12.000 horas </br>- Minuto: 1-72.000 minutos </br>- Segundo: 1-9.999,999 segundos<p>Por exemplo, se o intervalo for 6 e a frequência for "month", a recorrência será a cada seis meses. |
||||

*Opcional*

| Valor | Tipo | Descrição | 
|-------|------|-------------| 
| < *> de comportamento de retry* | Objeto JSON | Personaliza o comportamento de repetição para falhas intermitentes, que têm o código de status 408, 429 e 5XX e quaisquer exceções de conectividade. Para mais informações, consulte [as políticas de Retry](../logic-apps/logic-apps-exception-handling.md#retry-policies). | 
| <*parâmetros de consulta*> | Objeto JSON | Qualquer parâmetro de consulta a ser incluído com a chamada à API. Por exemplo, o `"queries": { "api-version": "2018-01-01" }` objeto adiciona `?api-version=2018-01-01` à chamada. | 
| < *> de corridas máximas* | Número inteiro | Por defeito, as instâncias de fluxo de trabalho são executadas ao mesmo tempo (simultaneamente ou paralelamente) até ao [limite de predefinição](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Para alterar este limite estabelecendo um novo valor <*count*> ver [Change trigger concurrency](#change-trigger-concurrency). | 
| <> *de fila máxima* | Número inteiro | Quando o seu fluxo de trabalho já está a executar o número máximo de casos, que pode alterar com base na propriedade `runtimeConfiguration.concurrency.runs`, quaisquer novas correções são colocadas nesta fila até ao [limite de predefinição](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Para alterar o limite de predefinição, consulte alterar o limite de [corridas](#change-waiting-runs)de espera . | 
| <*dividido> de expressão* | Cadeia | Para gatilhos que retornam matrizes, essa expressão faz referência à matriz a ser usada para que você possa criar e executar uma instância de fluxo de trabalho para cada item de matriz, em vez de usar um loop "for each". <p>Por exemplo, esta expressão representa um item na matriz devolvido dentro do conteúdo corporal do gatilho: `@triggerbody()?['value']` |
| < *> de opção de operação* | Cadeia | Pode alterar o comportamento predefinido definindo a propriedade `operationOptions`. Para mais informações, consulte [opções de operação.](#operation-options) |
||||

*Saídas*
 
| Elemento | Tipo | Descrição |
|---------|------|-------------|
| cabeçalhos | Objeto JSON | Os cabeçalhos da resposta |
| corpo | Objeto JSON | O corpo da resposta |
| código de estado | Número inteiro | O código de status da resposta |
|||| 

*Exemplo*

Essa definição de gatilho verifica o email todos os dias dentro da caixa de entrada de uma conta do Outlook do Office 365:

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
| < *> de nome de ligação* | Cadeia | O nome da conexão com a API gerenciada que o fluxo de trabalho usa | 
| <> *de conteúdo corporal* | Objeto JSON | Qualquer conteúdo de mensagem para enviar como carga para a API gerenciada | 
|||| 

*Opcional*

| Valor | Tipo | Descrição | 
|-------|------|-------------| 
| < *> de comportamento de retry* | Objeto JSON | Personaliza o comportamento de repetição para falhas intermitentes, que têm o código de status 408, 429 e 5XX e quaisquer exceções de conectividade. Para mais informações, consulte [as políticas de Retry](../logic-apps/logic-apps-exception-handling.md#retry-policies). | 
| <*parâmetros de consulta*> | Objeto JSON | Qualquer parâmetro de consulta a ser incluído com a chamada à API <p>Por exemplo, o `"queries": { "api-version": "2018-01-01" }` objeto adiciona `?api-version=2018-01-01` à chamada. | 
| < *> de corridas máximas* | Número inteiro | Por defeito, as instâncias de fluxo de trabalho são executadas ao mesmo tempo (simultaneamente ou paralelamente) até ao [limite de predefinição](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Para alterar este limite estabelecendo um novo valor <*count*> ver [Change trigger concurrency](#change-trigger-concurrency). | 
| <> *de fila máxima* | Número inteiro | Quando o seu fluxo de trabalho já está a executar o número máximo de casos, que pode alterar com base na propriedade `runtimeConfiguration.concurrency.runs`, quaisquer novas correções são colocadas nesta fila até ao [limite de predefinição](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Para alterar o limite de predefinição, consulte alterar o limite de [corridas](#change-waiting-runs)de espera . | 
| <*dividido> de expressão* | Cadeia | Para gatilhos que retornam matrizes, essa expressão faz referência à matriz a ser usada para que você possa criar e executar uma instância de fluxo de trabalho para cada item de matriz, em vez de usar um loop "for each". <p>Por exemplo, esta expressão representa um item na matriz devolvido dentro do conteúdo corporal do gatilho: `@triggerbody()?['value']` |
| < *> de opção de operação* | Cadeia | Pode alterar o comportamento predefinido definindo a propriedade `operationOptions`. Para mais informações, consulte [opções de operação.](#operation-options) | 
|||| 

*Exemplo*

Essa definição de gatilho assina a API do Outlook do Office 365, fornece uma URL de retorno de chamada para o ponto de extremidade da API e aguarda o ponto de extremidade responder quando um novo email chegar.

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

Esse gatilho envia uma solicitação para o ponto de extremidade HTTP ou HTTPS especificado com base na agenda de recorrência especificada. Em seguida, o gatilho verifica a resposta para determinar se o fluxo de trabalho é executado.

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
| `method` | <> *tipo método* | Cadeia | O método a ser usado para enviar a solicitação de saída: "GET", "PUT", "POST", "PATCH" ou "DELETE" |
| `uri` | <*http-ou-HTTPS-endpoint-URL*> | Cadeia | A URL de ponto de extremidade HTTP ou HTTPS para a qual você deseja enviar a solicitação de saída. Tamanho máximo da cadeia de caracteres: 2 KB <p>Para um serviço ou recurso do Azure, essa sintaxe de URI inclui a ID de recurso e o caminho para o recurso que você deseja acessar. |
| `frequency` | < *> da unidade de tempo* | Cadeia | A unidade de tempo que descreve a frequência com que o gatilho é acionado: "segundo", "minuto", "hora", "dia", "semana", "mês" |
| `interval` | <*número de unidades*> | Número inteiro | Um valor que especifica com que frequência o gatilho é acionado com base na frequência, que é o número de unidades de tempo a aguardar até que o gatilho seja acionado novamente <p>Aqui estão os intervalos mínimos e máximos: <p>- Mês: 1-16 meses </br>- Dia: 1-500 dias </br>- Hora: 1-12.000 horas </br>- Minuto: 1-72.000 minutos </br>- Segundo: 1-9.999,999 segundos<p>Por exemplo, se o intervalo for 6 e a frequência for "month", a recorrência será a cada seis meses. |
|||||

*Opcional*

| Propriedade | Valor | Tipo | Descrição |
|----------|-------|------|-------------|
| `headers` | <> *de conteúdo de cabeçalho* | Objeto JSON | Todos os cabeçalhos que você precisa incluir com a solicitação <p>Por exemplo, para definir o idioma e o tipo: <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` |
| `queries` | <*parâmetros de consulta*> | Objeto JSON | Todos os parâmetros de consulta que você precisa usar na solicitação <p>Por exemplo, o objeto `"queries": { "api-version": "2018-01-01" }` adiciona `?api-version=2018-01-01` ao pedido. |
| `body` | <> *de conteúdo corporal* | Objeto JSON | O conteúdo da mensagem a ser enviada como carga com a solicitação |
| `authentication` | <*valores de autenticação e propriedade*> | Objeto JSON | O modelo de autenticação que a solicitação usa para autenticar solicitações de saída. Para mais informações, consulte [Adicionar autenticação a chamadas de saída](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound). Além do Scheduler, a propriedade `authority` é suportada. Quando não especificado, o valor predefinido é `https://management.azure.com/`, mas pode utilizar um valor diferente. |
| `retryPolicy` > `type` | < *> de comportamento de retry* | Objeto JSON | Personaliza o comportamento de repetição para falhas intermitentes, que têm o código de status 408, 429 e 5XX e quaisquer exceções de conectividade. Para mais informações, consulte [as políticas de Retry](../logic-apps/logic-apps-exception-handling.md#retry-policies). |
| `runs` | < *> de corridas máximas* | Número inteiro | Por defeito, as instâncias de fluxo de trabalho são executadas ao mesmo tempo (simultaneamente ou paralelamente) até ao [limite de predefinição](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Para alterar este limite estabelecendo um novo valor <*count*> ver [Change trigger concurrency](#change-trigger-concurrency). |
| `maximumWaitingRuns` | <> *de fila máxima* | Número inteiro | Quando o seu fluxo de trabalho já está a executar o número máximo de casos, que pode alterar com base na propriedade `runtimeConfiguration.concurrency.runs`, quaisquer novas correções são colocadas nesta fila até ao [limite de predefinição](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Para alterar o limite de predefinição, consulte alterar o limite de [corridas](#change-waiting-runs)de espera . |
| `operationOptions` | < *> de opção de operação* | Cadeia | Pode alterar o comportamento predefinido definindo a propriedade `operationOptions`. Para mais informações, consulte [opções de operação.](#operation-options) |
|||||

*Saídas*

| Elemento | Tipo | Descrição |
|---------|------|-------------| 
| cabeçalhos | Objeto JSON | Os cabeçalhos da resposta | 
| corpo | Objeto JSON | O corpo da resposta | 
| código de estado | Número inteiro | O código de status da resposta | 
|||| 

*Requisitos para pedidos de entrada*

Para funcionar bem com seu aplicativo lógico, o ponto de extremidade deve estar em conformidade com um padrão ou contrato de gatilho específico e reconhecer essas propriedades:  
  
| Resposta | Necessário | Descrição | 
|----------|----------|-------------| 
| Código de estado | Sim | O código de status "200 OK" inicia uma execução. Qualquer outro código de status não inicia uma execução. | 
| Cabeçalho repetir-após | Não | O número de segundos até o aplicativo lógico sondar o ponto de extremidade novamente | 
| Cabeçalho do local | Não | A URL a ser chamada no próximo intervalo de sondagem. Se não for especificado, a URL original será usada. | 
|||| 

*Comportamentos de exemplo para diferentes pedidos*

| Código de estado | Tentar novamente após | Comportamento | 
|-------------|-------------|----------|
| 200 | {nenhum} | Execute o fluxo de trabalho e verifique novamente se há mais dados após a recorrência definida. | 
| 200 | 10 segundos | Execute o fluxo de trabalho e verifique novamente se há mais dados após 10 segundos. |  
| 202 | 60 segundos | Não dispare o fluxo de trabalho. A próxima tentativa ocorrerá em um minuto, sujeito à recorrência definida. Se a recorrência definida for menor que um minuto, o cabeçalho Retry-After terá precedência. Caso contrário, a recorrência definida será usada. | 
| 400 | {nenhum} | Solicitação inadequada, não execute o fluxo de trabalho. Se não for definida qualquer `retryPolicy`, então a política de incumprimento é utilizada. Depois que o número de repetições for atingido, o gatilho verificará novamente os dados após a recorrência definida. | 
| 500 | {nenhum}| Erro do servidor, não execute o fluxo de trabalho. Se não for definida qualquer `retryPolicy`, então a política de incumprimento é utilizada. Depois que o número de repetições for atingido, o gatilho verificará novamente os dados após a recorrência definida. | 
|||| 

<a name="http-webhook-trigger"></a>

### <a name="httpwebhook-trigger"></a>Gatilho HTTPWebhook  

Esse gatilho torna seu aplicativo lógico chamável criando um ponto de extremidade que pode registrar uma assinatura chamando a URL de ponto de extremidade especificada. Quando você cria esse gatilho em seu fluxo de trabalho, uma solicitação de saída faz a chamada para registrar a assinatura. Dessa forma, o gatilho pode iniciar a escuta de eventos. Quando uma operação torna esse gatilho inválido, uma solicitação de saída faz automaticamente a chamada para cancelar a assinatura. Para mais informações, consulte [as subscrições do Endpoint.](#subscribe-unsubscribe)

Também pode especificar [limites assíncronos](#asynchronous-limits) num gatilho **HTTPWebhook.** O comportamento do gatilho depende das seções que você usa ou omite.

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

Alguns valores, como o tipo de*método*>estão disponíveis tanto para os objetos `"subscribe"` como para `"unsubscribe"`.

*Necessário*

| Valor | Tipo | Descrição | 
|-------|------|-------------| 
| <> *tipo método* | Cadeia | O método HTTP a ser usado para a solicitação de assinatura: "GET", "PUT", "POST", "PATCH" ou "DELETE" | 
| <> de *URL de subscrição de ponto final* | Cadeia | A URL do ponto de extremidade para onde enviar a solicitação de assinatura | 
|||| 

*Opcional*

| Valor | Tipo | Descrição | 
|-------|------|-------------| 
| <> *tipo método* | Cadeia | O método HTTP a ser usado para a solicitação de cancelamento: "GET", "PUT", "POST", "PATCH" ou "DELETE" | 
| <> de *URL sem subscrição* | Cadeia | A URL do ponto de extremidade para onde enviar a solicitação de cancelamento | 
| <> *de conteúdo corporal* | Cadeia | Qualquer conteúdo de mensagem para enviar na solicitação de assinatura ou cancelamento | 
| <> *tipo autenticação* | Objeto JSON | O modelo de autenticação que a solicitação usa para autenticar solicitações de saída. Para mais informações, consulte [Adicionar autenticação a chamadas de saída](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound). |
| < *> de comportamento de retry* | Objeto JSON | Personaliza o comportamento de repetição para falhas intermitentes, que têm o código de status 408, 429 e 5XX e quaisquer exceções de conectividade. Para mais informações, consulte [as políticas de Retry](../logic-apps/logic-apps-exception-handling.md#retry-policies). | 
| < *> de corridas máximas* | Número inteiro | Por defeito, as instâncias de fluxo de trabalho funcionam ao mesmo tempo (simultaneamente ou paralelamente) até ao limite de [predefinição](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Para alterar este limite estabelecendo um novo valor <*count*> ver [Change trigger concurrency](#change-trigger-concurrency). | 
| <> *de fila máxima* | Número inteiro | Quando o seu fluxo de trabalho já está a executar o número máximo de casos, que pode alterar com base na propriedade `runtimeConfiguration.concurrency.runs`, quaisquer novas correções são colocadas nesta fila até ao [limite de predefinição](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Para alterar o limite de predefinição, consulte alterar o limite de [corridas](#change-waiting-runs)de espera . | 
| < *> de opção de operação* | Cadeia | Pode alterar o comportamento predefinido definindo a propriedade `operationOptions`. Para mais informações, consulte [opções de operação.](#operation-options) | 
|||| 

*Saídas* 

| Elemento | Tipo | Descrição |
|---------|------|-------------| 
| cabeçalhos | Objeto JSON | Os cabeçalhos da resposta | 
| corpo | Objeto JSON | O corpo da resposta | 
| código de estado | Número inteiro | O código de status da resposta | 
|||| 

*Exemplo*

Esse gatilho cria uma assinatura para o ponto de extremidade especificado, fornece uma URL de retorno de chamada exclusiva e aguarda artigos de tecnologia publicados recentemente.

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

Esse gatilho é executado com base na agenda de recorrência especificada e fornece uma maneira fácil de criar um fluxo de trabalho em execução regularmente.

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
| < *> da unidade de tempo* | Cadeia | A unidade de tempo que descreve a frequência com que o gatilho é acionado: "segundo", "minuto", "hora", "dia", "semana", "mês" | 
| <*número de unidades*> | Número inteiro | Um valor que especifica com que frequência o gatilho é acionado com base na frequência, que é o número de unidades de tempo a aguardar até que o gatilho seja acionado novamente <p>Aqui estão os intervalos mínimos e máximos: <p>- Mês: 1-16 meses </br>- Dia: 1-500 dias </br>- Hora: 1-12.000 horas </br>- Minuto: 1-72.000 minutos </br>- Segundo: 1-9.999,999 segundos<p>Por exemplo, se o intervalo for 6 e a frequência for "month", a recorrência será a cada seis meses. | 
|||| 

*Opcional*

| Valor | Tipo | Descrição | 
|-------|------|-------------| 
| <hora *de início com formato-YYYY-MM-DDThh:mms*> | Cadeia | A data e a hora de início neste formato: <p>AAAA-MM-DDThh: mm: SS se você especificar um fuso horário <p>-ou- <p>AAAA-MM-DDThh: mm: ssZ se você não especificar um fuso horário <p>Por exemplo, se você quiser 18 de setembro de 2017 às 2:00 PM, especifique "2017-09-18T14:00:00" e especifique um fuso horário como "hora padrão do Pacífico" ou especifique "2017-09-18T14:00:00Z" sem um fuso horário. <p>**Nota:** Esta hora de início tem um máximo de 49 anos no futuro e deve seguir a especificação de [data ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations) no [formato de data utc,](https://en.wikipedia.org/wiki/Coordinated_Universal_Time)mas sem [uma compensação UTC](https://en.wikipedia.org/wiki/UTC_offset). Se você não especificar um fuso horário, deverá adicionar a letra "Z" no final sem espaços. Este "Z" refere-se ao [tempo náutico](https://en.wikipedia.org/wiki/Nautical_time)equivalente . <p>Para horários simples, a hora de início é a primeira ocorrência, enquanto para horários complexos, o gatilho não dispara tão cedo quanto a hora de início. Para mais informações sobre datas e horários de início, consulte [Criar e agendar tarefas regulares.](../connectors/connectors-native-recurrence.md) | 
| <> *do fuso horário* | Cadeia | Aplica-se apenas quando especifica um tempo de início porque este gatilho não aceita [compensação UTC](https://en.wikipedia.org/wiki/UTC_offset). Especifique o fuso horário que você deseja aplicar. | 
| < *> de notas de uma hora ou mais de uma hora* | Matriz inteiro ou inteiro | Se especificar "Day" ou "Week" para `frequency`, pode especificar um ou mais inteiros de 0 a 23, separados por vírgulas, como as horas do dia em que pretende executar o fluxo de trabalho. <p>Por exemplo, se especificar "10", "12" e "14", terá 10:00, 12:00 e 14 horas como marca a hora. | 
| < *> de marcas de um ou mais minutos* | Matriz inteiro ou inteiro | Se especificar "Day" ou "Week" para `frequency`, pode especificar um ou mais inteiros de 0 a 59, separados por vírgulas, como os minutos da hora em que pretende executar o fluxo de trabalho. <p>Por exemplo, pode especificar "30" como a marca de minutos e usando o exemplo anterior para horas do dia, você recebe 10:30 am, 12:30 pm e 14:30. | 
| weekDays | String ou string array | Se especificar "Semana" para `frequency`, pode especificar um ou mais dias, separados por vírgulas, quando quiser executar o fluxo de trabalho: "Segunda-feira", "terça-feira", "quarta-feira", "quinta-feira", "sábado" e "domingo" | 
| < *> de corridas máximas* | Número inteiro | Por defeito, as instâncias de fluxo de trabalho funcionam ao mesmo tempo (simultaneamente ou paralelamente) até ao limite de [predefinição](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Para alterar este limite estabelecendo um novo valor <*count*> ver [Change trigger concurrency](#change-trigger-concurrency). | 
| <> *de fila máxima* | Número inteiro | Quando o seu fluxo de trabalho já está a executar o número máximo de casos, que pode alterar com base na propriedade `runtimeConfiguration.concurrency.runs`, quaisquer novas correções são colocadas nesta fila até ao [limite de predefinição](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Para alterar o limite de predefinição, consulte alterar o limite de [corridas](#change-waiting-runs)de espera . | 
| < *> de opção de operação* | Cadeia | Pode alterar o comportamento predefinido definindo a propriedade `operationOptions`. Para mais informações, consulte [opções de operação.](#operation-options) | 
|||| 

*Exemplo 1*

Este gatilho de recorrência básico é executado diariamente:

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

Você pode especificar uma data e hora de início para acionar o gatilho. Este gatilho de recorrência começa na data especificada e, em seguida, é acionado diariamente:

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

Este gatilho de recorrência começa em 9 de setembro de 2017 às 2:00 e é acionado semanalmente a cada segunda-feira às 10:30 AM, 12:30 PM e 2:30 PM hora oficial do Pacífico:

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

Esse gatilho torna seu aplicativo lógico chamável criando um ponto de extremidade que pode aceitar solicitações de entrada. Para esse gatilho, forneça um esquema JSON que descreve e valida a carga ou as entradas que o gatilho recebe da solicitação de entrada. O esquema também torna as propriedades de gatilho mais fáceis de fazer referência a partir de ações posteriores no fluxo de trabalho.

Para chamar este gatilho, deve utilizar a `listCallbackUrl` API, que está descrita na [API](https://docs.microsoft.com/rest/api/logic/workflows)REST Do Serviço de Fluxo de Trabalho . Para aprender a usar este gatilho como ponto final http, consulte [Call, trigger ou nest workflows com pontos finais HTTP](../logic-apps/logic-apps-http-endpoint.md).

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
| < *> de nome de propriedade* | Cadeia | O nome de uma propriedade no esquema JSON, que descreve a carga | 
| <> *tipo de propriedade* | Cadeia | O tipo da propriedade | 
|||| 

*Opcional*

| Valor | Tipo | Descrição | 
|-------|------|-------------| 
| <> *tipo método* | Cadeia | O método que as solicitações de entrada devem usar para chamar seu aplicativo lógico: "GET", "PUT", "POST", "PATCH", "DELETE" |
| <> *de parâmetro relativo-caminho-para-aceite* | Cadeia | O caminho relativo para o parâmetro que a URL do ponto de extremidade pode aceitar | 
| <*propriedades necessárias*> | Matriz | Uma ou mais propriedades que exigem valores | 
| < *> de corridas máximas* | Número inteiro | Por defeito, as instâncias de fluxo de trabalho funcionam ao mesmo tempo (simultaneamente ou paralelamente) até ao limite de [predefinição](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Para alterar este limite estabelecendo um novo valor <*count*> ver [Change trigger concurrency](#change-trigger-concurrency). | 
| <> *de fila máxima* | Número inteiro | Quando o seu fluxo de trabalho já está a executar o número máximo de casos, que pode alterar com base na propriedade `runtimeConfiguration.concurrency.runs`, quaisquer novas correções são colocadas nesta fila até ao [limite de predefinição](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Para alterar o limite de predefinição, consulte alterar o limite de [corridas](#change-waiting-runs)de espera . | 
| < *> de opção de operação* | Cadeia | Pode alterar o comportamento predefinido definindo a propriedade `operationOptions`. Para mais informações, consulte [opções de operação.](#operation-options) | 
|||| 

*Exemplo*

Esse gatilho especifica que uma solicitação de entrada deve usar o método HTTP POST para chamar o gatilho e inclui um esquema que valida a entrada da solicitação de entrada:

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

## <a name="trigger-conditions"></a>Condições do gatilho

Para qualquer gatilho e somente gatilhos, você pode incluir uma matriz que contém uma ou mais expressões para condições que determinam se o fluxo de trabalho deve ser executado. Para adicionar a propriedade `conditions` a um gatilho no seu fluxo de trabalho, abra a sua aplicação lógica no editor de visualização de código.

Por exemplo, pode especificar que um gatilho dispara apenas quando um site devolve um erro interno do servidor, referindo o código de estado do gatilho na propriedade `conditions`:

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

Por padrão, um gatilho é acionado somente depois de obter uma resposta "200 OK". Quando uma expressão referencia o código de status de um gatilho, o comportamento padrão do gatilho é substituído. Portanto, se você quiser que o gatilho seja acionado para mais de um código de status, como o código de status "200" e "201", você deve incluir essa expressão como sua condição:

`@or(equals(triggers().code, 200),equals(triggers().code, 201))` 

<a name="split-on-debatch"></a>

## <a name="trigger-multiple-runs"></a>Disparar várias execuções

Se o gatilho retornar uma matriz para o aplicativo lógico processar, às vezes um loop "for each" pode demorar muito para processar cada item da matriz. Em vez disso, pode usar a propriedade **SplitOn** no seu gatilho para *desbatchar* a matriz. O desenvio em lote divide os itens de matriz e inicia uma nova instância de fluxo de trabalho que é executada para cada item de matriz. Essa abordagem é útil, por exemplo, quando você deseja sondar um ponto de extremidade que possa retornar vários novos itens entre intervalos de sondagem. Para o número máximo de itens de matriz que **splitOn** pode processar numa única aplicação lógica, consulte [Limites e configuração](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). 

> [!NOTE]
> Não podes usar o **SplitOn** com um padrão de resposta sincronizado. Qualquer fluxo de trabalho que utilize **splitOn** e inclua uma ação de resposta corre assincronicamente e envia imediatamente uma resposta `202 ACCEPTED`.
>
> Quando a moeda do gatilho está ativada, o [limite SplitOn](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits) é significativamente reduzido. Se o número de itens exceder esse limite, o recurso de divisão será desabilitado.
 
Se o ficheiro Swagger do seu gatilho descrever uma carga útil que é uma matriz, a propriedade **SplitOn** é adicionada automaticamente ao seu gatilho. Caso contrário, adicione essa propriedade dentro da carga de resposta que tem a matriz que você deseja deslotear.

*Exemplo*

Suponha que você tenha uma API que retorne esta resposta: 
  
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

A sua aplicação lógica só precisa do conteúdo da matriz em `Rows`, para que possa criar um gatilho como este exemplo:

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
> Se usar o comando `SplitOn`, não pode obter as propriedades que estão fora da matriz. Assim, por exemplo, não é possível obter a propriedade `status` na resposta devolvida da API.
> 
> Para evitar uma falha se a propriedade `Rows` não existir, este exemplo utiliza o operador de `?`.

A sua definição de fluxo de trabalho pode agora usar `@triggerBody().name` para obter os valores `name`, que são `"customer-name-one"` desde a primeira execução e `"customer-name-two"` a partir da segunda corrida. Portanto, as saídas do gatilho são semelhantes a estes exemplos:

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

## <a name="actions-overview"></a>Visão geral das ações

Os aplicativos lógicos do Azure fornecem vários tipos de ação, cada um com entradas diferentes que definem o comportamento exclusivo de uma ação. As ações têm esses elementos de alto nível, embora alguns sejam opcionais:

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
| < *> de nome de ação* | Cadeia | O nome da ação | 
| <> *tipo de ação* | Cadeia | O tipo de ação, por exemplo, "http" ou "ApiConnection"| 
| < *> de nome de entrada* | Cadeia | O nome de uma entrada que define o comportamento da ação | 
| > *de valor de entrada <* | Vários | O valor de entrada, que pode ser uma cadeia de caracteres, um inteiro, um objeto JSON e assim por diante | 
| <> *anterior-gatilho ou de ação* | Objeto JSON | O nome e o status resultante para o gatilho ou ação que deve ser executado imediatamente antes que esta ação atual possa ser executada | 
|||| 

*Opcional*

| Valor | Tipo | Descrição | 
|-------|------|-------------|
| < *> de comportamento de retry* | Objeto JSON | Personaliza o comportamento de repetição para falhas intermitentes, que têm o código de status 408, 429 e 5XX e quaisquer exceções de conectividade. Para obter mais informações, consulte políticas de repetição. | 
| <*opções de config-config* de tempo de execução> | Objeto JSON | Para algumas ações, você pode mudar o comportamento da ação no tempo de execução, definindo propriedades `runtimeConfiguration`. Para mais informações, consulte as definições de [configuração do Tempo de Execução](#runtime-config-options). | 
| < *> de opção de operação* | Cadeia | Para algumas ações, pode alterar o comportamento padrão definindo a propriedade `operationOptions`. Para mais informações, consulte [opções de operação.](#operation-options) | 
|||| 

## <a name="action-types-list"></a>Lista de tipos de ação

Aqui estão alguns tipos de ação usados com frequência: 

* [Tipos de ação incorporados,](#built-in-actions) como estes exemplos e muito mais: 

  * [**HTTP**](#http-action) para chamar pontos finais sobre HTTP ou HTTPS

  * [**Resposta**](#response-action) para responder a pedidos

  * [**Execute o Código JavaScript**](#run-javascript-code) para executar snippets de código JavaScript

  * [**Função**](#function-action) para chamar funções azure

  * Ações de operação de dados como [**Aderir,** ](#join-action) [**Compor,** ](#compose-action) [**Tabela,** ](#table-action) [**Selecionar,** ](#select-action)e outras que criam ou transformam dados de várias inputs

  * [**Fluxo de trabalho**](#workflow-action) para chamar outra lógica de fluxo de trabalho de app

* Tipos de ação geridos pela [API,](#managed-api-actions) tais como [**ApiConnection**](#apiconnection-action) e [**ApiConnectionWebHook**](#apiconnectionwebhook-action) que ligam para vários conectores e APIs geridos pela Microsoft, por exemplo, Azure Service Bus, Office 365 Outlook, Power BI, Azure Blob Storage, OneDrive, GitHub, e muito mais

* [Controle tipos](#control-workflow-actions) de ação de fluxo de trabalho como [**Se,** ](#if-action) [**Foreach,** ](#foreach-action) [**Switch,** ](#switch-action) [**Scope,** ](#scope-action)e [**Até**](#until-action), que contêm outras ações e ajudam a organizar a execução do fluxo de trabalho

<a name="built-in-actions"></a>

### <a name="built-in-actions"></a>Ações incorporadas

| Tipo de ação | Descrição | 
|-------------|-------------| 
| [**Compor**](#compose-action) | Cria uma única saída de entradas, que pode ter vários tipos. | 
| [**Executar código JavaScript**](#run-javascript-code) | Executar trechos de código JavaScript que se enquadram dentro de critérios específicos. Para obter requisitos de código e mais informações, consulte [Adicionar e executar códigos com código inline](../logic-apps/logic-apps-add-run-inline-code.md). |
| [**Função**](#function-action) | Chama uma função do Azure. | 
| [**HTTP**](#http-action) | Chama um ponto de extremidade HTTP. | 
| [**Junte-se**](#join-action) | Cria uma cadeia de caracteres de todos os itens em uma matriz e separa esses itens com um caractere delimitador especificado. | 
| [**Parse JSON**](#parse-json-action) | Cria tokens amigáveis de propriedades no conteúdo JSON. Em seguida, você pode fazer referência a essas propriedades incluindo os tokens em seu aplicativo lógico. | 
| [**Consulta**](#query-action) | Cria uma matriz de itens em outra matriz com base em uma condição ou filtro. | 
| [**Resposta**](#response-action) | Cria uma resposta a uma chamada ou solicitação recebida. | 
| [**Selecione**](#select-action) | Cria uma matriz com objetos JSON transformando itens de outra matriz com base no mapa especificado. | 
| [**Tabela**](#table-action) | Cria uma tabela CSV ou HTML de uma matriz. | 
| [**Terminar**](#terminate-action) | Interrompe um fluxo de trabalho em execução ativamente. | 
| [**Espera, espera.** ](#wait-action) | Pausa o fluxo de trabalho por uma duração especificada ou até a data e hora especificadas. | 
| [**Fluxo de trabalho**](#workflow-action) | Aninha um fluxo de trabalho dentro de outro fluxo de trabalho. | 
||| 

<a name="managed-api-actions"></a>

### <a name="managed-api-actions"></a>Ações de API gerenciadas

| Tipo de ação | Descrição | 
|-------------|-------------|  
| [**ApiConnection**](#apiconnection-action) | Chama um ponto final http utilizando um [API gerido pela Microsoft](../connectors/apis-list.md). | 
| [**ApiConnectionWebhook**](#apiconnectionwebhook-action) | Funciona como http Webhook mas usa um [API gerido pela Microsoft](../connectors/apis-list.md). | 
||| 

<a name="control-workflow-actions"></a>

### <a name="control-workflow-actions"></a>Ações de fluxo de trabalho de controle

Essas ações ajudam a controlar a execução do fluxo de trabalho e a incluir outras ações. De fora de uma ação de fluxo de trabalho de controle, você pode referenciar ações diretamente dentro dessa ação de fluxo de trabalho de controle. Por exemplo, se tiver uma ação `Http` dentro de um âmbito, pode fazer referência à expressão `@body('Http')` de qualquer lugar no fluxo de trabalho. No entanto, as ações existentes dentro de uma ação de fluxo de trabalho de controle só podem ser "executadas após" outras ações que estão na mesma estrutura de fluxo de trabalho de controle.

| Tipo de ação | Descrição | 
|-------------|-------------| 
| [**ForEach**](#foreach-action) | Execute as mesmas ações em um loop para cada item em uma matriz. | 
| [**Se**](#if-action) | Execute ações com base em se a condição especificada é true ou false. | 
| [**Âmbito de aplicação**](#scope-action) | Execute ações com base no status do grupo de um conjunto de ações. | 
| [**Mudar**](#switch-action) | Execute ações organizadas em casos em que os valores de expressões, objetos ou tokens correspondam aos valores especificados por cada caso. | 
| [**Até**](#until-action) | Execute ações em um loop até que a condição especificada seja verdadeira. | 
|||  

## <a name="actions---detailed-reference"></a>Ações-referência detalhada

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
| < *> de nome de ação* | Cadeia | O nome da ação fornecida pelo conector | 
| <*nome de api*> | Cadeia | O nome da API gerenciada pela Microsoft que é usada para a conexão | 
| <> *tipo método* | Cadeia | O método HTTP para chamar a API: "GET", "PUT", "POST", "PATCH" ou "DELETE" | 
| <> *de operação api* | Cadeia | A operação de API para chamar | 
|||| 

*Opcional*

| Valor | Tipo | Descrição | 
|-------|------|-------------| 
| <> de *outras propriedades específicas de ação* | Objeto JSON | Todas as outras propriedades de entrada que se aplicam a essa ação específica | 
| < *> de comportamento de retry* | Objeto JSON | Personaliza o comportamento de repetição para falhas intermitentes, que têm o código de status 408, 429 e 5XX e quaisquer exceções de conectividade. Para mais informações, consulte [as políticas de Retry](../logic-apps/logic-apps-exception-handling.md#retry-policies). | 
| <*parâmetros de consulta*> | Objeto JSON | Qualquer parâmetro de consulta a ser incluído com a chamada à API. <p>Por exemplo, o `"queries": { "api-version": "2018-01-01" }` objeto adiciona `?api-version=2018-01-01` à chamada. | 
| <> de *propriedades específicas de outra ação* | Objeto JSON | Todas as outras propriedades que se aplicam a essa ação específica | 
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

Alguns valores, como o tipo de*método*>estão disponíveis tanto para os objetos `"subscribe"` como para `"unsubscribe"`.

*Necessário*

| Valor | Tipo | Descrição | 
|-------|------|-------------| 
| < *> de nome de ação* | Cadeia | O nome da ação fornecida pelo conector | 
| <> *tipo método* | Cadeia | O método HTTP a ser usado para assinatura ou cancelamento de um ponto de extremidade: "GET", "PUT", "POST", "PATCH" ou "DELETE" | 
| <> de *URL api-subscrição* | Cadeia | O URI a ser usado para assinar a API | 
|||| 

*Opcional*

| Valor | Tipo | Descrição | 
|-------|------|-------------| 
| <> de *URL api-unsubscribe-* | Cadeia | O URI a ser usado para cancelar a da API | 
| <> *de conteúdo de cabeçalho* | Objeto JSON | Todos os cabeçalhos a serem enviados na solicitação <p>Por exemplo, para definir o idioma e o tipo em uma solicitação: <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` |
| <> *de conteúdo corporal* | Objeto JSON | Qualquer conteúdo de mensagem para enviar na solicitação | 
| <> *tipo autenticação* | Objeto JSON | O modelo de autenticação que a solicitação usa para autenticar solicitações de saída. Para mais informações, consulte [Adicionar autenticação a chamadas de saída](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound). |
| < *> de comportamento de retry* | Objeto JSON | Personaliza o comportamento de repetição para falhas intermitentes, que têm o código de status 408, 429 e 5XX e quaisquer exceções de conectividade. Para mais informações, consulte [as políticas de Retry](../logic-apps/logic-apps-exception-handling.md#retry-policies). | 
| <*parâmetros de consulta*> | Objeto JSON | Qualquer parâmetro de consulta a ser incluído com a chamada à API <p>Por exemplo, o `"queries": { "api-version": "2018-01-01" }` objeto adiciona `?api-version=2018-01-01` à chamada. | 
| <> de *outras propriedades específicas de ação* | Objeto JSON | Todas as outras propriedades de entrada que se aplicam a essa ação específica | 
| <> de *propriedades específicas de outra ação* | Objeto JSON | Todas as outras propriedades que se aplicam a essa ação específica | 
|||| 

Também pode especificar limites numa ação **ApiConnectionWebhook** da mesma forma que os [limites assíncronos HTTP](#asynchronous-limits).

<a name="compose-action"></a>

### <a name="compose-action"></a>Componha a ação

Essa ação cria uma única saída de várias entradas, incluindo expressões. Tanto a saída quanto as entradas podem ter qualquer tipo ao qual os aplicativos lógicos do Azure dão suporte nativo, como matrizes, objetos JSON, XML e binário. Em seguida, você pode usar a saída da ação em outras ações. 

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
| <*inputs-para-compor*> | Qualquer | As entradas para criar uma única saída | 
|||| 

*Exemplo 1*

<!-- markdownlint-disable MD038 -->
Esta definição de ação une `abcdefg ` com um espaço de trailing e o valor `1234`:
<!-- markdownlint-enable MD038 -->

```json
"Compose": {
   "type": "Compose",
   "inputs": "abcdefg 1234",
   "runAfter": {}
},
```

Aqui está a saída que essa ação cria:

`abcdefg 1234`

*Exemplo 2*

Esta definição de ação combina uma variável de cadeia que contém `abcdefg` e uma variável inteiro que contém `1234`:

```json
"Compose": {
   "type": "Compose",
   "inputs": "@{variables('myString')}@{variables('myInteger')}",
   "runAfter": {}
},
```

Aqui está a saída que essa ação cria:

`"abcdefg1234"`

<a name="run-javascript-code"></a>

### <a name="execute-javascript-code-action"></a>Executar ação de código JavaScript

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
| <> de corte *de código JavaScript* | Varia | O código JavaScript que você deseja executar. Para obter requisitos de código e mais informações, consulte [Adicionar e executar códigos com código inline](../logic-apps/logic-apps-add-run-inline-code.md). <p>No atributo `code`, o seu código pode utilizar o objeto de `workflowContext` de leitura apenas como entrada. Este objeto tem subpropriedades que dão ao seu código acesso aos resultados do gatilho e ações anteriores no seu fluxo de trabalho. Para obter mais informações sobre o `workflowContext` objeto, consulte o [gatilho de referência e os resultados de ação no seu código](../logic-apps/logic-apps-add-run-inline-code.md#workflowcontext). |
||||

*Obrigatório em alguns casos*

O atributo `explicitDependencies` especifica que pretende incluir explicitamente os resultados do gatilho, ações anteriores ou ambos como dependências para o seu código. Para obter mais informações sobre a adição destas dependências, consulte [Adicionar parâmetros para código inline](../logic-apps/logic-apps-add-run-inline-code.md#add-parameters). 

Para o atributo `includeTrigger`, pode especificar valores `true` ou `false`.

| Valor | Tipo | Descrição |
|-------|------|-------------|
| <> *de ações anteriores* | matriz de cordas | Uma matriz com os nomes de ação especificados. Utilize os nomes de ação que aparecem na sua definição de fluxo de trabalho onde os nomes de ação usam sublinha (_), não espaços (" "). |
||||

*Exemplo 1*

Esta ação executa código que obtém o nome da sua aplicação lógica e devolve o texto "Olá mundo de \<lógica-app-nome>" como resultado. Neste exemplo, o código refere o nome do fluxo de trabalho acedendo à propriedade `workflowContext.workflow.name` através do objeto de `workflowContext` de leitura. Para obter mais informações sobre a utilização do objeto `workflowContext`, consulte o [gatilho de referência e a ação resulta no seu código](../logic-apps/logic-apps-add-run-inline-code.md#workflowcontext).

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

Esta ação executa o código em um aplicativo lógico que dispara quando um novo email chega em uma conta do Outlook do Office 365. O aplicativo lógico também usa uma ação enviar email de aprovação que encaminha o conteúdo do email recebido junto com uma solicitação de aprovação.

O código extrai os endereços de e-mail da propriedade `Body` do gatilho e devolve os endereços juntamente com o valor `SelectedOption` propriedade da ação de aprovação. A ação inclui explicitamente a ação de envio de e-mail como uma dependência no `explicitDependencies` > `actions` atributo.

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
| <> de *identificação de funções azure* | Cadeia | A ID de recurso para a função do Azure que você deseja chamar. Este é o formato para este valor:<p>"/subscrições/<*Azure-subscrição-ID*>/resourceGroups/<*Azure-resource-group*>/providers/Microsoft.Web/sites/<*Azure-function-app-name*>/functions/<*Azure-function-name*>" | 
| <> *tipo método* | Cadeia | O método HTTP a ser usado para chamar a função: "GET", "PUT", "POST", "PATCH" ou "DELETE" <p>Se não for especificado, o padrão será o método "POST". | 
||||

*Opcional*

| Valor | Tipo | Descrição | 
|-------|------|-------------|  
| <> *de conteúdo de cabeçalho* | Objeto JSON | Todos os cabeçalhos a serem enviados com a chamada <p>Por exemplo, para definir o idioma e o tipo em uma solicitação: <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` |
| <> *de conteúdo corporal* | Objeto JSON | Qualquer conteúdo de mensagem para enviar na solicitação | 
| <*parâmetros de consulta*> | Objeto JSON | Qualquer parâmetro de consulta a ser incluído com a chamada à API <p>Por exemplo, o `"queries": { "api-version": "2018-01-01" }` objeto adiciona `?api-version=2018-01-01` à chamada. | 
| <> de *outras propriedades específicas de ação* | Objeto JSON | Todas as outras propriedades de entrada que se aplicam a essa ação específica | 
| <> de *propriedades específicas de outra ação* | Objeto JSON | Todas as outras propriedades que se aplicam a essa ação específica | 
||||

Quando você salva seu aplicativo lógico, o mecanismo de aplicativos lógicos executa essas verificações na função referenciada:

* Seu fluxo de trabalho deve ter acesso à função.

* Seu fluxo de trabalho pode usar apenas um gatilho HTTP padrão ou um gatilho de webhook JSON genérico. 

  O mecanismo de aplicativos lógicos Obtém e armazena em cache a URL do gatilho, que é usada em tempo de execução. No entanto, se qualquer operação invalidar o URL em cache, a ação **função** falha no tempo de funcionamento. Para corrigir esse problema, salve o aplicativo lógico novamente para que o aplicativo lógico obtenha e armazene em cache a URL do gatilho novamente.

* A função não pode ter nenhuma rota definida.

* Somente os níveis de autorização "função" e "anônimo" são permitidos. 

*Exemplo*

Essa definição de ação chama a função "getProductId" criada anteriormente:

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

Essa ação envia uma solicitação para o ponto de extremidade HTTP ou HTTPS especificado e verifica a resposta para determinar se o fluxo de trabalho é executado.

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
| `method` | <> *tipo método* | Cadeia | O método a ser usado para enviar a solicitação de saída: "GET", "PUT", "POST", "PATCH" ou "DELETE" |
| `uri` | <*http-ou-HTTPS-endpoint-URL*> | Cadeia | A URL de ponto de extremidade HTTP ou HTTPS para a qual você deseja enviar a solicitação de saída. Tamanho máximo da cadeia de caracteres: 2 KB <p>Para um serviço ou recurso do Azure, essa sintaxe de URI inclui a ID de recurso e o caminho para o recurso que você deseja acessar. |
|||||

*Opcional*

| Propriedade | Valor | Tipo | Descrição |
|----------|-------|------|-------------|
| `headers` | <> *de conteúdo de cabeçalho* | Objeto JSON | Todos os cabeçalhos que você precisa incluir com a solicitação <p>Por exemplo, para definir o idioma e o tipo: <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` |
| `queries` | <*parâmetros de consulta*> | Objeto JSON | Todos os parâmetros de consulta que você precisa usar na solicitação <p>Por exemplo, o `"queries": { "api-version": "2018-01-01" }` objeto adiciona `?api-version=2018-01-01` à chamada. |
| `body` | <> *de conteúdo corporal* | Objeto JSON | O conteúdo da mensagem a ser enviada como carga com a solicitação |
| `authentication` | <*valores de autenticação e propriedade*> | Objeto JSON | O modelo de autenticação que a solicitação usa para autenticar solicitações de saída. Para mais informações, consulte [Adicionar autenticação a chamadas de saída](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound). Além do Scheduler, a propriedade `authority` é suportada. Quando não especificado, o valor predefinido é `https://management.azure.com/`, mas pode utilizar um valor diferente. |
| `retryPolicy` > `type` | < *> de comportamento de retry* | Objeto JSON | Personaliza o comportamento de repetição para falhas intermitentes, que têm o código de status 408, 429 e 5XX e quaisquer exceções de conectividade. Para mais informações, consulte [as políticas de Retry](../logic-apps/logic-apps-exception-handling.md#retry-policies). |
| <> de *outras propriedades específicas de ação* | <> *de entrada-propriedade* | Objeto JSON | Todas as outras propriedades de entrada que se aplicam a essa ação específica |
| <> de *propriedades específicas de outra ação* | <> *de valor imobiliário* | Objeto JSON | Todas as outras propriedades que se aplicam a essa ação específica |
|||||

*Exemplo*

Essa definição de ação Obtém as notícias mais recentes enviando uma solicitação para o ponto de extremidade especificado:

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

Essa ação cria uma cadeia de caracteres de todos os itens em uma matriz e separa esses itens com o caractere delimitador especificado. 

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
| < *> de matriz* | Matriz | A matriz ou expressão que fornece os itens de origem. Se você especificar uma expressão, coloque essa expressão entre aspas duplas. | 
| <> *delimitador* | Cadeia de caracteres única | O caractere que separa cada item na cadeia de caracteres | 
|||| 

*Exemplo*

Suponha que você tenha uma variável "myIntegerArray" criada anteriormente que contenha essa matriz de inteiros: 

`[1,2,3,4]` 

Esta definição de ação obtém os valores da variável utilizando a função `variables()` numa expressão e cria esta cadeia com esses valores, que são separados por uma vírvia: `"1,2,3,4"`

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

Esta ação cria campos ou *fichas* fáceis de utilizar das propriedades em conteúdo JSON. Em vez disso, você pode acessar essas propriedades em seu aplicativo lógico usando os tokens. Por exemplo, quando você deseja usar a saída JSON de serviços como o barramento de serviço do Azure e Azure Cosmos DB, você pode incluir essa ação em seu aplicativo lógico para que você possa referenciar mais facilmente os dados nessa saída.

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
| <> *fonte JSON* | Objeto JSON | O conteúdo JSON que você deseja analisar | 
| < *> JSON-schema* | Objeto JSON | O esquema JSON que descreve a base do conteúdo JSON, que a ação usa para analisar o conteúdo JSON de origem. <p>**Dica**: Em Aplicações LógicaS Designer, pode fornecer o esquema ou fornecer uma carga útil de amostra para que a ação possa gerar o esquema. | 
|||| 

*Exemplo*

Esta definição de ação cria estes símbolos que pode usar no seu fluxo de trabalho, mas apenas em ações que decorrem após a ação **da Parse JSON:**

`FirstName`, `LastName`e `Email`

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

Neste exemplo, a propriedade "content" especifica o conteúdo JSON para a ação a ser analisada. Você também pode fornecer esse conteúdo JSON como a carga de exemplo para gerar o esquema.

```json
"content": {
   "Member": { 
      "FirstName": "Sophie",
      "LastName": "Owen",
      "Email": "Sophie.Owen@contoso.com"
   }
},
```

A propriedade "Schema" especifica o esquema JSON usado para descrever o conteúdo JSON:

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

Essa ação cria uma matriz de itens em outra matriz com base em uma condição ou filtro especificado.

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
| < *> de matriz* | Matriz | A matriz ou expressão que fornece os itens de origem. Se você especificar uma expressão, coloque essa expressão entre aspas duplas. |
| < *> de condição ou filtro* | Cadeia | A condição usada para filtrar itens na matriz de origem <p>**Nota:** Se nenhum valore satisfaz a condição, então a ação cria uma matriz vazia. |
|||| 

*Exemplo*

Essa definição de ação cria uma matriz com valores maiores que o valor especificado, que é dois:

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

Essa ação cria a carga para a resposta a uma solicitação HTTP. 

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
| <código *de estado de resposta*> | Número inteiro | O código de status HTTP que é enviado para a solicitação de entrada. O código padrão é "200 OK", mas o código pode ser qualquer código de status válido que comece com 2xx, 4xx ou 5xx, mas não com 3xxx. | 
|||| 

*Opcional*

| Valor | Tipo | Descrição | 
|-------|------|-------------| 
| <*cabeçalhos de resposta*> | Objeto JSON | Um ou mais cabeçalhos a serem incluídos com a resposta | 
| < *> corpo de resposta* | Vários | O corpo da resposta, que pode ser uma cadeia de caracteres, um objeto JSON ou até mesmo conteúdo binário de uma ação anterior | 
|||| 

*Exemplo*

Essa definição de ação cria uma resposta a uma solicitação HTTP com o código de status, o corpo da mensagem e os cabeçalhos de mensagem especificados:

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

  No entanto, se seu fluxo de trabalho chama outro aplicativo lógico como um fluxo de trabalho aninhado, o fluxo de trabalho pai aguarda até que o fluxo de trabalho aninhado seja concluído, não importa quanto tempo o fluxo de trabalho aninhado é concluído.

* Quando o seu fluxo de trabalho utiliza a ação **Resposta** e um padrão de resposta sincronizado, o fluxo de trabalho também não pode usar o comando **splitOn** na definição do gatilho porque esse comando cria várias execuções. Verifique esse caso quando o método PUT for usado e, se for true, retornar uma resposta de "solicitação inválida".

  Caso contrário, se o seu fluxo de trabalho utilizar o comando **splitOn** e uma ação **de resposta,** o fluxo de trabalho funciona assíncronamente e devolve imediatamente uma resposta "202 ACCEPTED".

* Quando a execução do seu fluxo de trabalho chega à ação **resposta,** mas o pedido de entrada já recebeu uma resposta, a ação **de Resposta** é marcada como "Falhada" devido ao conflito. E, como resultado, a execução do aplicativo lógico também é marcada com o status "falha".

<a name="select-action"></a>

### <a name="select-action"></a>Selecione ação

Essa ação cria uma matriz com objetos JSON transformando itens de outra matriz com base no mapa especificado. A matriz de saída e a matriz de origem sempre têm o mesmo número de itens. Embora não seja possível alterar o número de objetos na matriz de saída, você pode adicionar ou remover propriedades e seus valores entre esses objetos. A propriedade `select` especifica pelo menos um par de valor-chave que define o mapa para transformar itens na matriz de origem. Um par chave-valor representa uma propriedade e seu valor em todos os objetos na matriz de saída.

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
| < *> de matriz* | Matriz | A matriz ou expressão que fornece os itens de origem. Certifique-se de colocar uma expressão com aspas duplas. <p>**Nota:** Se a matriz de origem estiver vazia, a ação cria uma matriz vazia. | 
| < *> de nome-chave* | Cadeia | O nome da propriedade atribuído ao resultado de <*expressão*> <p>Para adicionar uma nova propriedade em todos os objetos da matriz de saída, forneça um <*nome-chave*> para esse imóvel e uma*expressão*< para o valor da propriedade. <p>Para remover uma propriedade de todos os objetos da matriz, omita o nome*de chaves*> para essa propriedade. | 
| <*expressão*> | Cadeia | A expressão que transforma o item na matriz de origem e atribui o resultado a> | 
|||| 

A ação **Select** cria uma matriz como saída, pelo que qualquer ação que queira utilizar esta saída deve aceitar uma matriz, ou deve converter a matriz no tipo que a ação do consumidor aceita. Por exemplo, para converter a matriz de saída numa corda, pode passar essa matriz para a ação **Compor** e, em seguida, referenciar a saída da ação **Compor** nas suas outras ações.

*Exemplo*

Essa definição de ação cria uma matriz de objeto JSON a partir de uma matriz de inteiros. A ação iterates através da matriz de origem, obtém cada valor inteiro usando a expressão `@item()`, e atribui cada valor à propriedade "`number`" em cada objeto JSON:

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

Aqui está a matriz que essa ação cria:

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

### <a name="table-action"></a>Ação da tabela

Essa ação cria uma tabela CSV ou HTML de uma matriz. Para matrizes com objetos JSON, essa ação cria automaticamente os cabeçalhos de coluna dos nomes de propriedade dos objetos. Para matrizes com outros tipos de dados, você deve especificar os cabeçalhos e valores de coluna. Por exemplo, esta matriz inclui as propriedades "ID" e "Product_Name" que esta ação pode usar para os nomes do cabeçalho da coluna:

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
| \<CSV *ou* HTML>| Cadeia | O formato da tabela que você deseja criar | 
| < *> de matriz* | Matriz | A matriz ou expressão que fornece os itens de origem para a tabela <p>**Nota:** Se a matriz de origem estiver vazia, a ação cria uma mesa vazia. | 
|||| 

*Opcional*

Para especificar ou personalizar cabeçalhos e valores da coluna, utilize a matriz `columns`. Quando os pares `header-value` têm o mesmo nome cabeçalho, os seus valores aparecem na mesma coluna sob o nome do cabeçalho. Caso contrário, cada cabeçalho exclusivo define uma coluna exclusiva.

| Valor | Tipo | Descrição | 
|-------|------|-------------| 
| <*nome da coluna*> | Cadeia | O nome do cabeçalho de uma coluna | 
| <> *de valor de coluna* | Qualquer | O valor nessa coluna | 
|||| 

*Exemplo 1*

Suponha que você tenha uma variável "myItemArray" criada anteriormente que contenha atualmente esta matriz:

`[ {"ID": 0, "Product_Name": "Apples"}, {"ID": 1, "Product_Name": "Oranges"} ]`

Essa definição de ação cria uma tabela CSV a partir da variável "myItemArray". A expressão usada pela propriedade `from` obtém a matriz de "myItemArray" utilizando a função `variables()`:

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

Esta é a tabela CSV que esta ação cria: 

```
ID,Product_Name 
0,Apples 
1,Oranges 
```

*Exemplo 2*

Essa definição de ação cria uma tabela HTML a partir da variável "myItemArray". A expressão usada pela propriedade `from` obtém a matriz de "myItemArray" utilizando a função `variables()`:

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

Esta é a tabela HTML que esta ação cria: 

<table><thead><tr><th>ID</th><th>Product_Name</th></tr></thead><tbody><tr><td>0</td><td>Aplica</td></tr><tr><td>1</td><td>Laranjas</td></tr></tbody></table>

*Exemplo 3*

Essa definição de ação cria uma tabela HTML a partir da variável "myItemArray". No entanto, este exemplo sobrepõe-se aos nomes de cabeçalho da coluna predefinida com "Stock_ID" e "Descrição", e adiciona a palavra "Orgânico" aos valores da coluna "Descrição".

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

Esta é a tabela HTML que esta ação cria: 

<table><thead><tr><th>Stock_ID</th><th>Descrição</th></tr></thead><tbody><tr><td>0</td><td>Maçãs orgânicas</td></tr><tr><td>1</td><td>Laranjas orgânicas</td></tr></tbody></table>

<a name="terminate-action"></a>

### <a name="terminate-action"></a>Ação terminar

Essa ação interrompe a execução de uma instância de fluxo de trabalho, cancela as ações em andamento, ignora as ações restantes e retorna o status especificado. Por exemplo, pode utilizar a ação **'Terminate'** quando a sua aplicação lógica deve sair completamente de um estado de erro. Esta ação não afeta ações já concluídas e não pode aparecer dentro de **Foreach** e **Até** loops, incluindo loops sequenciais.

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
| <*status*> | Cadeia | O status a ser retornado para a execução: "falha", "cancelado" ou "êxito" |
|||| 

*Opcional*

As propriedades do objeto "runStatus" aplicam-se somente quando a propriedade "runStatus" está definida como status "falha".

| Valor | Tipo | Descrição | 
|-------|------|-------------| 
| <*código de erro ou nome*> | Cadeia | O código ou o nome do erro |
| <> *de mensagem de erro* | Cadeia | A mensagem ou o texto que descreve o erro e as ações que o usuário do aplicativo pode executar | 
|||| 

*Exemplo*

Essa definição de ação interrompe uma execução de fluxo de trabalho, define o status de execução como "falha" e retorna o status, um código de erro e uma mensagem de erro:

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

Essa ação pausa a execução do fluxo de trabalho para o intervalo especificado ou até a hora especificada, mas não ambos.

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
| <> *de data-data-data* | Cadeia | Para o **atraso até à** ação, a data e a hora para retomar a execução. Este valor deve utilizar o formato de [data utc](https://en.wikipedia.org/wiki/Coordinated_Universal_Time). | 
|||| 

*Exemplo 1*

Essa definição de ação pausa o fluxo de trabalho por 15 minutos:

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

Essa definição de ação pausa o fluxo de trabalho até o tempo especificado:

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

Essa ação chama outro aplicativo lógico criado anteriormente, o que significa que você pode incluir e reutilizar outros fluxos de trabalho de aplicativo lógico. Também pode utilizar as saídas da criança ou app lógica *aninhada* em ações que seguem a aplicação lógica aninhada, desde que a aplicação lógica infantil retorne uma resposta.

O mecanismo de aplicativos lógicos verifica o acesso ao gatilho que você deseja chamar, portanto, verifique se você pode acessar esse gatilho. Além disso, o aplicativo lógico aninhado deve atender a estes critérios:

* Um gatilho torna a aplicação lógica aninhada chamada, como um [gatilho request](#request-trigger) ou [HTTP](#http-trigger)

* A mesma assinatura do Azure que seu aplicativo lógico pai

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
| <> de nome de *aplicativo aninhado-lógica* | Cadeia | O nome do aplicativo lógico que você deseja chamar | 
| <> *de nome de gatilho* | Cadeia | O nome do gatilho no aplicativo lógico aninhado que você deseja chamar | 
| <*azure-assinatura-ID*> | Cadeia | A ID de assinatura do Azure para o aplicativo lógico aninhado |
| <> *do grupo de recursos Azure* | Cadeia | O nome do grupo de recursos do Azure para o aplicativo lógico aninhado |
| <> de nome de *aplicativo aninhado-lógica* | Cadeia | O nome do aplicativo lógico que você deseja chamar |
||||

*Opcional*

| Valor | Tipo | Descrição | 
|-------|------|-------------|  
| <> *de conteúdo de cabeçalho* | Objeto JSON | Todos os cabeçalhos a serem enviados com a chamada | 
| <> *de conteúdo corporal* | Objeto JSON | Qualquer conteúdo de mensagem para enviar com a chamada | 
||||

*Saídas*

As saídas dessa ação variam de acordo com a ação de resposta do aplicativo lógico aninhado. Se o aplicativo lógico aninhado não incluir uma ação de resposta, as saídas estarão vazias.

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

## <a name="control-workflow-action-details"></a>Detalhes da ação de fluxo de trabalho de controle

<a name="foreach-action"></a>

### <a name="foreach-action"></a>Ação foreach

Essa ação de loop itera por meio de uma matriz e executa ações em cada item da matriz. Por padrão, o loop "for each" é executado em paralelo até um número máximo de loops. Para este máximo, consulte [Limites e config](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Aprenda [a criar loops "para cada".](../logic-apps/logic-apps-control-flow-loops.md#foreach-loop)

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
| <*acção-1... n*> | Cadeia | Os nomes das ações que são executadas em cada item da matriz | 
| <*definição de acção-1... n*> | Objeto JSON | As definições das ações que são executadas | 
| < *> de expressão para cada expressão* | Cadeia | A expressão que faz referência a cada item na matriz especificada | 
|||| 

*Opcional*

| Valor | Tipo | Descrição | 
|-------|------|-------------| 
| <*contagem*> | Número inteiro | Por predefinição, as iterações em loop "para cada" funcionam ao mesmo tempo (simultaneamente ou paralelamente) até ao limite de [predefinição](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Para alterar este limite estabelecendo um novo valor <*count*> consulte [Alterar "para cada" conmoeda](#change-for-each-concurrency)de loop . | 
| < *> de opção de operação* | Cadeia | Para executar um laço "para cada" loop sequencialmente, em vez de em paralelo, definido ou <*operation-option*> para `Sequential` ou <*count*> para `1`, mas não ambos. Para mais informações, consulte [correr "para cada" loops sequencialmente](#sequential-for-each). | 
|||| 

*Exemplo*

Esse loop "for each" envia um email para cada item na matriz, que contém anexos de um email de entrada. O loop envia um email, incluindo o anexo, para uma pessoa que revisa o anexo.

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

Para especificar apenas uma matriz que é passada como saída do gatilho, esta expressão recebe o nome de matriz <*matriz*> matriz do corpo do gatilho. Para evitar uma falha se a matriz não existir, a expressão utiliza o operador `?`:

`@triggerBody()?['<array-name>']` 

<a name="if-action"></a>

### <a name="if-action"></a>Ação If

Esta ação, que é uma *declaração condicional,* avalia uma expressão que representa uma condição e gere um ramo diferente com base no facto de a condição ser verdadeira ou falsa. Se a condição for verdadeira, a condição será marcada com o status "êxito". Aprenda [a criar declarações condicionais.](../logic-apps/logic-apps-control-flow-conditional-statement.md)

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
| *<condição*> | Objeto JSON | A condição, que pode ser uma expressão, para avaliar | 
| <*ação-1*> | Objeto JSON | A ação a decorrer quando <*condição*> avalia a verdade | 
| <> *de definição de ação* | Objeto JSON | A definição da ação | 
| < *> de ação 2* | Objeto JSON | A ação a executar quando <*condição*> avalia a falsa | 
|||| 

As ações nos objetos `actions` ou `else` obtêm estes estatutos:

* "Êxito" quando eles são executados e bem-sucedidos
* "Falha" ao executar e falhar
* "Ignorado" quando a respectiva ramificação não é executada

*Exemplo*

Essa condição especifica que quando a variável de inteiro tem um valor maior que zero, o fluxo de trabalho verifica um site. Se a variável for zero ou menos, o fluxo de trabalho verificará um site diferente.

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

Aqui estão alguns exemplos que mostram como você pode usar expressões em condições:
  
| JSON | Resultado | 
|------|--------| 
| "expressão": "@parameters('<*hasSpecialAction*>')" | Somente para expressões booleanas, a condição passa para qualquer valor que seja avaliado como true. <p>Para converter outros tipos em Boolean, utilize estas funções: `empty()` ou `equals()`. | 
| "expressão": "@greater(ações('<*action*>''output.value, parâmetros ('<*threshold*>')" | Para funções de comparação, a ação só funciona quando a saída de <*action*> é mais do que o valor <*threshold*> | 
| "expressão": "@or(ações maiores('<*action*>'output.value, parâmetros ('<*threshold*>')), menos('<*same-action*&').output.value, 100)" | Para funções lógicas e criação de expressões booleanas aninhadas, a ação corre quando a saída de <*action*> é mais do que o valor <*limiar*> ou inferior a 100. | 
| "expressão": "@equals('<*action*>'outputs.errors), 0)" | Você pode usar funções de matriz para verificar se a matriz tem algum item. A ação funciona quando a `errors` matriz está vazia. | 
||| 

<a name="scope-action"></a>

### <a name="scope-action"></a>Ação de escopo

Esta ação agrupa logicamente as ações em *âmbitos*, que obtêm o seu próprio estatuto após as ações nesse âmbito terminarem em execução. Você pode usar o status do escopo para determinar se outras ações são executadas. Aprenda [a criar âmbitos.](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)

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
| <*ação interior-1... n*> | Objeto JSON | Uma ou mais ações que são executadas dentro do escopo |
| <*inputs de ação*> | Objeto JSON | As entradas para cada ação |
|||| 

<a name="switch-action"></a>

### <a name="switch-action"></a>Ação do comutador

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
| <*expressão-objecto-ou-token*> | Varia | A expressão, o objeto JSON ou o token a ser avaliado | 
| < *> de nome de ação* | Cadeia | O nome da ação a ser executada para o caso de correspondência | 
| <> *de definição de ação* | Objeto JSON | A definição da ação a ser executada para o caso de correspondência | 
| <> *de valor correspondente* | Varia | O valor a ser comparado com o resultado avaliado | 
|||| 

*Opcional*

| Valor | Tipo | Descrição | 
|-------|------|-------------| 
| <> *de nome de ação por defeito* | Cadeia | O nome da ação padrão a ser executada quando não houver nenhum caso correspondente | 
| <> *de definição de ação por predefinição de incumprimento* | Objeto JSON | A definição da ação a ser executada quando não houver nenhum caso correspondente | 
|||| 

*Exemplo*

Esta definição de ação avalia se a pessoa que está respondendo ao email de solicitação de aprovação selecionou a opção "aprovar" ou a opção "rejeitar". Com base nesta escolha, a ação da **Switch** executa as ações para o caso correspondente, que é enviar outro e-mail para o responder, mas com formulação diferente em cada caso. 

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

### <a name="until-action"></a>Ação until

Essa ação de loop contém ações que são executadas até que a condição especificada seja verdadeira. O loop verifica a condição como a última etapa após a execução de todas as outras ações. Pode incluir mais do que uma ação no `"actions"` objeto, e a ação deve definir pelo menos um limite. Aprenda [a criar loops "até".](../logic-apps/logic-apps-control-flow-loops.md#until-loop) 

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
| < *> de nome de ação* | Cadeia | O nome da ação que você deseja executar dentro do loop | 
| <> *tipo de ação* | Cadeia | O tipo de ação que você deseja executar | 
| <*inputs de ação*> | Vários | As entradas para a ação a ser executada | 
| *<condição*> | Cadeia | A condição ou expressão a ser avaliada depois que todas as ações no loop terminarem de ser executadas | 
| <> *de contagem de loop* | Número inteiro | O limite do número máximo de loops que a ação pode executar. Para obter mais informações sobre o limite padrão e limite máximo, consulte [Limites e configuração para Aplicações Lógicas Azure](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). | 
| <> *de tempo de tempo livre* | Cadeia | O limite do tempo mais longo que o loop pode ser executado. O valor `timeout` padrão é `PT1H`, que é o [formato ISO 8601](https://en.wikipedia.org/wiki/ISO_8601)necessário . |
|||| 

*Exemplo*

Essa definição de ação de loop envia uma solicitação HTTP para a URL especificada até que uma dessas condições seja atendida:

* A solicitação Obtém uma resposta com o código de status "200 OK".
* O loop executou 60 vezes.
* O loop foi executado por uma hora.

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

## <a name="webhooks-and-subscriptions"></a>WebHooks e assinaturas

Gatilhos e ações baseados em webhook não verificam regularmente os pontos de extremidade, mas aguardam eventos ou dados específicos nesses pontos de extremidade. Estes gatilhos e ações *subscrevem* os pontos finais fornecendo um URL de *callback* onde o ponto final pode enviar respostas.

A chamada `subscribe` acontece quando o fluxo de trabalho muda de alguma forma, por exemplo, quando as credenciais são renovadas, ou quando os parâmetros de entrada mudam para um gatilho ou ação. Essa chamada usa os mesmos parâmetros que as ações HTTP padrão. 

A chamada `unsubscribe` acontece automaticamente quando uma operação torna o gatilho ou a ação inválidos, por exemplo:

* Excluindo ou desabilitando o gatilho. 
* Excluindo ou desabilitando o fluxo de trabalho. 
* Excluindo ou desabilitando a assinatura. 

Para suportar estas chamadas, a expressão `@listCallbackUrl()` devolve um "URL de callback" único para o gatilho ou ação. Essa URL representa um identificador exclusivo para os pontos de extremidade que usam a API REST do serviço. Os parâmetros para essa função são os mesmos que o gatilho ou ação de webhook.

<a name="asynchronous-limits"></a>

## <a name="change-asynchronous-duration"></a>Alterar duração assíncrona

Tanto para gatilhos como para ações, pode limitar a duração do padrão assíncrono a um intervalo de tempo específico, adicionando a propriedade `limit.timeout`. Assim, se a ação não tiver terminado quando o intervalo caduca, o estado da ação é marcado como `Cancelled` com o código `ActionTimedOut`. A propriedade `timeout` utiliza o [formato ISO 8601.](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations)

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

Pode alterar o comportamento padrão de tempo de execução para gatilhos e ações adicionando estas propriedades `runtimeConfiguration` ao gatilho ou definição de ação.

| Propriedade | Tipo | Descrição | Gatilho ou ação | 
|----------|------|-------------|-------------------| 
| `runtimeConfiguration.concurrency.runs` | Número inteiro | Alterar o [*limite de predefinição*](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits) do número de casos de fluxo de trabalho que podem ser executados ao mesmo tempo (simultaneamente ou em paralelo). O ajuste desse valor pode ajudar a limitar o número de solicitações que os sistemas de back-end recebem. <p>A definição da propriedade `runs` para `1` funciona da mesma forma que a definição da propriedade `operationOptions` para `SingleInstance`. Você pode definir qualquer propriedade, mas não ambos. <p>Para alterar o limite de predefinição, consulte alterar a [conmoeda](#change-trigger-concurrency) do gatilho ou [as instâncias do Gatilho sequencialmente](#sequential-trigger). | Todos os gatilhos | 
| `runtimeConfiguration.concurrency.maximumWaitingRuns` | Número inteiro | Altere o [*limite padrão*](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits) no número de casos de fluxo de trabalho que devem esperar para ser executados quando a sua aplicação lógica já estiver a executar as instâncias simultâneas máximas. <p>Para alterar o limite de predefinição, consulte alterar o limite de [corridas](#change-waiting-runs)de espera . | Todos os gatilhos | 
| `runtimeConfiguration.concurrency.repetitions` | Número inteiro | Alterar o [*limite de predefinição*](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits) do número de iterações de loop "para cada" que podem ser executados ao mesmo tempo (simultaneamente ou em paralelo). <p>A definição da propriedade `repetitions` para `1` funciona da mesma forma que a definição da propriedade `operationOptions` para `SingleInstance`. Você pode definir qualquer propriedade, mas não ambos. <p>Para alterar o limite de predefinição, consulte [alterar "para cada" conmoeda](#change-for-each-concurrency) ou [executar ciclos "para cada" circuitos sequencialmente](#sequential-for-each). | Ação: <p>[Foreach](#foreach-action) | 
| `runtimeConfiguration.paginationPolicy.minimumItemCount` | Número inteiro | Para ações específicas que suportem e têm paginação ligada, este valor especifica o número *mínimo* de resultados a recuperar. <p>Para ligar a paginação, consulte [Obter dados em massa, itens ou resultados usando paginação](../logic-apps/logic-apps-exceed-default-page-size-with-pagination.md) | Ação: variadas |
| `runtimeConfiguration.secureData.properties` | Matriz | Em muitos gatilhos e ações, essas configurações ocultam entradas, saídas ou ambos do histórico de execução do aplicativo lógico. <p>Para saber mais sobre a salvaguarda destes dados, consulte [as inputs e saídas do Ocultar a partir da história da execução](../logic-apps/logic-apps-securing-a-logic-app.md#secure-data-code-view). | Maioria dos gatilhos e ações |
| `runtimeConfiguration.staticResult` | Objeto JSON | Para ações que suportam e têm a definição [de resultado estático](../logic-apps/test-logic-apps-mock-data-static-results.md) ligada, o `staticResult` objeto tem estes atributos: <p>- `name`, que faz referência ao nome de definição de resultado estático da ação atual, que aparece dentro do atributo `staticResults` no atributo de `definition` do fluxo de aplicações lógicas. Para mais informações, consulte [Resultados Estáticos - Referência de Schema para Linguagem](../logic-apps/logic-apps-workflow-definition-language.md#static-results)de Definição de Fluxo de Trabalho . <p> - `staticResultOptions`, que especifica se os resultados estáticos são `Enabled` ou não para a ação em curso. <p>Para ativar resultados estáticos, consulte [aplicações lógicas de teste com dados falsos, configurando resultados estáticos](../logic-apps/test-logic-apps-mock-data-static-results.md) | Ação: variadas |
||||| 

<a name="operation-options"></a>

## <a name="operation-options"></a>Opções de operação

Pode alterar o comportamento padrão para gatilhos e ações com a propriedade `operationOptions` na definição de gatilho ou ação.

| Opção de operação | Tipo | Descrição | Gatilho ou ação | 
|------------------|------|-------------|-------------------| 
| `DisableAsyncPattern` | Cadeia | Execute ações baseadas em HTTP de forma síncrona, em vez de assíncrona. <p><p>Para definir esta opção, consulte [as ações de Corrida sincronizadamente](#asynchronous-patterns). | Ações: <p>[ApiConnection,](#apiconnection-action) <br>[HTTP,](#http-action) <br>[Resposta](#response-action) | 
| `OptimizedForHighThroughput` | Cadeia | Alterar o [limite de predefinição](../logic-apps/logic-apps-limits-and-config.md#throughput-limits) do número de execuções de ação por 5 minutos até ao [limite máximo](../logic-apps/logic-apps-limits-and-config.md#throughput-limits). <p><p>Para definir esta opção, consulte [Correr no modo de alta potência](#run-high-throughput-mode). | Todas as ações | 
| `Sequential` | Cadeia | Execute "para cada" iterações de loop, uma de cada vez, em vez de todas ao mesmo tempo em paralelo. <p>Esta opção funciona da mesma forma que definir a propriedade `runtimeConfiguration.concurrency.repetitions` para `1`. Você pode definir qualquer propriedade, mas não ambos. <p><p>Para definir esta opção, consulte [correr "para cada" loops sequencialmente](#sequential-for-each).| Ação: <p>[Foreach](#foreach-action) | 
| `SingleInstance` | Cadeia | Execute o gatilho para cada instância de aplicativo lógico sequencialmente e aguarde até que a execução ativa anteriormente seja acionada antes de disparar a próxima instância de aplicativo lógico. <p><p>Esta opção funciona da mesma forma que definir a propriedade `runtimeConfiguration.concurrency.runs` para `1`. Você pode definir qualquer propriedade, mas não ambos. <p>Para definir esta opção, consulte [as instâncias do Gatilho sequencialmente](#sequential-trigger). | Todos os gatilhos | 
||||

<a name="change-trigger-concurrency"></a>

### <a name="change-trigger-concurrency"></a>Alterar simultaneidade do gatilho

Por padrão, todas as instâncias de fluxo de trabalho do aplicativo lógico são executadas ao mesmo tempo (simultaneamente ou em paralelo). Esse comportamento significa que cada instância de gatilho é acionada antes da execução da instância de fluxo de trabalho ativa anteriormente. No entanto, o número de casos de funcionamento simultâneo tem um [limite de predefinição](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Quando o número de instâncias de fluxo de trabalho em execução simultânea atinge esse limite, todas as outras novas instâncias devem aguardar para serem executadas. Esse limite ajuda a controlar o número de solicitações que os sistemas de back-end recebem.

Para alterar o limite padrão, pode utilizar o editor de visão de código ou o Logic Apps Designer porque alterar a definição de condivisa através do designer adiciona ou atualiza a propriedade `runtimeConfiguration.concurrency.runs` na definição de gatilho subjacente e vice-versa. Essa propriedade controla o número máximo de instâncias de fluxo de trabalho que podem ser executadas em paralelo. Aqui estão algumas considerações sobre quando você deseja habilitar o controle de simultaneidade:

* Quando a moeda está ativada, o [limite SplitOn](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits) é significativamente reduzido para conjuntos de [debatching](#split-on-debatch). Se o número de itens exceder esse limite, o recurso de divisão será desabilitado.

* Enquanto a simultaneidade está habilitada, uma instância de aplicativo lógico de execução longa pode fazer com que novas instâncias do aplicativo lógico entrem em um estado de espera. Esse estado impede que os aplicativos lógicos do Azure criem novas instâncias e ocorram mesmo quando o número de execuções simultâneas for menor do que o número máximo especificado de execuções simultâneas.

  * Para interromper este estado, cancele as primeiras instâncias que ainda estão *em execução.*

    1. No menu da sua aplicação lógica, selecione **Visão Geral**.

    1. Na secção **de história runs,** selecione a primeira instância que ainda está em execução, por exemplo:

       ![Selecionar instância em execução mais antiga](./media/logic-apps-workflow-actions-triggers/waiting-runs.png)

       > [!TIP]
       > Para ver apenas as instâncias que ainda estão em execução, abra a lista **All** e selecione **Running**.

    1. Sob **a execução da aplicação Logic,** selecione **Cancelar executar**.

       ![Localizar instância em execução mais antiga](./media/logic-apps-workflow-actions-triggers/cancel-run.png)

  * Para contornar essa possibilidade, adicione um tempo limite a qualquer ação que possa manter essas execuções. Se estiver a trabalhar no editor de códigos, consulte [alterar a duração assíncrona.](#asynchronous-limits) Caso contrário, se você estiver usando o designer, siga estas etapas:

    1. Na sua aplicação lógica, na ação em que pretende adicionar um tempo limite, no canto superior direito, selecione o botão elipses **(...** ) e, em seguida, selecione **Definições**.

       ![Definições de ação aberta](./media/logic-apps-workflow-actions-triggers/action-settings.png)

    1. No tempo **de tempo,** especifique a duração do tempo no [formato ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations).

       ![Especificar duração do tempo limite](./media/logic-apps-workflow-actions-triggers/timeout.png)

* Para executar a sua aplicação lógica sequencialmente, detete tea moeda do gatilho para `1` quer usando o editor de visualização de código seleções ou o designer. Certifique-se de que também não define a propriedade `operationOptions` do gatilho para `SingleInstance` no editor de visualização de códigos. Caso contrário, você receberá um erro de validação. Para mais informações, consulte [os casos de Trigger sequencialmente](#sequential-trigger).

#### <a name="edit-in-code-view"></a>Editar na exibição de código 

Na definição subjacente ao gatilho, adicione a propriedade `runtimeConfiguration.concurrency.runs`, que pode ter um valor que varia de `1` a `50`.

Aqui está um exemplo que limita as execuções simultâneas a 10 instâncias:

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

#### <a name="edit-in-logic-apps-designer"></a>Editar no designer de aplicativos lógicos

1. No canto superior direito do gatilho, selecione o botão elipses ( **...** ) e, em seguida, selecione **Definições**.

1. Sob **controlo de moeda,** dectolimite para **on**. 

1. Arraste o **grau de parallelismo** para o valor que desejar. Para executar a sua aplicação lógica sequencialmente, arraste o valor do slider para **1**.

<a name="change-for-each-concurrency"></a>

### <a name="change-for-each-concurrency"></a>Alterar "para cada" simultaneidade

Por padrão, todas as iterações de loop "for each" são executadas ao mesmo tempo (simultaneamente ou em paralelo). Esse comportamento significa que cada iteração começa a ser executada antes que a iteração anterior termine a execução. No entanto, o número de iterações em execução em simultâneo tem um [limite de padrão](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Quando o número de iterações em execução simultânea atinge esse limite, todas as outras iterações devem aguardar para serem executadas.

Para alterar o limite padrão, pode utilizar o editor de visão de código ou o Logic Apps Designer porque alterar a definição de condivisa através do designer adiciona ou atualiza a propriedade `runtimeConfiguration.concurrency.repetitions` na definição de ação subjacente "para cada" e vice-versa. Essa propriedade controla o número máximo de iterações que podem ser executadas em paralelo.

> [!NOTE] 
> Se definir a ação "para cada" para funcionar sequencialmente, quer utilizando o designer ou o editor de visualização de códigos, não detete te `operationOptions` propriedade da ação para `Sequential` no editor de visualização de códigos. Caso contrário, você receberá um erro de validação. Para mais informações, consulte [correr "para cada" loops sequencialmente](#sequential-for-each).

#### <a name="edit-in-code-view"></a>Editar na exibição de código 

Na definição subjacente "para cada um", adicione ou atualize o `runtimeConfiguration.concurrency.repetitions` imóvel, que pode ter um valor que varia de `1` e `50`.

Aqui está um exemplo que limita as execuções simultâneas a 10 iterações:

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

#### <a name="edit-in-logic-apps-designer"></a>Editar no designer de aplicativos lógicos

1. No **Para cada** ação, a partir do canto superior direito, selecione o botão elipses ( **...** ) e, em seguida, selecione **Definições**.

1. Sob **controlo de moeda,** coloque **o Controlo de Moeda** sintetmente **ligado**.

1. Arraste o **grau de parallelismo** para o valor que desejar. Para executar a sua aplicação lógica sequencialmente, arraste o valor do slider para **1**.

<a name="change-waiting-runs"></a>

### <a name="change-waiting-runs-limit"></a>Alterar limite de execuções de espera

Por padrão, todas as instâncias de fluxo de trabalho do aplicativo lógico são executadas ao mesmo tempo (simultaneamente ou em paralelo). Esse comportamento significa que cada instância de gatilho é acionada antes da execução da instância de fluxo de trabalho ativa anteriormente. No entanto, o número de casos de funcionamento simultâneo tem um [limite de predefinição](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Quando o número de instâncias de fluxo de trabalho em execução simultânea atinge esse limite, todas as outras novas instâncias devem aguardar para serem executadas.

O número de corridas de espera também tem um [limite de predefinição](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Quando o número de execuções em espera atinge esse limite, o mecanismo de aplicativos lógicos não aceita mais novas execuções. Os gatilhos de solicitação e webhook retornam erros 429 e os gatilhos recorrentes começam a ignorar as tentativas de sondagem.

Não só pode [alterar o limite de predefinição da moeda](#change-trigger-concurrency)do gatilho , como também pode alterar o limite de predefinição das corridas de espera. Na definição subjacente ao gatilho, adicione a propriedade `runtimeConfiguration.concurrency.maximumWaitingRuns`, que pode ter um valor que varia de `1` a `100`.

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

### <a name="trigger-instances-sequentially"></a>Instâncias de gatilho sequencialmente

Para executar cada instância de fluxo de trabalho do aplicativo lógico somente após a conclusão da execução da instância anterior, defina o gatilho para ser executado em sequência. Você pode usar o editor de visão de código ou Logic Apps Designer porque mudar a configuração da concurrency através do designer também adiciona ou atualiza a propriedade `runtimeConfiguration.concurrency.runs` na definição de gatilho subjacente e vice-versa.

> [!NOTE] 
> Quando definir um gatilho para executar sequencialmente, quer utilizando o designer ou o editor de visualização de código, não detete a propriedade `operationOptions` do gatilho para `Sequential` no editor de visualização de códigos. Caso contrário, você receberá um erro de validação. 

#### <a name="edit-in-code-view"></a>Editar na exibição de código

Na definição do gatilho, defina uma dessas propriedades, mas não ambas. 

Deteto a propriedade `runtimeConfiguration.concurrency.runs` para `1`:

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

Deteto a propriedade `operationOptions` para `SingleInstance`:

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

#### <a name="edit-in-logic-apps-designer"></a>Editar no designer de aplicativos lógicos

1. No canto superior direito do gatilho, selecione o botão elipses ( **...** ) e, em seguida, selecione **Definições**.

1. Sob **controlo de moeda,** dectolimite para **on**. 

1. Arraste o **grau de parallelismo** para o número `1`. 

<a name="sequential-for-each"></a>

### <a name="run-for-each-loops-sequentially"></a>Executar loops "for each" sequencialmente

Para executar uma iteração de loop "for each" somente após a conclusão da execução da iteração anterior, defina a ação "para cada" para ser executada em sequência. Você pode usar o editor de visão de código ou Logic Apps Designer porque mudar a conmoeda da ação através do designer também adiciona ou atualiza a propriedade `runtimeConfiguration.concurrency.repetitions` na definição de ação subjacente e vice-versa.

> [!NOTE] 
> Quando definir uma ação "para cada" para executar sequencialmente, quer usando o designer ou editor de code view, não detete a propriedade `operationOptions` da ação para `Sequential` no editor de visualização de códigos. Caso contrário, você receberá um erro de validação. 

#### <a name="edit-in-code-view"></a>Editar na exibição de código

Na definição de ação, defina qualquer uma dessas propriedades, mas não ambas. 

Deteto a propriedade `runtimeConfiguration.concurrency.repetitions` para `1`:

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

Deteto a propriedade `operationOptions` para `Sequential`:

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

#### <a name="edit-in-logic-apps-designer"></a>Editar no designer de aplicativos lógicos

1. No canto superior direito de **cada** ação, selecione o botão elipses ( **...** ) e, em seguida, selecione **Definições**.

1. Sob **controlo de moeda,** coloque **o Controlo de Moeda** sintetmente **ligado**.

1. Arraste o **grau de parallelismo** para o número `1`.

<a name="asynchronous-patterns"></a>

### <a name="run-actions-synchronously"></a>Executar ações de forma síncrona

Por padrão, todas as ações baseadas em HTTP seguem o padrão de operação assíncrona padrão. Esse padrão especifica que quando uma ação baseada em HTTP envia uma solicitação para o ponto de extremidade especificado, o servidor remoto envia de volta uma resposta "202 aceito". Essa resposta significa que o servidor aceitou a solicitação de processamento. O mecanismo de aplicativos lógicos mantém a verificação da URL especificada pelo cabeçalho de local da resposta até que o processamento seja interrompido, que é qualquer resposta diferente de 202.

No entanto, os pedidos têm um limite de tempo, por isso, para ações de longa duração, pode desativar o comportamento assíncrono adicionando e definindo a propriedade `operationOptions` para `DisableAsyncPattern` sob as inputs da ação.

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

### <a name="run-in-high-throughput-mode"></a>Executar no modo de alta taxa de transferência

Para uma definição de aplicação lógica única, o número de ações que executam a cada 5 minutos tem um [limite padrão](../logic-apps/logic-apps-limits-and-config.md#throughput-limits). Para elevar este limite ao [máximo](../logic-apps/logic-apps-limits-and-config.md#throughput-limits) possível, coloque o `operationOptions` imóvel para `OptimizedForHighThroughput`. Essa configuração coloca seu aplicativo lógico no modo de "alta taxa de transferência".

> [!NOTE]
> O modo de alta taxa de transferência está em versão prévia. Você também pode distribuir uma carga de trabalho entre mais de um aplicativo lógico, conforme necessário.

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

Os pontos de extremidade HTTP e HTTPS dão suporte a diferentes tipos de autenticação. Com base no gatilho ou na ação que você usa para fazer chamadas ou solicitações de saída para acessar esses pontos de extremidade, você pode selecionar entre diferentes intervalos de tipos de autenticação. Para mais informações, consulte [Adicionar autenticação a chamadas de saída](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound).

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre a linguagem de [definição](../logic-apps/logic-apps-workflow-definition-language.md) de fluxo de trabalho
