---
title: Referência para tipos de gatilho e ação na linguagem de definição de fluxo de trabalho – aplicativos lógicos do Azure
description: Guia de referência para tipos de gatilho e ação na linguagem de definição de fluxo de trabalho para aplicativos lógicos do Azure
services: logic-apps
ms.service: logic-apps
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.suite: integration
ms.topic: reference
ms.date: 06/19/2019
ms.openlocfilehash: 0c7fbe209a20182ac2a17fcdb467d1f2a03183e5
ms.sourcegitcommit: c71306fb197b433f7b7d23662d013eaae269dc9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/22/2019
ms.locfileid: "68370724"
---
# <a name="reference-for-trigger-and-action-types-in-workflow-definition-language-for-azure-logic-apps"></a>Referência para tipos de gatilho e ação na linguagem de definição de fluxo de trabalho para aplicativos lógicos do Azure

Esta referência descreve os tipos gerais usados para identificar gatilhos e ações na definição de fluxo de trabalho subjacente do aplicativo lógico, que é descrita e validada pela [linguagem de definição de fluxo de trabalho](../logic-apps/logic-apps-workflow-definition-language.md).
Para localizar gatilhos e ações de conectores específicos que você pode usar em seus aplicativos lógicos, consulte a lista na [visão geral](https://docs.microsoft.com/connectors/)dos conectores.

<a name="triggers-overview"></a>

## <a name="triggers-overview"></a>Visão geral de gatilhos

Cada fluxo de trabalho inclui um gatilho, que define as chamadas que instanciam e iniciam o fluxo de trabalho. Aqui estão as categorias gerais de gatilho:

* Um  gatilho de sondagem, que verifica o ponto de extremidade de um serviço em intervalos regulares

* Um gatilho de *Push* , que cria uma assinatura para um ponto de extremidade e fornece uma *URL de retorno de chamada* para que o ponto de extremidade possa notificar o gatilho quando o evento especificado ocorrer ou se os dados estiverem disponíveis. Em seguida, o gatilho aguarda a resposta do ponto de extremidade antes de disparar. 

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

| Value | Type | Descrição | 
|-------|------|-------------| 
| <*trigger-name*> | Cadeia | O nome do gatilho | 
| <*trigger-type*> | Cadeia | O tipo de gatilho, como "http" ou "ApiConnection" | 
| <*trigger-inputs*> | Objeto JSON | As entradas que definem o comportamento do gatilho | 
| <*time-unit*> | Cadeia | A unidade de tempo que descreve a frequência com que o gatilho é acionado: "Segundo", "minuto", "hora", "dia", "semana", "mês" | 
| <*number-of-time-units*> | Número inteiro | Um valor que especifica com que frequência o gatilho é acionado com base na frequência, que é o número de unidades de tempo a aguardar até que o gatilho seja acionado novamente <p>Aqui estão os intervalos mínimo e máximo: <p>Mês 1-16 meses </br>Diário 1-500 dias </br>Dia 1 a 12000 horas </br>Demorar 1 a 72000 minutos </br>Microssegundo 1 a 9999999 segundos<p>Por exemplo, se o intervalo for 6 e a frequência for "month", a recorrência será a cada seis meses. | 
|||| 

*Adicional*

| Value | Type | Descrição | 
|-------|------|-------------| 
| <*matriz com condições*> | Array | Uma matriz que contém uma ou mais [condições](#trigger-conditions) que determinam se o fluxo de trabalho deve ser executado. Disponível somente para gatilhos. | 
| <*runtime-config-options*> | Objeto JSON | Você pode alterar o comportamento do tempo de `runtimeConfiguration` execução do gatilho definindo propriedades. Para obter mais informações, consulte [definições de configuração de tempo de execução](#runtime-config-options). | 
| <*splitOn-expression*> | Cadeia | Para gatilhos que retornam uma matriz, você pode especificar uma expressão que [divida  ou](#split-on-debatch) delotee itens de matriz em várias instâncias de fluxo de trabalho para processamento. | 
| <*operation-option*> | Cadeia | Você pode alterar o comportamento padrão definindo a `operationOptions` propriedade. Para obter mais informações, consulte [Opções de operação](#operation-options). | 
|||| 

## <a name="trigger-types-list"></a>Lista de tipos de gatilho

Cada tipo de gatilho tem uma interface e entradas diferentes que definem o comportamento do gatilho. 

### <a name="built-in-triggers"></a>Gatilhos internos

| Tipo de acionador | Descrição | 
|--------------|-------------| 
| [**HTTP**](#http-trigger) | Verifica ou *sonda* qualquer ponto de extremidade. Esse ponto de extremidade deve estar em conformidade com um contrato de gatilho específico usando um padrão assíncrono "202" ou retornando uma matriz. | 
| [**HTTPWebhook**](#http-webhook-trigger) | Cria um ponto de extremidade chamável para seu aplicativo lógico, mas chama a URL especificada para registrar ou cancelar o registro. |
| [**Recurrence**](#recurrence-trigger) | Dispara com base em um agendamento definido. Você pode definir uma data e hora futuras para disparar esse gatilho. Com base na frequência, você também pode especificar horas e dias para executar o fluxo de trabalho. | 
| [**Request**](#request-trigger)  | Cria um ponto de extremidade que possa ser chamado para seu aplicativo lógico e também é conhecido como gatilho "manual". Por exemplo, consulte [chamar, disparar ou aninhar fluxos de trabalho com pontos de extremidade http](../logic-apps/logic-apps-http-endpoint.md). | 
||| 

### <a name="managed-api-triggers"></a>Gatilhos de API gerenciada

| Tipo de acionador | Descrição | 
|--------------|-------------| 
| [**ApiConnection**](#apiconnection-trigger) | Verifica ou *sonda* um ponto de extremidade usando [APIs gerenciadas pela Microsoft](../connectors/apis-list.md). | 
| [**ApiConnectionWebhook**](#apiconnectionwebhook-trigger) | Cria um ponto de extremidade chamável para seu aplicativo lógico chamando [APIs gerenciadas pela Microsoft](../connectors/apis-list.md) para assinar e cancelar a assinatura. | 
||| 

## <a name="triggers---detailed-reference"></a>Gatilhos-referência detalhada

<a name="apiconnection-trigger"></a>

### <a name="apiconnection-trigger"></a>Gatilho APIConnection  

Esse gatilho verifica ou *sonda* um ponto de extremidade usando [APIs gerenciadas pela Microsoft](../connectors/apis-list.md) para que os parâmetros para esse gatilho possam diferir com base no ponto de extremidade. Muitas seções nessa definição de gatilho são opcionais. O comportamento do gatilho depende se as seções estão incluídas ou não.

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

| Value | Type | Descrição | 
|-------|------|-------------| 
| <*APIConnection_trigger_name*> | Cadeia | O nome do gatilho | 
| <*nome da conexão*> | Cadeia | O nome da conexão com a API gerenciada que o fluxo de trabalho usa | 
| <*tipo de método*> | Cadeia | O método HTTP para se comunicar com a API gerenciada: "GET", "PUT", "POST", "PATCH", "DELETE" | 
| <*api-operation*> | Cadeia | A operação de API para chamar | 
| <*time-unit*> | Cadeia | A unidade de tempo que descreve a frequência com que o gatilho é acionado: "Segundo", "minuto", "hora", "dia", "semana", "mês" | 
| <*number-of-time-units*> | Número inteiro | Um valor que especifica com que frequência o gatilho é acionado com base na frequência, que é o número de unidades de tempo a aguardar até que o gatilho seja acionado novamente <p>Aqui estão os intervalos mínimo e máximo: <p>Mês 1-16 meses </br>Diário 1-500 dias </br>Dia 1 a 12000 horas </br>Demorar 1 a 72000 minutos </br>Microssegundo 1 a 9999999 segundos<p>Por exemplo, se o intervalo for 6 e a frequência for "month", a recorrência será a cada seis meses. | 
|||| 

*Adicional*

| Value | Type | Descrição | 
|-------|------|-------------| 
| <*comportamento de repetição*> | Objeto JSON | Personaliza o comportamento de repetição para falhas intermitentes, que têm o código de status 408, 429 e 5XX e quaisquer exceções de conectividade. Para obter mais informações, consulte [políticas de repetição](../logic-apps/logic-apps-exception-handling.md#retry-policies). | 
| <*query-parameters*> | Objeto JSON | Qualquer parâmetro de consulta a ser incluído com a chamada à API. Por exemplo, o `"queries": { "api-version": "2018-01-01" }` objeto é `?api-version=2018-01-01` adicionado à chamada. | 
| <*max-runs*> | Número inteiro | Por padrão, as instâncias de fluxo de trabalho são executadas ao mesmo tempo ou em paralelo até o [limite padrão](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Para alterar esse limite definindo um novo valor de >*contagem*de <, consulte [alterar simultaneidade de gatilho](#change-trigger-concurrency). | 
| <*max-runs-queue*> | Número inteiro | Quando o fluxo de trabalho já estiver executando o número máximo de instâncias, que podem ser alteradas `runtimeConfiguration.concurrency.runs` com base na propriedade, todas as novas execuções serão colocadas nessa fila até o [limite padrão](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Para alterar o limite padrão, consulte [alterar o limite](#change-waiting-runs)de execuções em espera. | 
| <*splitOn-expression*> | Cadeia | Para gatilhos que retornam matrizes, essa expressão faz referência à matriz a ser usada para que você possa criar e executar uma instância de fluxo de trabalho para cada item de matriz, em vez de usar um loop "for each". <p>Por exemplo, essa expressão representa um item na matriz retornada no conteúdo do corpo do gatilho:`@triggerbody()?['value']` |
| <*operation-option*> | Cadeia | Você pode alterar o comportamento padrão definindo a `operationOptions` propriedade. Para obter mais informações, consulte [Opções de operação](#operation-options). |
||||

*Saídas*
 
| Elemento | Type | Descrição |
|---------|------|-------------|
| conector | Objeto JSON | Os cabeçalhos da resposta |
| corpo | Objeto JSON | O corpo da resposta |
| código de status | Número inteiro | O código de status da resposta |
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

Esse gatilho envia uma solicitação de assinatura para um ponto de extremidade usando uma [API gerenciada pela Microsoft](../connectors/apis-list.md), fornece uma URL de *retorno de chamada* para onde o ponto de extremidade pode enviar uma resposta e aguarda o ponto de extremidade responder. Para obter mais informações, consulte as [assinaturas do ponto de extremidade](#subscribe-unsubscribe).

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

| Value | Type | Descrição | 
|-------|------|-------------| 
| <*nome da conexão*> | Cadeia | O nome da conexão com a API gerenciada que o fluxo de trabalho usa | 
| <*body-content*> | Objeto JSON | Qualquer conteúdo de mensagem para enviar como carga para a API gerenciada | 
|||| 

*Adicional*

| Valor | Type | Descrição | 
|-------|------|-------------| 
| <*comportamento de repetição*> | Objeto JSON | Personaliza o comportamento de repetição para falhas intermitentes, que têm o código de status 408, 429 e 5XX e quaisquer exceções de conectividade. Para obter mais informações, consulte [políticas de repetição](../logic-apps/logic-apps-exception-handling.md#retry-policies). | 
| <*query-parameters*> | Objeto JSON | Qualquer parâmetro de consulta a ser incluído com a chamada à API <p>Por exemplo, o `"queries": { "api-version": "2018-01-01" }` objeto é `?api-version=2018-01-01` adicionado à chamada. | 
| <*max-runs*> | Número inteiro | Por padrão, as instâncias de fluxo de trabalho são executadas ao mesmo tempo ou em paralelo até o [limite padrão](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Para alterar esse limite definindo um novo valor de >*contagem*de <, consulte [alterar simultaneidade de gatilho](#change-trigger-concurrency). | 
| <*max-runs-queue*> | Número inteiro | Quando o fluxo de trabalho já estiver executando o número máximo de instâncias, que podem ser alteradas `runtimeConfiguration.concurrency.runs` com base na propriedade, todas as novas execuções serão colocadas nessa fila até o [limite padrão](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Para alterar o limite padrão, consulte [alterar o limite](#change-waiting-runs)de execuções em espera. | 
| <*splitOn-expression*> | Cadeia | Para gatilhos que retornam matrizes, essa expressão faz referência à matriz a ser usada para que você possa criar e executar uma instância de fluxo de trabalho para cada item de matriz, em vez de usar um loop "for each". <p>Por exemplo, essa expressão representa um item na matriz retornada no conteúdo do corpo do gatilho:`@triggerbody()?['value']` |
| <*operation-option*> | Cadeia | Você pode alterar o comportamento padrão definindo a `operationOptions` propriedade. Para obter mais informações, consulte [Opções de operação](#operation-options). | 
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

### <a name="http-trigger"></a>Accionador HTTP

Esse gatilho verifica ou sonda o ponto de extremidade especificado com base na agenda de recorrência especificada. A resposta do ponto de extremidade determina se o fluxo de trabalho é executado.

```json
"HTTP": {
   "type": "Http",
   "inputs": {
      "method": "<method-type>",
      "uri": "<endpoint-URL>",
      "headers": { "<header-content>" },
      "body": "<body-content>",
      "authentication": { "<authentication-method>" },
      "retryPolicy": { "<retry-behavior>" },
      "queries": "<query-parameters>"
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

| Value | Type | Descrição | 
|-------|------|-------------| 
| <*tipo de método*> | Cadeia | O método HTTP a ser usado para sondar o ponto de extremidade especificado: "GET", "PUT", "POST", "PATCH", "DELETE" | 
| <*endpoint-URL*> | Cadeia | A URL HTTP ou HTTPS para o ponto de extremidade para sondagem <p>Tamanho máximo da cadeia de caracteres: 2 KB | 
| <*time-unit*> | Cadeia | A unidade de tempo que descreve a frequência com que o gatilho é acionado: "Segundo", "minuto", "hora", "dia", "semana", "mês" | 
| <*number-of-time-units*> | Número inteiro | Um valor que especifica com que frequência o gatilho é acionado com base na frequência, que é o número de unidades de tempo a aguardar até que o gatilho seja acionado novamente <p>Aqui estão os intervalos mínimo e máximo: <p>Mês 1-16 meses </br>Diário 1-500 dias </br>Dia 1 a 12000 horas </br>Demorar 1 a 72000 minutos </br>Microssegundo 1 a 9999999 segundos<p>Por exemplo, se o intervalo for 6 e a frequência for "month", a recorrência será a cada seis meses. | 
|||| 

*Adicional*

| Valor | Type | Descrição | 
|-------|------|-------------| 
| <*header-content*> | Objeto JSON | Os cabeçalhos a serem enviados com a solicitação <p>Por exemplo, para definir o idioma e o tipo de uma solicitação: <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` |
| <*body-content*> | Cadeia | O conteúdo da mensagem a ser enviada como carga com a solicitação | 
| <*método de autenticação*> | Objeto JSON | O método usado pela solicitação para autenticação. Para obter mais informações, consulte [autenticação de saída](../scheduler/scheduler-outbound-authentication.md)do Agendador. Além do Agendador, `authority` há suporte para a propriedade. Quando não especificado, o valor padrão é `https://login.windows.net`, mas você pode usar um valor diferente,`https://login.windows\-ppe.net`como. |
| <*comportamento de repetição*> | Objeto JSON | Personaliza o comportamento de repetição para falhas intermitentes, que têm o código de status 408, 429 e 5XX e quaisquer exceções de conectividade. Para obter mais informações, consulte [políticas de repetição](../logic-apps/logic-apps-exception-handling.md#retry-policies). |  
 <*query-parameters*> | Objeto JSON | Qualquer parâmetro de consulta a ser incluído com a solicitação <p>Por exemplo, o `"queries": { "api-version": "2018-01-01" }` objeto é `?api-version=2018-01-01` adicionado à solicitação. | 
| <*max-runs*> | Número inteiro | Por padrão, as instâncias de fluxo de trabalho são executadas ao mesmo tempo ou em paralelo até o [limite padrão](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Para alterar esse limite definindo um novo valor de >*contagem*de <, consulte [alterar simultaneidade de gatilho](#change-trigger-concurrency). | 
| <*max-runs-queue*> | Número inteiro | Quando o fluxo de trabalho já estiver executando o número máximo de instâncias, que podem ser alteradas `runtimeConfiguration.concurrency.runs` com base na propriedade, todas as novas execuções serão colocadas nessa fila até o [limite padrão](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Para alterar o limite padrão, consulte [alterar o limite](#change-waiting-runs)de execuções em espera. | 
| <*operation-option*> | Cadeia | Você pode alterar o comportamento padrão definindo a `operationOptions` propriedade. Para obter mais informações, consulte [Opções de operação](#operation-options). | 
|||| 

*Saídas*

| Elemento | Type | Descrição |
|---------|------|-------------| 
| conector | Objeto JSON | Os cabeçalhos da resposta | 
| corpo | Objeto JSON | O corpo da resposta | 
| código de status | Número inteiro | O código de status da resposta | 
|||| 

*Requisitos para solicitações de entrada*

Para funcionar bem com seu aplicativo lógico, o ponto de extremidade deve estar em conformidade com um padrão ou contrato de gatilho específico e reconhecer essas propriedades:  
  
| Resposta | Requerido | Descrição | 
|----------|----------|-------------| 
| Código de estado | Sim | O código de status "200 OK" inicia uma execução. Qualquer outro código de status não inicia uma execução. | 
| Cabeçalho repetir-após | Não | O número de segundos até o aplicativo lógico sondar o ponto de extremidade novamente | 
| Cabeçalho do local | Não | A URL a ser chamada no próximo intervalo de sondagem. Se não for especificado, a URL original será usada. | 
|||| 

*Comportamentos de exemplo para solicitações diferentes*

| Código de estado | Tentar novamente após | Comportamento | 
|-------------|-------------|----------|
| 200 | None | Execute o fluxo de trabalho e verifique novamente se há mais dados após a recorrência definida. | 
| 200 | 10 segundos | Execute o fluxo de trabalho e verifique novamente se há mais dados após 10 segundos. |  
| 202 | 60 segundos | Não dispare o fluxo de trabalho. A próxima tentativa ocorrerá em um minuto, sujeito à recorrência definida. Se a recorrência definida for menor que um minuto, o cabeçalho Retry-After terá precedência. Caso contrário, a recorrência definida será usada. | 
| 400 | None | Solicitação inadequada, não execute o fluxo de trabalho. Se não `retryPolicy` for definido, a política padrão será usada. Depois que o número de repetições for atingido, o gatilho verificará novamente os dados após a recorrência definida. | 
| 500 | None| Erro do servidor, não execute o fluxo de trabalho. Se não `retryPolicy` for definido, a política padrão será usada. Depois que o número de repetições for atingido, o gatilho verificará novamente os dados após a recorrência definida. | 
|||| 

<a name="http-webhook-trigger"></a>

### <a name="httpwebhook-trigger"></a>Gatilho HTTPWebhook  

Esse gatilho torna seu aplicativo lógico chamável criando um ponto de extremidade que pode registrar uma assinatura chamando a URL de ponto de extremidade especificada. Quando você cria esse gatilho em seu fluxo de trabalho, uma solicitação de saída faz a chamada para registrar a assinatura. Dessa forma, o gatilho pode iniciar a escuta de eventos. Quando uma operação torna esse gatilho inválido, uma solicitação de saída faz automaticamente a chamada para cancelar a assinatura. Para obter mais informações, consulte as [assinaturas do ponto de extremidade](#subscribe-unsubscribe).

Você também pode especificar [limites assíncronos](#asynchronous-limits) em um gatilho **HTTPWebhook** .
O comportamento do gatilho depende das seções que você usa ou omite. 

```json
"HTTP_Webhook": {
   "type": "HttpWebhook",
   "inputs": {
      "subscribe": {
         "method": "<method-type>",
         "uri": "<endpoint-subscribe-URL>",
         "headers": { "<header-content>" },
         "body": "<body-content>",
         "authentication": { "<authentication-method>" },
         "retryPolicy": { "<retry-behavior>" }
         },
      },
      "unsubscribe": {
         "method": "<method-type>",
         "url": "<endpoint-unsubscribe-URL>",
         "headers": { "<header-content>" },
         "body": "<body-content>",
         "authentication": { "<authentication-method>" }
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

Alguns valores, como < > do*tipo de método*, estão disponíveis para os `"subscribe"` objetos e. `"unsubscribe"`

*Necessário*

| Value | Type | Descrição | 
|-------|------|-------------| 
| <*tipo de método*> | Cadeia | O método HTTP a ser usado para a solicitação de assinatura: "GET", "PUT", "POST", "PATCH" ou "DELETE" | 
| <*endpoint-subscribe-URL*> | Cadeia | A URL do ponto de extremidade para onde enviar a solicitação de assinatura | 
|||| 

*Adicional*

| Value | Type | Descrição | 
|-------|------|-------------| 
| <*tipo de método*> | Cadeia | O método HTTP a ser usado para a solicitação de cancelamento: "GET", "PUT", "POST", "PATCH" ou "DELETE" | 
| <*endpoint-unsubscribe-URL*> | Cadeia | A URL do ponto de extremidade para onde enviar a solicitação de cancelamento | 
| <*body-content*> | Cadeia | Qualquer conteúdo de mensagem para enviar na solicitação de assinatura ou cancelamento | 
| <*método de autenticação*> | Objeto JSON | O método usado pela solicitação para autenticação. Para obter mais informações, consulte [autenticação de saída](../scheduler/scheduler-outbound-authentication.md)do Agendador. |
| <*comportamento de repetição*> | Objeto JSON | Personaliza o comportamento de repetição para falhas intermitentes, que têm o código de status 408, 429 e 5XX e quaisquer exceções de conectividade. Para obter mais informações, consulte [políticas de repetição](../logic-apps/logic-apps-exception-handling.md#retry-policies). | 
| <*max-runs*> | Número inteiro | Por padrão, todas as instâncias de fluxo de trabalho são executadas ao mesmo tempo ou em paralelo até o [limite padrão](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Para alterar esse limite definindo um novo valor de >*contagem*de <, consulte [alterar simultaneidade de gatilho](#change-trigger-concurrency). | 
| <*max-runs-queue*> | Número inteiro | Quando o fluxo de trabalho já estiver executando o número máximo de instâncias, que podem ser alteradas `runtimeConfiguration.concurrency.runs` com base na propriedade, todas as novas execuções serão colocadas nessa fila até o [limite padrão](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Para alterar o limite padrão, consulte [alterar o limite](#change-waiting-runs)de execuções em espera. | 
| <*operation-option*> | Cadeia | Você pode alterar o comportamento padrão definindo a `operationOptions` propriedade. Para obter mais informações, consulte [Opções de operação](#operation-options). | 
|||| 

*Saídas* 

| Elemento | Type | Descrição |
|---------|------|-------------| 
| conector | Objeto JSON | Os cabeçalhos da resposta | 
| corpo | Objeto JSON | O corpo da resposta | 
| código de status | Número inteiro | O código de status da resposta | 
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

| Valor | Type | Descrição | 
|-------|------|-------------| 
| <*time-unit*> | Cadeia | A unidade de tempo que descreve a frequência com que o gatilho é acionado: "Segundo", "minuto", "hora", "dia", "semana", "mês" | 
| <*number-of-time-units*> | Número inteiro | Um valor que especifica com que frequência o gatilho é acionado com base na frequência, que é o número de unidades de tempo a aguardar até que o gatilho seja acionado novamente <p>Aqui estão os intervalos mínimo e máximo: <p>Mês 1-16 meses </br>Diário 1-500 dias </br>Dia 1 a 12000 horas </br>Demorar 1 a 72000 minutos </br>Microssegundo 1 a 9999999 segundos<p>Por exemplo, se o intervalo for 6 e a frequência for "month", a recorrência será a cada seis meses. | 
|||| 

*Adicional*

| Value | Type | Descrição | 
|-------|------|-------------| 
| <*Data de início-hora-com-formato-AAAA-MM-DDThh: mm: SS*> | Cadeia | A data e a hora de início neste formato: <p>AAAA-MM-DDThh: mm: SS se você especificar um fuso horário <p>-ou- <p>AAAA-MM-DDThh: mm: ssZ se você não especificar um fuso horário <p>Por exemplo, se você quiser 18 de setembro de 2017 às 2:00 PM, especifique "2017-09-18T14:00:00" e especifique um fuso horário como "hora padrão do Pacífico" ou especifique "2017-09-18T14:00:00Z" sem um fuso horário. <p>**Nota:** Essa hora de início deve seguir a especificação de data e hora [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations) no [formato de data e hora UTC](https://en.wikipedia.org/wiki/Coordinated_Universal_Time), mas sem um [deslocamento UTC](https://en.wikipedia.org/wiki/UTC_offset). Se você não especificar um fuso horário, deverá adicionar a letra "Z" no final sem espaços. Esse "Z" refere-se ao [tempo náuticas](https://en.wikipedia.org/wiki/Nautical_time)equivalente. <p>Para agendamentos simples, a hora de início é a primeira ocorrência, enquanto para agendas complexas, o gatilho não é acionado antes da hora de início. Para obter mais informações sobre datas e horários de início, consulte [criar e agendar tarefas em execução regularmente](../connectors/connectors-native-recurrence.md). | 
| <*fuso horário*> | Cadeia | Aplica-se somente quando você especifica uma hora de início porque esse gatilho não aceita o [deslocamento UTC](https://en.wikipedia.org/wiki/UTC_offset). Especifique o fuso horário que você deseja aplicar. | 
| <*uma ou mais marcas de hora*> | Matriz de inteiro ou de inteiro | Se você especificar "Day" ou "Week" para `frequency`, poderá especificar um ou mais inteiros de 0 a 23, separados por vírgulas, como as horas do dia em que você deseja executar o fluxo de trabalho. <p>Por exemplo, se você especificar "10", "12" e "14", obterá 10 A.M., 12 PM e 2 PM como as marcas de hora. | 
| <*one-or-more-minute-marks*> | Matriz de inteiro ou de inteiro | Se você especificar "Day" ou "Week" para `frequency`, poderá especificar um ou mais inteiros de 0 a 59, separados por vírgulas, como os minutos da hora em que você deseja executar o fluxo de trabalho. <p>Por exemplo, você pode especificar "30" como a marca de minuto e usar o exemplo anterior para horas do dia, você recebe 10:30, 12:30 PM e 2:30 PM. | 
| weekDays | Matriz de cadeia de caracteres ou cadeia de caracteres | Se você especificar "semana" para `frequency`, poderá especificar um ou mais dias, separados por vírgulas, quando desejar executar o fluxo de trabalho: "Segunda-feira", "terça-feira", "quarta-feira", "quinta-feira", "sexta-feira", "sábado" e "domingo" | 
| <*max-runs*> | Número inteiro | Por padrão, todas as instâncias de fluxo de trabalho são executadas ao mesmo tempo ou em paralelo até o [limite padrão](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Para alterar esse limite definindo um novo valor de >*contagem*de <, consulte [alterar simultaneidade de gatilho](#change-trigger-concurrency). | 
| <*max-runs-queue*> | Número inteiro | Quando o fluxo de trabalho já estiver executando o número máximo de instâncias, que podem ser alteradas `runtimeConfiguration.concurrency.runs` com base na propriedade, todas as novas execuções serão colocadas nessa fila até o [limite padrão](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Para alterar o limite padrão, consulte [alterar o limite](#change-waiting-runs)de execuções em espera. | 
| <*operation-option*> | Cadeia | Você pode alterar o comportamento padrão definindo a `operationOptions` propriedade. Para obter mais informações, consulte [Opções de operação](#operation-options). | 
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

Para obter mais informações e exemplos para esse gatilho, consulte [criar e agendar tarefas em execução regularmente](../connectors/connectors-native-recurrence.md).

<a name="request-trigger"></a>

### <a name="request-trigger"></a>Gatilho de solicitação

Esse gatilho torna seu aplicativo lógico chamável criando um ponto de extremidade que pode aceitar solicitações de entrada. Para esse gatilho, forneça um esquema JSON que descreve e valida a carga ou as entradas que o gatilho recebe da solicitação de entrada. O esquema também torna as propriedades de gatilho mais fáceis de fazer referência a partir de ações posteriores no fluxo de trabalho. 

Para chamar esse gatilho, você deve usar a `listCallbackUrl` API, que é descrita na [API REST do serviço de fluxo de trabalho](https://docs.microsoft.com/rest/api/logic/workflows). Para saber como usar esse gatilho como um ponto de extremidade HTTP, consulte [chamar, disparar ou aninhar fluxos de trabalho com pontos de extremidades http](../logic-apps/logic-apps-http-endpoint.md).

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

| Valor | Type | Descrição | 
|-------|------|-------------| 
| <*nome da propriedade*> | Cadeia | O nome de uma propriedade no esquema JSON, que descreve a carga | 
| <*property-type*> | Cadeia | O tipo da propriedade | 
|||| 

*Adicional*

| Value | Type | Descrição | 
|-------|------|-------------| 
| <*tipo de método*> | Cadeia | O método que as solicitações de entrada devem usar para chamar seu aplicativo lógico: "GET", "PUT", "POST", "PATCH", "DELETE" |
| <*relative-path-for-accepted-parameter*> | Cadeia | O caminho relativo para o parâmetro que a URL do ponto de extremidade pode aceitar | 
| <*obrigatório-Propriedades*> | Array | Uma ou mais propriedades que exigem valores | 
| <*max-runs*> | Número inteiro | Por padrão, todas as instâncias de fluxo de trabalho são executadas ao mesmo tempo ou em paralelo até o [limite padrão](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Para alterar esse limite definindo um novo valor de >*contagem*de <, consulte [alterar simultaneidade de gatilho](#change-trigger-concurrency). | 
| <*max-runs-queue*> | Número inteiro | Quando o fluxo de trabalho já estiver executando o número máximo de instâncias, que podem ser alteradas `runtimeConfiguration.concurrency.runs` com base na propriedade, todas as novas execuções serão colocadas nessa fila até o [limite padrão](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Para alterar o limite padrão, consulte [alterar o limite](#change-waiting-runs)de execuções em espera. | 
| <*operation-option*> | Cadeia | Você pode alterar o comportamento padrão definindo a `operationOptions` propriedade. Para obter mais informações, consulte [Opções de operação](#operation-options). | 
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

Para qualquer gatilho e somente gatilhos, você pode incluir uma matriz que contém uma ou mais expressões para condições que determinam se o fluxo de trabalho deve ser executado. Para adicionar a `conditions` Propriedade a um gatilho em seu fluxo de trabalho, abra seu aplicativo lógico no editor de exibição de código.

Por exemplo, você pode especificar que um gatilho seja acionado somente quando um site retornar um erro de servidor interno referenciando o código de status `conditions` do gatilho na propriedade:

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

Se o gatilho retornar uma matriz para o aplicativo lógico processar, às vezes um loop "for each" pode demorar muito para processar cada item da matriz. Em vez disso, você pode  usar a propriedade splitize em seu gatilho para deslote da matriz. O desenvio em lote divide os itens de matriz e inicia uma nova instância de fluxo de trabalho que é executada para cada item de matriz. Essa abordagem é útil, por exemplo, quando você deseja sondar um ponto de extremidade que possa retornar vários novos itens entre intervalos de sondagem.
Para o número máximo de itens de matriz que a **divisão** pode processar em uma única execução de aplicativo lógico, consulte [limites e configuração](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). 

> [!NOTE]
> Não é possível usar a **divisão** com um padrão de resposta síncrona. Qualquer fluxo de trabalho que usa a **divisão** e inclui uma ação de resposta é executado de `202 ACCEPTED` forma assíncrona e envia imediatamente uma resposta.

Se o arquivo Swagger do gatilho descrever uma carga que é uma matriz, a propriedade de **divisão** será adicionada automaticamente ao gatilho. Caso contrário, adicione essa propriedade dentro da carga de resposta que tem a matriz que você deseja deslotear. 

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
 
Seu aplicativo lógico precisa apenas do conteúdo da matriz no `Rows`, para que você possa criar um gatilho como este exemplo:

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
> Se você usar o `SplitOn` comando, não poderá obter as propriedades que estão fora da matriz. Portanto, para este exemplo, você não pode `status` obter a propriedade na resposta retornada da API.
> 
> Para evitar uma falha se a `Rows` propriedade não existir, este exemplo usará `?` o operador.

Sua definição de fluxo de trabalho `@triggerBody().name` agora pode usar `name` para obter os valores `"customer-name-one"` , que são da primeira `"customer-name-two"` execução e da segunda execução. Portanto, as saídas do gatilho são semelhantes a estes exemplos:

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

Os aplicativos lógicos do Azure fornecem vários tipos de ação, cada um com entradas diferentes que definem o comportamento exclusivo de uma ação. 

As ações têm esses elementos de alto nível, embora alguns sejam opcionais:

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

| Valor | Type | Descrição | 
|-------|------|-------------|
| <*nome da ação*> | Cadeia | O nome da ação | 
| <*action-type*> | Cadeia | O tipo de ação, por exemplo, "http" ou "ApiConnection"| 
| <*input-name*> | Cadeia | O nome de uma entrada que define o comportamento da ação | 
| <*entrada-valor*> | Vários | O valor de entrada, que pode ser uma cadeia de caracteres, um inteiro, um objeto JSON e assim por diante | 
| <*previous-trigger-or-action-status*> | Objeto JSON | O nome e o status resultante para o gatilho ou ação que deve ser executado imediatamente antes que esta ação atual possa ser executada | 
|||| 

*Adicional*

| Value | Type | Descrição | 
|-------|------|-------------|
| <*comportamento de repetição*> | Objeto JSON | Personaliza o comportamento de repetição para falhas intermitentes, que têm o código de status 408, 429 e 5XX e quaisquer exceções de conectividade. Para obter mais informações, consulte políticas de repetição. | 
| <*runtime-config-options*> | Objeto JSON | Para algumas ações, você pode alterar o comportamento da ação em tempo `runtimeConfiguration` de execução definindo propriedades. Para obter mais informações, consulte [definições de configuração de tempo de execução](#runtime-config-options). | 
| <*operation-option*> | Cadeia | Para algumas ações, você pode alterar o comportamento padrão definindo a `operationOptions` propriedade. Para obter mais informações, consulte [Opções de operação](#operation-options). | 
|||| 

## <a name="action-types-list"></a>Lista de tipos de ação

Aqui estão alguns tipos de ação usados com frequência: 

* [Tipos de ação internos](#built-in-actions) , como estes exemplos e mais: 

  * [**Http**](#http-action) para chamar pontos de extremidade via http ou https

  * [**Resposta**](#response-action) para responder a solicitações

  * [**Executar código JavaScript**](#run-javascript-code) para executar trechos de código JavaScript

  * [**Função**](#function-action) para chamar Azure Functions

  * Ações de operação de dados como [**unir**](#join-action), [**compor**](#compose-action), [**tabela**](#table-action), [**selecionar**](#select-action)e outros que criam ou transformam dados de várias entradas

  * [**Fluxo de trabalho**](#workflow-action) para chamar outro fluxo de trabalho de aplicativo lógico

* [Tipos de ação de API gerenciada](#managed-api-actions) , como [**ApiConnection**](#apiconnection-action) e [**ApiConnectionWebHook**](#apiconnectionwebhook-action) , que chamam vários conectores e APIs gerenciados pela Microsoft, por exemplo, barramento de serviço do azure, Office 365 Outlook, Power bi, armazenamento de BLOBs do Azure, onedrive, github e muito mais

* [Controlar tipos de ação de fluxo de trabalho](#control-workflow-actions) como [**If**](#if-action), [**foreach**](#foreach-action), [**switch**](#switch-action), [**Scope**](#scope-action)e [**until**](#until-action), que contêm outras ações e ajudar a organizar a execução do fluxo de trabalho

<a name="built-in-actions"></a>

### <a name="built-in-actions"></a>Ações incorporadas

| Tipo de ação | Descrição | 
|-------------|-------------| 
| [**Compose**](#compose-action) | Cria uma única saída de entradas, que pode ter vários tipos. | 
| [**Executar código JavaScript**](#run-javascript-code) | Executar trechos de código JavaScript que se enquadram dentro de critérios específicos. Para obter os requisitos de código e mais informações, consulte [Adicionar e executar trechos de código com código embutido](../logic-apps/logic-apps-add-run-inline-code.md). |
| [**Funcionamento**](#function-action) | Chama uma função do Azure. | 
| [**HTTP**](#http-action) | Chama um ponto de extremidade HTTP. | 
| [**Em**](#join-action) | Cria uma cadeia de caracteres de todos os itens em uma matriz e separa esses itens com um caractere delimitador especificado. | 
| [**Analisar JSON**](#parse-json-action) | Cria tokens amigáveis de propriedades no conteúdo JSON. Em seguida, você pode fazer referência a essas propriedades incluindo os tokens em seu aplicativo lógico. | 
| [**Consultá**](#query-action) | Cria uma matriz de itens em outra matriz com base em uma condição ou filtro. | 
| [**Responde**](#response-action) | Cria uma resposta a uma chamada ou solicitação recebida. | 
| [**Não**](#select-action) | Cria uma matriz com objetos JSON transformando itens de outra matriz com base no mapa especificado. | 
| [**Tabela**](#table-action) | Cria uma tabela CSV ou HTML de uma matriz. | 
| [**Encerrar**](#terminate-action) | Interrompe um fluxo de trabalho em execução ativamente. | 
| [**Wait**](#wait-action) | Pausa o fluxo de trabalho por uma duração especificada ou até a data e hora especificadas. | 
| [**Modelo**](#workflow-action) | Aninha um fluxo de trabalho dentro de outro fluxo de trabalho. | 
||| 

<a name="managed-api-actions"></a>

### <a name="managed-api-actions"></a>Ações de API gerenciadas

| Tipo de ação | Descrição | 
|-------------|-------------|  
| [**ApiConnection**](#apiconnection-action) | Chama um ponto de extremidade HTTP usando uma [API gerenciada pela Microsoft](../connectors/apis-list.md). | 
| [**ApiConnectionWebhook**](#apiconnectionwebhook-action) | Funciona como webhook HTTP, mas usa uma [API gerenciada pela Microsoft](../connectors/apis-list.md). | 
||| 

<a name="control-workflow-actions"></a>

### <a name="control-workflow-actions"></a>Ações de fluxo de trabalho de controle

Essas ações ajudam a controlar a execução do fluxo de trabalho e a incluir outras ações. De fora de uma ação de fluxo de trabalho de controle, você pode referenciar ações diretamente dentro dessa ação de fluxo de trabalho de controle. Por exemplo, se você tiver uma `Http` ação dentro de um escopo, poderá fazer referência `@body('Http')` à expressão de qualquer lugar no fluxo de trabalho. No entanto, as ações existentes dentro de uma ação de fluxo de trabalho de controle só podem ser "executadas após" outras ações que estão na mesma estrutura de fluxo de trabalho de controle.

| Tipo de ação | Descrição | 
|-------------|-------------| 
| [**ForEach**](#foreach-action) | Execute as mesmas ações em um loop para cada item em uma matriz. | 
| [**If**](#if-action) | Execute ações com base em se a condição especificada é true ou false. | 
| [**Com**](#scope-action) | Execute ações com base no status do grupo de um conjunto de ações. | 
| [**Comutador**](#switch-action) | Execute ações organizadas em casos em que os valores de expressões, objetos ou tokens correspondam aos valores especificados por cada caso. | 
| [**Haja**](#until-action) | Execute ações em um loop até que a condição especificada seja verdadeira. | 
|||  

## <a name="actions---detailed-reference"></a>Ações-referência detalhada

<a name="apiconnection-action"></a>

### <a name="apiconnection-action"></a>Ação APIConnection

Essa ação envia uma solicitação HTTP para uma [API gerenciada pela Microsoft](../connectors/apis-list.md) e requer informações sobre a API e os parâmetros, além de uma referência a uma conexão válida. 

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

| Valor | Type | Descrição | 
|-------|------|-------------| 
| <*nome da ação*> | Cadeia | O nome da ação fornecida pelo conector | 
| <*api-name*> | Cadeia | O nome da API gerenciada pela Microsoft que é usada para a conexão | 
| <*tipo de método*> | Cadeia | O método HTTP para chamar a API: "GET", "PUT", "POST", "PATCH" ou "DELETE" | 
| <*api-operation*> | Cadeia | A operação de API para chamar | 
|||| 

*Adicional*

| Value | Type | Descrição | 
|-------|------|-------------| 
| <*other-action-specific-input-properties*> | Objeto JSON | Todas as outras propriedades de entrada que se aplicam a essa ação específica | 
| <*comportamento de repetição*> | Objeto JSON | Personaliza o comportamento de repetição para falhas intermitentes, que têm o código de status 408, 429 e 5XX e quaisquer exceções de conectividade. Para obter mais informações, consulte [políticas de repetição](../logic-apps/logic-apps-exception-handling.md#retry-policies). | 
| <*query-parameters*> | Objeto JSON | Qualquer parâmetro de consulta a ser incluído com a chamada à API. <p>Por exemplo, o `"queries": { "api-version": "2018-01-01" }` objeto é `?api-version=2018-01-01` adicionado à chamada. | 
| <*outras propriedades de ação específica*> | Objeto JSON | Todas as outras propriedades que se aplicam a essa ação específica | 
|||| 

*Exemplo*

Essa definição descreve a ação **enviar um email** para o conector Outlook do Office 365, que é uma API gerenciada pela Microsoft: 

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

Essa ação envia uma solicitação de assinatura por HTTP para um ponto de extremidade usando uma [API gerenciada pela Microsoft](../connectors/apis-list.md), fornece uma URL de *retorno de chamada* para onde o ponto de extremidade pode enviar uma resposta e aguarda o ponto de extremidade responder. Para obter mais informações, consulte as [assinaturas do ponto de extremidade](#subscribe-unsubscribe).

```json
"<action-name>": {
   "type": "ApiConnectionWebhook",
   "inputs": {
      "subscribe": {
         "method": "<method-type>",
         "uri": "<api-subscribe-URL>",
         "headers": { "<header-content>" },
         "body": "<body-content>",
         "authentication": { "<authentication-method>" },
         "retryPolicy": "<retry-behavior>",
         "queries": { "<query-parameters>" },
         "<other-action-specific-input-properties>"
      },
      "unsubscribe": {
         "method": "<method-type>",
         "uri": "<api-unsubscribe-URL>",
         "headers": { "<header-content>" },
         "body": "<body-content>",
         "authentication": { "<authentication-method>" },
         "<other-action-specific-properties>"
      },
   },
   "runAfter": {}
}
```

Alguns valores, como < > do*tipo de método*, estão disponíveis para os `"subscribe"` objetos e. `"unsubscribe"`

*Necessário*

| Value | Type | Descrição | 
|-------|------|-------------| 
| <*nome da ação*> | Cadeia | O nome da ação fornecida pelo conector | 
| <*tipo de método*> | Cadeia | O método HTTP a ser usado para assinatura ou cancelamento de um ponto de extremidade: "GET", "PUT", "POST", "PATCH" ou "DELETE" | 
| <*api-subscribe-URL*> | Cadeia | O URI a ser usado para assinar a API | 
|||| 

*Adicional*

| Value | Type | Descrição | 
|-------|------|-------------| 
| <*api-unsubscribe-URL*> | Cadeia | O URI a ser usado para cancelar a da API | 
| <*header-content*> | Objeto JSON | Todos os cabeçalhos a serem enviados na solicitação <p>Por exemplo, para definir o idioma e o tipo em uma solicitação: <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` |
| <*body-content*> | Objeto JSON | Qualquer conteúdo de mensagem para enviar na solicitação | 
| <*método de autenticação*> | Objeto JSON | O método usado pela solicitação para autenticação. Para obter mais informações, consulte [autenticação de saída](../scheduler/scheduler-outbound-authentication.md)do Agendador. |
| <*comportamento de repetição*> | Objeto JSON | Personaliza o comportamento de repetição para falhas intermitentes, que têm o código de status 408, 429 e 5XX e quaisquer exceções de conectividade. Para obter mais informações, consulte [políticas de repetição](../logic-apps/logic-apps-exception-handling.md#retry-policies). | 
| <*query-parameters*> | Objeto JSON | Qualquer parâmetro de consulta a ser incluído com a chamada à API <p>Por exemplo, o `"queries": { "api-version": "2018-01-01" }` objeto é `?api-version=2018-01-01` adicionado à chamada. | 
| <*other-action-specific-input-properties*> | Objeto JSON | Todas as outras propriedades de entrada que se aplicam a essa ação específica | 
| <*outras propriedades de ação específica*> | Objeto JSON | Todas as outras propriedades que se aplicam a essa ação específica | 
|||| 

Você também pode especificar limites em uma ação **ApiConnectionWebhook** da mesma forma que [os limites assíncronos http](#asynchronous-limits).

<a name="compose-action"></a>

### <a name="compose-action"></a>Ação de composição

Essa ação cria uma única saída de várias entradas, incluindo expressões. Tanto a saída quanto as entradas podem ter qualquer tipo ao qual os aplicativos lógicos do Azure dão suporte nativo, como matrizes, objetos JSON, XML e binário.
Em seguida, você pode usar a saída da ação em outras ações. 

```json
"Compose": {
   "type": "Compose",
   "inputs": "<inputs-to-compose>",
   "runAfter": {}
},
```

*Necessário* 

| Value | Type | Descrição | 
|-------|------|-------------| 
| <*inputs-to-compose*> | Any | As entradas para criar uma única saída | 
|||| 

*Exemplo 1*

<!-- markdownlint-disable MD038 -->
Esta definição de ação é `abcdefg ` mesclada com um espaço à direita e `1234`o valor:
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

Essa definição de ação mescla uma variável de cadeia de `abcdefg` caracteres que contém e uma variável `1234`de inteiro que contém:

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

Essa ação executa um trecho de código JavaScript e retorna os resultados por `Result` meio de um token que as ações posteriores podem referenciar.

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

| Valor | Type | Descrição |
|-------|------|-------------|
| <*JavaScript-code-snippet*> | Varia | O código JavaScript que você deseja executar. Para obter os requisitos de código e mais informações, consulte [Adicionar e executar trechos de código com código embutido](../logic-apps/logic-apps-add-run-inline-code.md). <p>No atributo, seu trecho de código pode usar o objeto somente `workflowContext` leitura como entrada. `code` Esse objeto tem subpropriedades que dão ao seu código acesso aos resultados do gatilho e ações anteriores em seu fluxo de trabalho. Para obter mais informações sobre `workflowContext` o objeto, consulte [gatilho de referência e resultados de ação em seu código](../logic-apps/logic-apps-add-run-inline-code.md#workflowcontext). |
||||

*Necessário em alguns casos*

O `explicitDependencies` atributo especifica que você deseja incluir explicitamente os resultados do gatilho, ações anteriores ou ambas como dependências para o trecho de código. Para obter mais informações sobre como adicionar essas dependências, consulte [adicionar parâmetros para código embutido](../logic-apps/logic-apps-add-run-inline-code.md#add-parameters). 

Para o `includeTrigger` atributo, você pode especificar `true` ou `false` valores.

| Value | Type | Descrição |
|-------|------|-------------|
| <*ações anteriores*> | Matriz de cadeia de caracteres | Uma matriz com os nomes de ação especificados. Use os nomes de ação que aparecem na definição de fluxo de trabalho em que os nomes de ação usam sublinhados (_), não espaços (""). |
||||

*Exemplo 1*

Esta ação executa o código que obtém o nome do aplicativo lógico e retorna o texto "Olá, \<mundo de Logic-app-Name >" como resultado. Neste exemplo, o código faz referência ao nome do fluxo de trabalho acessando a `workflowContext.workflow.name` Propriedade por meio do objeto somente `workflowContext` leitura. Para obter mais informações sobre como `workflowContext` usar o objeto, consulte [gatilho de referência e resultados de ação em seu código](../logic-apps/logic-apps-add-run-inline-code.md#workflowcontext).

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

O código extrai endereços de email da Propriedade do `Body` gatilho e retorna esses endereços de email junto com o `SelectedOption` valor da propriedade da ação de aprovação. A ação inclui explicitamente a ação enviar email de aprovação como uma dependência no `explicitDependencies`  >  `actions` atributo.

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

Essa ação chama uma [função do Azure](../azure-functions/functions-create-first-azure-function.md)criada anteriormente.

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

| Valor | Type | Descrição | 
|-------|------|-------------|  
| <*Azure-function-ID*> | Cadeia | A ID de recurso para a função do Azure que você deseja chamar. Este é o formato para este valor:<p>"/subscriptions/<*Azure-Subscription-ID*>/ResourceGroups/<*Azure-resource-group*>/Providers/Microsoft.Web/sites/<*Azure-function-app-Name*>/Functions/<*Azure-function-name*> " | 
| <*tipo de método*> | Cadeia | O método HTTP a ser usado para chamar a função: "GET", "PUT", "POST", "PATCH" ou "DELETE" <p>Se não for especificado, o padrão será o método "POST". | 
||||

*Adicional*

| Value | Type | Descrição | 
|-------|------|-------------|  
| <*header-content*> | Objeto JSON | Todos os cabeçalhos a serem enviados com a chamada <p>Por exemplo, para definir o idioma e o tipo em uma solicitação: <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` |
| <*body-content*> | Objeto JSON | Qualquer conteúdo de mensagem para enviar na solicitação | 
| <*query-parameters*> | Objeto JSON | Qualquer parâmetro de consulta a ser incluído com a chamada à API <p>Por exemplo, o `"queries": { "api-version": "2018-01-01" }` objeto é `?api-version=2018-01-01` adicionado à chamada. | 
| <*other-action-specific-input-properties*> | Objeto JSON | Todas as outras propriedades de entrada que se aplicam a essa ação específica | 
| <*outras propriedades de ação específica*> | Objeto JSON | Todas as outras propriedades que se aplicam a essa ação específica | 
||||

Quando você salva seu aplicativo lógico, o mecanismo de aplicativos lógicos executa essas verificações na função referenciada:

* Seu fluxo de trabalho deve ter acesso à função.

* Seu fluxo de trabalho pode usar apenas um gatilho HTTP padrão ou um gatilho de webhook JSON genérico. 

  O mecanismo de aplicativos lógicos Obtém e armazena em cache a URL do gatilho, que é usada em tempo de execução. No entanto, se qualquer operação invalida a URL armazenada em cache, a ação da **função** falhará em tempo de execução. Para corrigir esse problema, salve o aplicativo lógico novamente para que o aplicativo lógico obtenha e armazene em cache a URL do gatilho novamente.

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

Essa ação envia uma solicitação para o ponto de extremidade especificado e verifica a resposta para determinar se o fluxo de trabalho deve ser executado. 

```json
"HTTP": {
   "type": "Http",
   "inputs": {
      "method": "<method-type>",
      "uri": "<HTTP-or-HTTPS-endpoint-URL>"
   },
   "runAfter": {}
}
```

*Necessário*

| Valor | Type | Descrição | 
|-------|------|-------------| 
| <*tipo de método*> | Cadeia | O método a ser usado para enviar a solicitação: "GET", "PUT", "POST", "PATCH" ou "DELETE" | 
| <*HTTP-or-HTTPS-endpoint-URL*> | Cadeia | O ponto de extremidade HTTP ou HTTPS para chamar. Tamanho máximo da cadeia de caracteres: 2 KB | 
|||| 

*Adicional*

| Value | Type | Descrição | 
|-------|------|-------------| 
| <*header-content*> | Objeto JSON | Todos os cabeçalhos a serem enviados com a solicitação <p>Por exemplo, para definir o idioma e o tipo: <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` |
| <*body-content*> | Objeto JSON | Qualquer conteúdo de mensagem para enviar na solicitação | 
| <*comportamento de repetição*> | Objeto JSON | Personaliza o comportamento de repetição para falhas intermitentes, que têm o código de status 408, 429 e 5XX e quaisquer exceções de conectividade. Para obter mais informações, consulte [políticas de repetição](../logic-apps/logic-apps-exception-handling.md#retry-policies). | 
| <*query-parameters*> | Objeto JSON | Qualquer parâmetro de consulta a ser incluído com a solicitação <p>Por exemplo, o `"queries": { "api-version": "2018-01-01" }` objeto é `?api-version=2018-01-01` adicionado à chamada. | 
| <*other-action-specific-input-properties*> | Objeto JSON | Todas as outras propriedades de entrada que se aplicam a essa ação específica | 
| <*outras propriedades de ação específica*> | Objeto JSON | Todas as outras propriedades que se aplicam a essa ação específica | 
|||| 

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

### <a name="join-action"></a>Ação de junção

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

| Value | Type | Descrição | 
|-------|------|-------------| 
| <*array*> | Array | A matriz ou expressão que fornece os itens de origem. Se você especificar uma expressão, coloque essa expressão entre aspas duplas. | 
| <*delimitador*> | Cadeia de caracteres única | O caractere que separa cada item na cadeia de caracteres | 
|||| 

*Exemplo*

Suponha que você tenha uma variável "myIntegerArray" criada anteriormente que contenha essa matriz de inteiros: 

`[1,2,3,4]` 

Essa definição de ação Obtém os valores da variável usando a `variables()` função em uma expressão e cria essa cadeia de caracteres com esses valores, que são separados por uma vírgula:`"1,2,3,4"`

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

### <a name="parse-json-action"></a>Analisar ação JSON

Esta ação cria campos amigáveis ou *tokens* das propriedades no conteúdo JSON. Em vez disso, você pode acessar essas propriedades em seu aplicativo lógico usando os tokens. Por exemplo, quando você deseja usar a saída JSON de serviços como o barramento de serviço do Azure e Azure Cosmos DB, você pode incluir essa ação em seu aplicativo lógico para que você possa referenciar mais facilmente os dados nessa saída. 

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

| Value | Type | Descrição | 
|-------|------|-------------| 
| <*Origem JSON*> | Objeto JSON | O conteúdo JSON que você deseja analisar | 
| <*JSON-schema*> | Objeto JSON | O esquema JSON que descreve a base do conteúdo JSON, que a ação usa para analisar o conteúdo JSON de origem. <p>**Dica**: No designer de aplicativos lógicos, você pode fornecer o esquema ou fornecer um conteúdo de exemplo para que a ação possa gerar o esquema. | 
|||| 

*Exemplo*

Essa definição de ação cria esses tokens que você pode usar em seu fluxo de trabalho, mas somente em ações que são executadas após a ação **analisar JSON** : 

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

| Value | Type | Descrição | 
|-------|------|-------------| 
| <*array*> | Array | A matriz ou expressão que fornece os itens de origem. Se você especificar uma expressão, coloque essa expressão entre aspas duplas. |
| <*condição ou filtro*> | Cadeia | A condição usada para filtrar itens na matriz de origem <p>**Nota**: Se nenhum valor atender à condição, a ação criará uma matriz vazia. |
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

| Value | Type | Descrição | 
|-------|------|-------------| 
| <*response-status-code*> | Número inteiro | O código de status HTTP que é enviado para a solicitação de entrada. O código padrão é "200 OK", mas o código pode ser qualquer código de status válido que comece com 2xx, 4xx ou 5xx, mas não com 3xxx. | 
|||| 

*Adicional*

| Value | Type | Descrição | 
|-------|------|-------------| 
| <*response-headers*> | Objeto JSON | Um ou mais cabeçalhos a serem incluídos com a resposta | 
| <*corpo da resposta*> | Vários | O corpo da resposta, que pode ser uma cadeia de caracteres, um objeto JSON ou até mesmo conteúdo binário de uma ação anterior | 
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

*Quanto*

Ao contrário de outras ações, a ação de **resposta** tem restrições especiais: 

* O fluxo de trabalho pode usar a ação de **resposta** somente quando o fluxo de trabalho começa com um gatilho de solicitação HTTP, o que significa que seu fluxo de trabalho deve ser disparado por uma solicitação HTTP.

* O fluxo de trabalho pode usar a ação de **resposta** em qualquer lugar, *exceto* dentro de loops **foreach** , **até** loops, incluindo loops sequenciais e ramificações paralelas. 

* A solicitação HTTP original Obtém a resposta do fluxo de trabalho somente quando todas as ações exigidas pela ação de **resposta** são concluídas dentro do [limite de tempo limite da solicitação HTTP](../logic-apps/logic-apps-limits-and-config.md#request-limits).

  No entanto, se seu fluxo de trabalho chama outro aplicativo lógico como um fluxo de trabalho aninhado, o fluxo de trabalho pai aguarda até que o fluxo de trabalho aninhado seja concluído, não importa quanto tempo o fluxo de trabalho aninhado é concluído.

* Quando o fluxo de trabalho usa a ação de **resposta** e um padrão de resposta síncrona, o fluxo de trabalho também não pode usar o comando de **divisão** na definição do gatilho porque esse comando cria várias execuções. Verifique esse caso quando o método PUT for usado e, se for true, retornar uma resposta de "solicitação inválida".

  Caso contrário, se o fluxo de trabalho usar o comando de **divisão** e uma ação de **resposta** , o fluxo de trabalho será executado de forma assíncrona e imediatamente retornará uma resposta "202 aceito".

* Quando a execução do fluxo de trabalho atinge a ação de **resposta** , mas a solicitação de entrada já recebeu uma resposta, a ação de **resposta** é marcada como "falha" devido ao conflito. E, como resultado, a execução do aplicativo lógico também é marcada com o status "falha".

<a name="select-action"></a>

### <a name="select-action"></a>Selecionar ação

Essa ação cria uma matriz com objetos JSON transformando itens de outra matriz com base no mapa especificado. A matriz de saída e a matriz de origem sempre têm o mesmo número de itens. Embora não seja possível alterar o número de objetos na matriz de saída, você pode adicionar ou remover propriedades e seus valores entre esses objetos. A `select` propriedade especifica pelo menos um par chave-valor que define o mapa para transformar itens na matriz de origem. Um par chave-valor representa uma propriedade e seu valor em todos os objetos na matriz de saída. 

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

| Value | Type | Descrição | 
|-------|------|-------------| 
| <*array*> | Array | A matriz ou expressão que fornece os itens de origem. Certifique-se de colocar uma expressão com aspas duplas. <p>**Nota**: Se a matriz de origem estiver vazia, a ação criará uma matriz vazia. | 
| <*key-name*> | Cadeia | O nome da propriedade atribuído ao resultado da*expressão* <> <p>Para adicionar uma nova propriedade em todos os objetos na matriz de saída, forneça um < > de*nome de chave*para essa propriedade e uma*expressão*de < > para o valor da propriedade. <p>Para remover uma propriedade de todos os objetos na matriz, omita o < > de*nome de chave*para essa propriedade. | 
| <*expressão*> | Cadeia | A expressão que transforma o item na matriz de origem e atribui o resultado a <*nome-chave*> | 
|||| 

A ação **selecionar** cria uma matriz como saída, portanto, qualquer ação que queira usar essa saída deve aceitar uma matriz ou você deve converter a matriz no tipo que a ação do consumidor aceita. Por exemplo, para converter a matriz de saída em uma cadeia de caracteres, você pode passar essa  matriz para a ação compor e, em seguida, fazer referência à saída da ação **compor** em suas outras ações.

*Exemplo*

Essa definição de ação cria uma matriz de objeto JSON a partir de uma matriz de inteiros. A ação faz a iteração pela matriz de origem, obtém cada valor inteiro usando `@item()` a expressão e atribui cada valor à propriedade "`number`" em cada objeto JSON: 

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

Para usar essa saída de matriz em outras ações, passe esta saída para uma ação de **composição** :

```json
"Compose": {
   "type": "Compose",
   "inputs": "@body('Select')",
   "runAfter": {
      "Select": [ "Succeeded" ]
   }
},
```

Você pode usar a saída da ação **compor** em suas outras ações, por exemplo, o **Office 365 Outlook – enviar uma** ação de email:

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

Essa ação cria uma tabela CSV ou HTML de uma matriz. Para matrizes com objetos JSON, essa ação cria automaticamente os cabeçalhos de coluna dos nomes de propriedade dos objetos. Para matrizes com outros tipos de dados, você deve especificar os cabeçalhos e valores de coluna. Por exemplo, essa matriz inclui as propriedades "ID" e "Product_Name" que essa ação pode usar para os nomes de cabeçalho de coluna:

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

| Value | Type | Descrição | 
|-------|------|-------------| 
| \<> CSV *ou* HTML| Cadeia | O formato da tabela que você deseja criar | 
| <*array*> | Array | A matriz ou expressão que fornece os itens de origem para a tabela <p>**Nota**: Se a matriz de origem estiver vazia, a ação criará uma tabela vazia. | 
|||| 

*Adicional*

Para especificar ou personalizar cabeçalhos e valores de coluna, use `columns` a matriz. Quando `header-value` os pares têm o mesmo nome de cabeçalho, seus valores aparecem na mesma coluna sob esse nome de cabeçalho. Caso contrário, cada cabeçalho exclusivo define uma coluna exclusiva.

| Value | Type | Descrição | 
|-------|------|-------------| 
| <*column-name*> | Cadeia | O nome do cabeçalho de uma coluna | 
| <*coluna-valor*> | Any | O valor nessa coluna | 
|||| 

*Exemplo 1*

Suponha que você tenha uma variável "myItemArray" criada anteriormente que contenha atualmente esta matriz: 

`[ {"ID": 0, "Product_Name": "Apples"}, {"ID": 1, "Product_Name": "Oranges"} ]`

Essa definição de ação cria uma tabela CSV a partir da variável "myItemArray". A expressão usada pela `from` propriedade Obtém a matriz de "myItemArray" usando a `variables()` função: 

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

Essa definição de ação cria uma tabela HTML a partir da variável "myItemArray". A expressão usada pela `from` propriedade Obtém a matriz de "myItemArray" usando a `variables()` função: 

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

<table><thead><tr><th>id</th><th>Product_Name</th></tr></thead><tbody><tr><td>0</td><td>Aplica</td></tr><tr><td>1</td><td>Laranjas</td></tr></tbody></table>

*Exemplo 3*

Essa definição de ação cria uma tabela HTML a partir da variável "myItemArray". No entanto, este exemplo substitui os nomes de cabeçalho de coluna padrão por "Stock_ID" e "Description" e adiciona a palavra "orgânica" aos valores na coluna "Descrição".

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

Essa ação interrompe a execução de uma instância de fluxo de trabalho, cancela as ações em andamento, ignora as ações restantes e retorna o status especificado. Por exemplo, você pode usar a ação **terminar** quando seu aplicativo lógico precisar sair completamente de um estado de erro. Essa ação não afeta as ações já concluídas e não pode aparecer dentro de loops **foreach** e **until** , incluindo loops sequenciais. 

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

| Value | Type | Descrição | 
|-------|------|-------------| 
| <*status*> | Cadeia | O status a ser retornado para a execução: "Falha", "cancelado" ou "êxito" |
|||| 

*Adicional*

As propriedades do objeto "runStatus" aplicam-se somente quando a propriedade "runStatus" está definida como status "falha".

| Valor | Type | Descrição | 
|-------|------|-------------| 
| <*error-code-or-name*> | Cadeia | O código ou o nome do erro |
| <*error-message*> | Cadeia | A mensagem ou o texto que descreve o erro e as ações que o usuário do aplicativo pode executar | 
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

| Value | Type | Descrição | 
|-------|------|-------------| 
| <*number-of-units*> | Número inteiro | Para a ação de **atraso** , o número de unidades a esperar | 
| <*interval*> | Cadeia | Para a ação de **atraso** , o intervalo a aguardar: "Segundo", "minuto", "hora", "dia", "semana", "mês" | 
| <*date-time-stamp*> | Cadeia | Para a ação **atrasar até** , a data e hora para retomar a execução. Esse valor deve usar o [formato de data e hora UTC](https://en.wikipedia.org/wiki/Coordinated_Universal_Time). | 
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

Essa ação chama outro aplicativo lógico criado anteriormente, o que significa que você pode incluir e reutilizar outros fluxos de trabalho de aplicativo lógico. Você também pode usar as saídas do aplicativo lógico filho  ou aninhado em ações que seguem o aplicativo lógico aninhado, desde que o aplicativo lógico filho retorne uma resposta.

O mecanismo de aplicativos lógicos verifica o acesso ao gatilho que você deseja chamar, portanto, verifique se você pode acessar esse gatilho. Além disso, o aplicativo lógico aninhado deve atender a estes critérios:

* Um gatilho torna o aplicativo lógico aninhado que possa ser chamado, como uma [solicitação](#request-trigger) ou um gatilho [http](#http-trigger)

* A mesma assinatura do Azure que seu aplicativo lógico pai

* Para usar as saídas do aplicativo lógico aninhado em seu aplicativo lógico pai, o aplicativo lógico aninhado deve ter uma ação de [resposta](#response-action) 

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

| Value | Type | Descrição | 
|-------|------|-------------| 
| <*nested-logic-app-name*> | Cadeia | O nome do aplicativo lógico que você deseja chamar | 
| <*trigger-name*> | Cadeia | O nome do gatilho no aplicativo lógico aninhado que você deseja chamar | 
| <*Azure-subscription-ID*> | Cadeia | A ID de assinatura do Azure para o aplicativo lógico aninhado |
| <*Azure-resource-group*> | Cadeia | O nome do grupo de recursos do Azure para o aplicativo lógico aninhado |
| <*nested-logic-app-name*> | Cadeia | O nome do aplicativo lógico que você deseja chamar |
||||

*Adicional*

| Value | Type | Descrição | 
|-------|------|-------------|  
| <*header-content*> | Objeto JSON | Todos os cabeçalhos a serem enviados com a chamada | 
| <*body-content*> | Objeto JSON | Qualquer conteúdo de mensagem para enviar com a chamada | 
||||

*Saídas*

As saídas dessa ação variam de acordo com a ação de resposta do aplicativo lógico aninhado. Se o aplicativo lógico aninhado não incluir uma ação de resposta, as saídas estarão vazias.

*Exemplo*

Depois que a ação "Start_search" for concluída com êxito, essa definição de ação de fluxo de trabalho chamará outro aplicativo lógico chamado "Get_product_information", que passa nas entradas especificadas: 

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

Essa ação de loop itera por meio de uma matriz e executa ações em cada item da matriz. Por padrão, o loop "for each" é executado em paralelo até um número máximo de loops. Para esse máximo, consulte [limites e configuração](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Saiba [como criar loops "para cada"](../logic-apps/logic-apps-control-flow-loops.md#foreach-loop).

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

| Value | Type | Descrição | 
|-------|------|-------------| 
| <*ação-1... p*> | Cadeia | Os nomes das ações que são executadas em cada item da matriz | 
| <*ação-definição-1... p*> | Objeto JSON | As definições das ações que são executadas | 
| <*for-each-expression*> | Cadeia | A expressão que faz referência a cada item na matriz especificada | 
|||| 

*Adicional*

| Value | Type | Descrição | 
|-------|------|-------------| 
| <*count*> | Número inteiro | Por padrão, as iterações de loop "for each" são executadas ao mesmo tempo ou em paralelo até o [limite padrão](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Para alterar esse limite definindo um novo valor de >*contagem*de <, consulte [alterar a simultaneidade de loop "for each"](#change-for-each-concurrency). | 
| <*operation-option*> | Cadeia | Para executar um loop "for each" sequencialmente, em vez de em paralelo, defina <*opção Operation-Option*> `Sequential` ou `1`<*Count*>, mas não ambos. Para obter mais informações, consulte [executar loops "for each" sequencialmente](#sequential-for-each). | 
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

Para especificar apenas uma matriz que é passada como saída do gatilho, essa expressão Obtém o <*nome da matriz*> matriz do corpo do gatilho. Para evitar uma falha se a matriz não existir, a expressão usará `?` o operador:

`@triggerBody()?['<array-name>']` 

<a name="if-action"></a>

### <a name="if-action"></a>Ação If

Essa ação, que é uma *instrução condicional*, avalia uma expressão que representa uma condição e executa uma ramificação diferente com base em se a condição é verdadeira ou falsa. Se a condição for verdadeira, a condição será marcada com o status "êxito". Saiba [como criar instruções condicionais](../logic-apps/logic-apps-control-flow-conditional-statement.md).

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

| Valor | Type | Descrição | 
|-------|------|-------------| 
| <*problema*> | Objeto JSON | A condição, que pode ser uma expressão, para avaliar | 
| <*ação-1*> | Objeto JSON | A ação a ser executada quando <*condição*> avaliada como true | 
| <*action-definition*> | Objeto JSON | A definição da ação | 
| <*ação-2*> | Objeto JSON | A ação a ser executada quando <*condição*> avaliada como falsa | 
|||| 

As ações nos `actions` objetos ou `else` obtêm estes status:

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
| "Expression": "@parameters(' <*hasSpecialAction*> ')" | Somente para expressões booleanas, a condição passa para qualquer valor que seja avaliado como true. <p>Para converter outros tipos em booliano, use estas funções `empty()` : `equals()`ou. | 
| "expression": "@greater(actions('<*action*>').output.value, parameters('<*threshold*>'))" | Para funções de comparação, a ação é executada somente quando a saída da*ação*< > é maior que o*limite*de < > valor. | 
| "expressão": "@or(maior (ações (' <*ação*> '). saída. valor, parâmetros (' <*limite*> ')), menos (ações (' <*mesma ação*> '). Output. Value, 100))" | Para funções lógicas e criação de expressões boolianas aninhadas, a ação é executada quando a saída da*ação*< > é maior que o*limite*de < > valor ou abaixo de 100. | 
| "expression": "@equals(length(actions('<*action*>').outputs.errors), 0))" | Você pode usar funções de matriz para verificar se a matriz tem algum item. A ação é executada quando `errors` a matriz está vazia. | 
||| 

<a name="scope-action"></a>

### <a name="scope-action"></a>Ação de escopo

Essa ação agrupa logicamente ações em escopos, que obtêm seu próprio status depois que as ações nesse escopo terminam de ser executadas. Você pode usar o status do escopo para determinar se outras ações são executadas. Saiba [como criar](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)escopos.

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

| Value | Type | Descrição | 
|-------|------|-------------|  
| <*inner-action-1...n*> | Objeto JSON | Uma ou mais ações que são executadas dentro do escopo |
| <*ação-entradas*> | Objeto JSON | As entradas para cada ação |
|||| 

<a name="switch-action"></a>

### <a name="switch-action"></a>Ação do comutador

Essa ação, também conhecida como *instrução switch*, organiza outras ações em *casos*e atribui um valor a cada caso, exceto para o caso padrão, se houver. Quando o fluxo de trabalho é executado, a ação **switch** compara o valor de uma expressão, objeto ou token com os valores especificados para cada caso. Se a  ação de comutador encontrar um caso correspondente, o fluxo de trabalho executará apenas as ações para esse caso. Cada vez que  a ação de comutador é executada, apenas um caso correspondente existe ou nenhuma correspondência existe. Se nenhuma correspondência existir, a  ação de comutador executará as ações padrão. Saiba [como criar instruções switch](../logic-apps/logic-apps-control-flow-switch-statement.md).

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

| Value | Type | Descrição | 
|-------|------|-------------| 
| <*expression-object-or-token*> | Varia | A expressão, o objeto JSON ou o token a ser avaliado | 
| <*nome da ação*> | Cadeia | O nome da ação a ser executada para o caso de correspondência | 
| <*action-definition*> | Objeto JSON | A definição da ação a ser executada para o caso de correspondência | 
| <*correspondência-valor*> | Varia | O valor a ser comparado com o resultado avaliado | 
|||| 

*Adicional*

| Valor | Type | Descrição | 
|-------|------|-------------| 
| <*default-action-name*> | Cadeia | O nome da ação padrão a ser executada quando não houver nenhum caso correspondente | 
| <*default-action-definition*> | Objeto JSON | A definição da ação a ser executada quando não houver nenhum caso correspondente | 
|||| 

*Exemplo*

Esta definição de ação avalia se a pessoa que está respondendo ao email de solicitação de aprovação selecionou a opção "aprovar" ou a opção "rejeitar". Com base nessa opção, a  ação de comutador executa as ações para o caso de correspondência, que é enviar outro email para o respondente, mas com palavras diferentes em cada caso. 

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

Essa ação de loop contém ações que são executadas até que a condição especificada seja verdadeira. O loop verifica a condição como a última etapa após a execução de todas as outras ações. Você pode incluir mais de uma ação no `"actions"` objeto e a ação deve definir pelo menos um limite. Saiba [como criar loops "until"](../logic-apps/logic-apps-control-flow-loops.md#until-loop). 

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

| Value | Type | Descrição | 
|-------|------|-------------| 
| <*nome da ação*> | Cadeia | O nome da ação que você deseja executar dentro do loop | 
| <*action-type*> | Cadeia | O tipo de ação que você deseja executar | 
| <*ação-entradas*> | Vários | As entradas para a ação a ser executada | 
| <*problema*> | Cadeia | A condição ou expressão a ser avaliada depois que todas as ações no loop terminarem de ser executadas | 
| <*loop-count*> | Número inteiro | O limite do número máximo de loops que a ação pode executar. O valor `count` padrão é 60. | 
| <*loop-timeout*> | Cadeia | O limite do tempo mais longo que o loop pode ser executado. O valor `timeout` padrão é `PT1H`, que é o [formato ISO 8601](https://en.wikipedia.org/wiki/ISO_8601)necessário. |
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
        "Http": {
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "http://myurl"
            },
            "runAfter": {}
        }
    },
    "expression": "@equals(outputs('Http')['statusCode', 200])",
    "limit": {
        "count": 60,
        "timeout": "PT1H"
    },
    "runAfter": {}
}
```

<a name="subscribe-unsubscribe"></a>

## <a name="webhooks-and-subscriptions"></a>WebHooks e assinaturas

Gatilhos e ações baseados em webhook não verificam regularmente os pontos de extremidade, mas aguardam eventos ou dados específicos nesses pontos de extremidade. Esses gatilhos e  ações assinam os pontos de extremidade fornecendo uma *URL de retorno de chamada* em que o ponto de extremidade pode enviar respostas.

A `subscribe` chamada ocorre quando o fluxo de trabalho é alterado de qualquer forma, por exemplo, quando as credenciais são renovadas ou quando os parâmetros de entrada são alterados para um gatilho ou uma ação. Essa chamada usa os mesmos parâmetros que as ações HTTP padrão. 

A `unsubscribe` chamada ocorre automaticamente quando uma operação torna o gatilho ou a ação inválida, por exemplo:

* Excluindo ou desabilitando o gatilho. 
* Excluindo ou desabilitando o fluxo de trabalho. 
* Excluindo ou desabilitando a assinatura. 

Para dar suporte a essas chamadas `@listCallbackUrl()` , a expressão retorna uma "URL de retorno de chamada" exclusiva para o gatilho ou a ação. Essa URL representa um identificador exclusivo para os pontos de extremidade que usam a API REST do serviço. Os parâmetros para essa função são os mesmos que o gatilho ou ação de webhook.

<a name="asynchronous-limits"></a>

## <a name="change-asynchronous-duration"></a>Alterar duração assíncrona

Para gatilhos e ações, você pode limitar a duração do padrão assíncrono a um intervalo de tempo específico adicionando a `limit.timeout` propriedade. Dessa forma, se a ação não tiver terminado quando o intervalo se sobrepuser, o status da ação será `Cancelled` marcado como `ActionTimedOut` com o código. A `timeout` propriedade usa o [formato ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations). 

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

Você pode alterar o comportamento de tempo de execução padrão para gatilhos `runtimeConfiguration` e ações com essas propriedades na definição do gatilho ou da ação.

| Propriedade | Tipo | Descrição | Gatilho ou ação | 
|----------|------|-------------|-------------------| 
| `runtimeConfiguration.concurrency.runs` | Número inteiro | Altere o [*limite padrão*](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits) no número de instâncias de fluxo de trabalho que podem ser executadas ao mesmo tempo ou em paralelo. Esse valor pode ajudar a limitar o número de solicitações que os sistemas de back-end recebem. <p>Definir a `runs` propriedade para `1` funciona da mesma maneira `SingleInstance`que definir a `operationOptions` Propriedade como. Você pode definir qualquer propriedade, mas não ambos. <p>Para alterar o limite padrão, consulte [alterar](#change-trigger-concurrency) a simultaneidade de gatilho ou [instâncias de gatilho sequencialmente](#sequential-trigger). | Todos os acionadores | 
| `runtimeConfiguration.concurrency.maximumWaitingRuns` | Número inteiro | Altere o [*limite padrão*](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits) no número de instâncias de fluxo de trabalho que podem esperar para serem executadas quando o fluxo de trabalho já estiver executando o máximo de instâncias simultâneas. Você pode alterar o limite de simultaneidade `concurrency.runs` na propriedade. <p>Para alterar o limite padrão, consulte [alterar o limite](#change-waiting-runs)de execuções em espera. | Todos os acionadores | 
| `runtimeConfiguration.concurrency.repetitions` | Número inteiro | Altere o [*limite padrão*](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits) no número de iterações de loop "for each" que podem ser executadas ao mesmo tempo ou em paralelo. <p>Definir a `repetitions` propriedade para `1` funciona da mesma maneira `SingleInstance`que definir a `operationOptions` Propriedade como. Você pode definir qualquer propriedade, mas não ambos. <p>Para alterar o limite padrão, consulte [alterar "para cada" simultaneidade](#change-for-each-concurrency) ou [executar "loop for each" em sequência](#sequential-for-each). | Ação: <p>[Foreach](#foreach-action) | 
| `runtimeConfiguration.paginationPolicy.minimumItemCount` | Número inteiro | Para ações específicas que dão suporte e têm a paginação ativada, esse valor especifica o número *mínimo* de resultados a serem recuperados. <p>Para ativar a paginação, consulte [obter dados em massa, itens ou resultados usando](../logic-apps/logic-apps-exceed-default-page-size-with-pagination.md) paginação | Ação: Variado |
| `runtimeConfiguration.secureData.properties` | Array | Em muitos gatilhos e ações, essas configurações ocultam entradas, saídas ou ambos do histórico de execução do aplicativo lógico. <p>Para proteger esses dados, consulte [ocultar entradas e saídas do histórico de execuções](../logic-apps/logic-apps-securing-a-logic-app.md#secure-data-code-view). | Maioria dos gatilhos e ações |
| `runtimeConfiguration.staticResult` | Objeto JSON | Para ações que dão suporte e têm a configuração de [resultado estático](../logic-apps/test-logic-apps-mock-data-static-results.md) ativada `staticResult` , o objeto tem estes atributos: <p>- `name`, que faz referência ao nome de definição de resultado estático da ação atual, que `staticResults` aparece dentro do atributo no atributo do `definition` fluxo de trabalho do aplicativo lógico. Para obter mais informações, consulte [resultados estáticos-referência de esquema para linguagem de definição de fluxo de trabalho](../logic-apps/logic-apps-workflow-definition-language.md#static-results). <p> - `staticResultOptions`, que especifica se os resultados estáticos são `Enabled` ou não para a ação atual. <p>Para ativar os resultados estáticos, consulte [testar aplicativos lógicos com dados fictícios Configurando resultados estáticos](../logic-apps/test-logic-apps-mock-data-static-results.md) | Ação: Variado |
||||| 

<a name="operation-options"></a>

## <a name="operation-options"></a>Opções de operação

Você pode alterar o comportamento padrão para gatilhos e ações com `operationOptions` a propriedade na definição do gatilho ou da ação.

| Opção de operação | Type | Descrição | Gatilho ou ação | 
|------------------|------|-------------|-------------------| 
| `DisableAsyncPattern` | Cadeia | Execute ações baseadas em HTTP de forma síncrona, em vez de assíncrona. <p><p>Para definir essa opção, consulte [executar ações](#asynchronous-patterns)de forma síncrona. | Ações <p>[ApiConnection](#apiconnection-action), <br>[HTTP](#http-action), <br>[Resposta](#response-action) | 
| `OptimizedForHighThroughput` | Cadeia | Altere o [limite padrão](../logic-apps/logic-apps-limits-and-config.md#throughput-limits) no número de execuções de ação por 5 minutos para o [limite máximo](../logic-apps/logic-apps-limits-and-config.md#throughput-limits). <p><p>Para definir essa opção, consulte [executar no modo de alta taxa de transferência](#run-high-throughput-mode). | Todas as ações | 
| `Sequential` | Cadeia | Execute "para cada" iterações de loop, uma de cada vez, em vez de todas ao mesmo tempo em paralelo. <p>Essa opção funciona da mesma maneira que definir a `runtimeConfiguration.concurrency.repetitions` `1`Propriedade como. Você pode definir qualquer propriedade, mas não ambos. <p><p>Para definir essa opção, consulte [executar loops "for each" sequencialmente](#sequential-for-each).| Ação: <p>[Foreach](#foreach-action) | 
| `SingleInstance` | Cadeia | Execute o gatilho para cada instância de aplicativo lógico sequencialmente e aguarde até que a execução ativa anteriormente seja acionada antes de disparar a próxima instância de aplicativo lógico. <p><p>Essa opção funciona da mesma maneira que definir a `runtimeConfiguration.concurrency.runs` `1`Propriedade como. Você pode definir qualquer propriedade, mas não ambos. <p>Para definir essa opção, confira [instâncias de gatilho sequencialmente](#sequential-trigger). | Todos os acionadores | 
||||

<a name="change-trigger-concurrency"></a>

### <a name="change-trigger-concurrency"></a>Alterar simultaneidade do gatilho

Por padrão, as instâncias do aplicativo lógico são executadas ao mesmo tempo, simultaneamente ou em paralelo até o [limite padrão](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Portanto, cada instância de gatilho é acionada antes da conclusão da execução da instância do fluxo de trabalho anterior. Esse limite ajuda a controlar o número de solicitações que os sistemas de back-end recebem. 

Para alterar o limite padrão, você pode usar o editor de exibição de código ou o designer de aplicativos lógicos porque alterar a configuração de simultaneidade por `runtimeConfiguration.concurrency.runs` meio do designer adiciona ou atualiza a propriedade na definição de gatilho subjacente e vice-versa. Essa propriedade controla o número máximo de instâncias de fluxo de trabalho que podem ser executadas em paralelo. 

> [!NOTE] 
> Se você definir o gatilho para ser executado sequencialmente usando o designer ou o editor de modo de exibição de código, não defina `operationOptions` a propriedade `SingleInstance` do gatilho como no editor de exibição de código. Caso contrário, você receberá um erro de validação. Para obter mais informações, consulte [disparar instâncias sequencialmente](#sequential-trigger).

#### <a name="edit-in-code-view"></a>Editar na exibição de código 

Na definição de gatilho subjacente, adicione ou atualize a `runtimeConfiguration.concurrency.runs` propriedade para um valor entre `1` e `50` inclusive.

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

#### <a name="edit-in-logic-apps-designer"></a>Editar no designer de aplicativos lógicos

1. No canto superior direito do gatilho, escolha o botão de reticências (...) e, em seguida, escolha **configurações**.

2. Em **controle**de simultaneidade, defina **limite** como **ativado**. 

3. Arraste o controle deslizante **grau de paralelismo** para o valor desejado. Para executar o aplicativo lógico em sequência, arraste o valor do controle deslizante para **1**.

<a name="change-for-each-concurrency"></a>

### <a name="change-for-each-concurrency"></a>Alterar "para cada" simultaneidade

Por padrão, as iterações de loop "for each" são executadas ao mesmo tempo, ou em paralelo, até o [limite padrão](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Para alterar o limite padrão, você pode usar o editor de modo de exibição de código ou o designer de aplicativos lógicos, pois alterar a configuração de `runtimeConfiguration.concurrency.repetitions` simultaneidade por meio do designer adiciona ou atualiza a propriedade na definição de ação "para cada" subjacente e vice-versa. Essa propriedade controla o número máximo de iterações que podem ser executadas em paralelo.

> [!NOTE] 
> Se você definir a ação "para cada" para ser executada sequencialmente usando o designer ou o editor de modo de exibição de código, não defina `operationOptions` a propriedade `Sequential` da ação como no editor de modo de exibição de código. Caso contrário, você receberá um erro de validação. Para obter mais informações, consulte [executar loops "for each" sequencialmente](#sequential-for-each).

#### <a name="edit-in-code-view"></a>Editar na exibição de código 

Na definição subjacente "para cada", adicione ou atualize a `runtimeConfiguration.concurrency.repetitions` propriedade para um valor entre `1` e `50` inclusive. 

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

#### <a name="edit-in-logic-apps-designer"></a>Editar no designer de aplicativos lógicos

1. Na ação **para cada** , no canto superior direito, escolha o botão de reticências (...) e, em seguida, escolha **configurações**.

2. Em **controle**de simultaneidade, defina **controle** de simultaneidade como **ativado**. 

3. Arraste o controle deslizante **grau de paralelismo** para o valor desejado. Para executar o aplicativo lógico em sequência, arraste o valor do controle deslizante para **1**.

<a name="change-waiting-runs"></a>

### <a name="change-waiting-runs-limit"></a>Alterar limite de execuções de espera

Por padrão, todas as instâncias de fluxo de trabalho do aplicativo lógico são executadas ao mesmo tempo, simultaneamente ou em paralelo até o [limite padrão](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Cada instância de gatilho é acionada antes da conclusão da execução da instância de fluxo de trabalho ativa anteriormente. Embora você possa [alterar esse limite padrão](#change-trigger-concurrency), quando o número de instâncias de fluxo de trabalho atinge o novo limite de simultaneidade, todas as novas instâncias devem aguardar para serem executadas. 

O número de execuções que podem esperar também tem um [limite padrão](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits), que pode ser alterado. No entanto, depois que o aplicativo lógico atingir o limite de execuções em espera, o mecanismo de aplicativos lógicos não aceitará mais novas execuções. Os gatilhos de solicitação e webhook retornam erros 429 e os gatilhos recorrentes começam a ignorar as tentativas de sondagem.

Para alterar o limite padrão em espera de execuções, na definição de gatilho subjacente, `runtimeConfiguration.concurency.maximumWaitingRuns` adicione a propriedade com um `0` valor `100`entre e. 

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

<a name="sequential-trigger"></a>

### <a name="trigger-instances-sequentially"></a>Instâncias de gatilho sequencialmente

Para executar cada instância de fluxo de trabalho do aplicativo lógico somente após a conclusão da execução da instância anterior, defina o gatilho para ser executado em sequência. Você pode usar o editor de exibição de código ou o designer de aplicativos lógicos porque alterar a configuração de simultaneidade por `runtimeConfiguration.concurrency.runs` meio do designer também adiciona ou atualiza a propriedade na definição de gatilho subjacente e vice-versa. 

> [!NOTE] 
> Quando você define um gatilho para ser executado sequencialmente usando o designer ou o editor de modo de exibição de código, não defina `operationOptions` a propriedade `Sequential` do gatilho como no editor de modo de exibição de código. Caso contrário, você receberá um erro de validação. 

#### <a name="edit-in-code-view"></a>Editar na exibição de código

Na definição do gatilho, defina uma dessas propriedades, mas não ambas. 

Defina a `runtimeConfiguration.concurrency.runs` Propriedade como `1`:

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

*or*

Defina a `operationOptions` Propriedade como `SingleInstance`:

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

#### <a name="edit-in-logic-apps-designer"></a>Editar no designer de aplicativos lógicos

1. No canto superior direito do gatilho, escolha o botão de reticências (...) e, em seguida, escolha **configurações**.

2. Em **controle**de simultaneidade, defina **limite** como **ativado**. 

3. Arraste o controle deslizante **grau de paralelismo** para o `1`número. 

<a name="sequential-for-each"></a>

### <a name="run-for-each-loops-sequentially"></a>Executar loops "for each" sequencialmente

Para executar uma iteração de loop "for each" somente após a conclusão da execução da iteração anterior, defina a ação "para cada" para ser executada em sequência. Você pode usar o editor de exibição de código ou o designer de aplicativos lógicos porque alterar a simultaneidade da ação por meio `runtimeConfiguration.concurrency.repetitions` do designer também adiciona ou atualiza a propriedade na definição de ação subjacente e vice-versa. 

> [!NOTE] 
> Quando você define uma ação "para cada" para execução sequencialmente usando o designer ou o editor de modo de exibição de código, não defina `operationOptions` a propriedade `Sequential` da ação como no editor de modo de exibição de código. Caso contrário, você receberá um erro de validação. 

#### <a name="edit-in-code-view"></a>Editar na exibição de código

Na definição de ação, defina qualquer uma dessas propriedades, mas não ambas. 

Defina a `runtimeConfiguration.concurrency.repetitions` Propriedade como `1`:

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

*or*

Defina a `operationOptions` Propriedade como `Sequential`:

```json
"For_each" {
   "type": "Foreach",
   "actions": { "<actions-to-run>" },
   "foreach": "<for-each-expression>",
   "runAfter": {},
   "operationOptions": "Sequential"
}
```

#### <a name="edit-in-logic-apps-designer"></a>Editar no designer de aplicativos lógicos

1. No canto superior direito de **cada** ação, escolha o botão de reticências (...) e, em seguida, escolha **configurações**.

2. Em **controle**de simultaneidade, defina **controle** de simultaneidade como **ativado**. 

3. Arraste o controle deslizante **grau de paralelismo** para o `1`número. 

<a name="asynchronous-patterns"></a>

### <a name="run-actions-synchronously"></a>Executar ações de forma síncrona

Por padrão, todas as ações baseadas em HTTP seguem o padrão de operação assíncrona padrão. Esse padrão especifica que quando uma ação baseada em HTTP envia uma solicitação para o ponto de extremidade especificado, o servidor remoto envia de volta uma resposta "202 aceito". Essa resposta significa que o servidor aceitou a solicitação de processamento. O mecanismo de aplicativos lógicos mantém a verificação da URL especificada pelo cabeçalho de local da resposta até que o processamento seja interrompido, que é qualquer resposta diferente de 202.

No entanto, as solicitações têm um limite de tempo limite, portanto, para ações de longa execução, você pode desabilitar o comportamento `operationOptions` assíncrono adicionando `DisableAsyncPattern` e configurando a propriedade para sob as entradas da ação.
  
```json
"<some-long-running-action>": {
   "type": "Http",
   "inputs": { "<action-inputs>" },
   "operationOptions": "DisableAsyncPattern",
   "runAfter": {}
}
```

<a name="run-high-throughput-mode"></a>

### <a name="run-in-high-throughput-mode"></a>Executar no modo de alta taxa de transferência

Para uma definição de aplicativo lógico único, o número de ações executadas a cada 5 minutos tem um [limite padrão](../logic-apps/logic-apps-limits-and-config.md#throughput-limits). Para aumentar esse limite para o [máximo](../logic-apps/logic-apps-limits-and-config.md#throughput-limits) possível, defina a `operationOptions` Propriedade como `OptimizedForHighThroughput`. Essa configuração coloca seu aplicativo lógico no modo de "alta taxa de transferência". 

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

<a name="connector-authentication"></a>

## <a name="authenticate-http-triggers-and-actions"></a>Autenticar gatilhos e ações HTTP

Os pontos de extremidade HTTP dão suporte a diferentes tipos de autenticação. Você pode configurar a autenticação para esses gatilhos e ações HTTP:

* [HTTP](../connectors/connectors-native-http.md)
* [HTTP + Swagger](../connectors/connectors-native-http-swagger.md)
* [Webhook de HTTP](../connectors/connectors-native-webhook.md)

Aqui estão os tipos de autenticação que você pode configurar:

* [Autenticação básica](#basic-authentication)
* [Autenticação de certificado de cliente](#client-certificate-authentication)
* [Autenticação OAuth do Azure Active Directory (Azure AD)](#azure-active-directory-oauth-authentication)

> [!IMPORTANT]
> Certifique-se de proteger quaisquer informações confidenciais manipuladas pela definição de fluxo de trabalho do aplicativo lógico. Use parâmetros protegidos e codifique os dados conforme necessário. Para obter mais informações sobre como usar e proteger parâmetros, consulte [proteger seu aplicativo lógico](../logic-apps/logic-apps-securing-a-logic-app.md#secure-action-parameters).

<a name="basic-authentication"></a>

### <a name="basic-authentication"></a>Autenticação básica

Para a [autenticação básica](../active-directory-b2c/active-directory-b2c-custom-rest-api-netfw-secure-basic.md) usando Azure Active Directory, sua definição de gatilho ou ação pode incluir `authentication` um objeto JSON, que tem as propriedades especificadas pela tabela a seguir. Para acessar valores de parâmetro em tempo de execução, você `@parameters('parameterName')` pode usar a expressão, que é fornecida pela [linguagem de definição de fluxo de trabalho](https://aka.ms/logicappsdocs). 

| Propriedade | Requerido | Value | Descrição | 
|----------|----------|-------|-------------| 
| **type** | Sim | Basic | O tipo de autenticação a ser usado, que é "básico" aqui | 
| **username** | Sim | "@parameters(' userNameParam ')" | O nome de usuário para autenticar o acesso ao ponto de extremidade de serviço de destino |
| **password** | Sim | "@parameters(' passwordParam ')" | A senha para autenticar o acesso ao ponto de extremidade de serviço de destino |
||||| 

Nesta definição de ação http de exemplo, `authentication` a seção `Basic` especifica autenticação. Para obter mais informações sobre como usar e proteger parâmetros, consulte [proteger seu aplicativo lógico](../logic-apps/logic-apps-securing-a-logic-app.md#secure-action-parameters).

```json
"HTTP": {
   "type": "Http",
   "inputs": {
      "method": "GET",
      "uri": "https://www.microsoft.com",
      "authentication": {
         "type": "Basic",
         "username": "@parameters('userNameParam')",
         "password": "@parameters('passwordParam')"
      }
  },
  "runAfter": {}
}
```

> [!IMPORTANT]
> Certifique-se de proteger quaisquer informações confidenciais manipuladas pela definição de fluxo de trabalho do aplicativo lógico. Use parâmetros protegidos e codifique os dados conforme necessário. Para obter mais informações sobre como proteger os parâmetros, consulte [proteger seu aplicativo lógico](../logic-apps/logic-apps-securing-a-logic-app.md#secure-action-parameters).

<a name="client-certificate-authentication"></a>

### <a name="client-certificate-authentication"></a>Autenticação de certificado de cliente

Para a [autenticação baseada em certificado](../active-directory/authentication/active-directory-certificate-based-authentication-get-started.md) usando Azure Active Directory, sua definição de gatilho ou ação pode `authentication` incluir um objeto JSON, que tem as propriedades especificadas pela tabela a seguir. Para acessar valores de parâmetro em tempo de execução, você `@parameters('parameterName')` pode usar a expressão, que é fornecida pela [linguagem de definição de fluxo de trabalho](https://aka.ms/logicappsdocs). Para obter limites sobre o número de certificados de cliente que você pode usar, consulte [limites e configuração para aplicativos lógicos do Azure](../logic-apps/logic-apps-limits-and-config.md).

| Propriedade | Requerido | Value | Descrição |
|----------|----------|-------|-------------|
| **type** | Sim | "ClientCertificate" | O tipo de autenticação a ser usado para certificados de cliente protocolo SSL (SSL). Embora haja suporte para certificados autoassinados, não há suporte para certificados autoassinados para SSL. |
| **pfx** | Sim | "@parameters('pfxParam') | O conteúdo codificado em Base64 de um arquivo de troca de informações pessoais (PFX) |
| **password** | Sim | "@parameters(' passwordParam ')" | A senha para acessar o arquivo PFX |
||||| 

Nesta definição de ação http de exemplo, `authentication` a seção `ClientCertificate` especifica autenticação. Para obter mais informações sobre como usar e proteger parâmetros, consulte [proteger seu aplicativo lógico](../logic-apps/logic-apps-securing-a-logic-app.md#secure-action-parameters).

```json
"HTTP": {
   "type": "Http",
   "inputs": {
      "method": "GET",
      "uri": "https://www.microsoft.com",
      "authentication": {
         "type": "ClientCertificate",
         "pfx": "@parameters('pfxParam')",
         "password": "@parameters('passwordParam')"
      }
   },
   "runAfter": {}
}
```

> [!IMPORTANT]
> Certifique-se de proteger quaisquer informações confidenciais manipuladas pela definição de fluxo de trabalho do aplicativo lógico. Use parâmetros protegidos e codifique os dados conforme necessário. Para obter mais informações sobre como proteger os parâmetros, consulte [proteger seu aplicativo lógico](../logic-apps/logic-apps-securing-a-logic-app.md#secure-action-parameters).

<a name="azure-active-directory-oauth-authentication"></a>

### <a name="azure-active-directory-ad-oauth-authentication"></a>Autenticação OAuth do Azure Active Directory (AD)

Para a [Autenticação OAuth do Azure ad](../active-directory/develop/authentication-scenarios.md), sua definição de gatilho ou ação `authentication` pode incluir um objeto JSON, que tem as propriedades especificadas pela tabela a seguir. Para acessar valores de parâmetro em tempo de execução, você `@parameters('parameterName')` pode usar a expressão, que é fornecida pela [linguagem de definição de fluxo de trabalho](https://aka.ms/logicappsdocs).

| Propriedade | Requerido | Value | Descrição |
|----------|----------|-------|-------------|
| **type** | Sim | `ActiveDirectoryOAuth` | O tipo de autenticação a ser usado, que é "ActiveDirectoryOAuth" para o OAuth do Azure AD |
| **autoridades** | Não | <*URL-for-authority-token-issuer*> | A URL para a autoridade que fornece o token de autenticação |
| **tenant** | Sim | <*tenant-ID*> | A ID de locatário para o locatário do Azure AD |
| **platéia** | Sim | <*recurso a ser autorizado*> | O recurso que você deseja usar para autorização, por exemplo,`https://management.core.windows.net/` |
| **clientId** | Sim | <*client-ID*> | A ID do cliente para o aplicativo que solicita autorização |
| **credentialType** | Sim | "Certificado" ou "segredo" | O tipo de credencial que o cliente usa para solicitar autorização. Essa propriedade e o valor não aparecem na definição subjacente, mas determina os parâmetros necessários para o tipo de credencial. |
| **pfx** | Sim, somente para o tipo de credencial "certificado" | "@parameters('pfxParam') | O conteúdo codificado em Base64 de um arquivo de troca de informações pessoais (PFX) |
| **password** | Sim, somente para o tipo de credencial "certificado" | "@parameters(' passwordParam ')" | A senha para acessar o arquivo PFX |
| **secret** | Sim, somente para o tipo de credencial "segredo" | "@parameters(' secretParam ')" | O segredo do cliente para solicitar autorização |
|||||

Nesta definição de ação http de exemplo, `authentication` a seção `ActiveDirectoryOAuth` especifica autenticação e o tipo de credencial "segredo". Para obter mais informações sobre como usar e proteger parâmetros, consulte [proteger seu aplicativo lógico](../logic-apps/logic-apps-securing-a-logic-app.md#secure-action-parameters).

```json
"HTTP": {
   "type": "Http",
   "inputs": {
      "method": "GET",
      "uri": "https://www.microsoft.com",
      "authentication": {
         "type": "ActiveDirectoryOAuth",
         "tenant": "72f988bf-86f1-41af-91ab-2d7cd011db47",
         "audience": "https://management.core.windows.net/",
         "clientId": "34750e0b-72d1-4e4f-bbbe-664f6d04d411",
         "secret": "@parameters('secretParam')"
     }
   },
   "runAfter": {}
}
```

> [!IMPORTANT]
> Certifique-se de proteger quaisquer informações confidenciais manipuladas pela definição de fluxo de trabalho do aplicativo lógico. Use parâmetros protegidos e codifique os dados conforme necessário. Para obter mais informações sobre como proteger os parâmetros, consulte [proteger seu aplicativo lógico](../logic-apps/logic-apps-securing-a-logic-app.md#secure-action-parameters).

## <a name="next-steps"></a>Passos Seguintes

* Saiba mais sobre a [linguagem de definição de fluxo de trabalho](../logic-apps/logic-apps-workflow-definition-language.md)
