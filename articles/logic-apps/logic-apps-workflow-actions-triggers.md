---
title: Acionador e ação de tipos de referência - Azure Logic Apps | Documentos da Microsoft
description: Saiba mais sobre os tipos de Acionador e ação no Azure Logic Apps, conforme descrito pelo esquema de linguagem de definição de fluxo de trabalho
services: logic-apps
ms.service: logic-apps
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.suite: integration
ms.topic: reference
ms.date: 06/22/2018
ms.openlocfilehash: 066c8bc3edfc2bf36b4d96f787d6db6f16daec9b
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57856829"
---
# <a name="trigger-and-action-types-reference-for-workflow-definition-language-in-azure-logic-apps"></a>Referência de tipos de Acionador e ação para a linguagem de definição de fluxo de trabalho no Azure Logic Apps

Na [do Azure Logic Apps](../logic-apps/logic-apps-overview.md), todos os fluxos de trabalho de aplicações lógicas começam com acionadores seguidos de ações. Este artigo descreve os tipos de Acionador e ação, que pode usar ao criar aplicações lógicas para automatizar tarefas, processos e fluxos de trabalho. É possível criar lógica fluxos de trabalho de aplicação com o estruturador de aplicações lógicas, visualmente ou criando as definições de fluxo de trabalho subjacente com o [linguagem de definição de fluxo de trabalho](../logic-apps/logic-apps-workflow-definition-language.md). Pode criar aplicações lógicas no portal do Azure ou do Visual Studio. A definição subjacente para o fluxo de trabalho completo, incluindo o acionador e ações, utiliza Javascript Object Notation (JSON).

<a name="triggers-overview"></a>

## <a name="triggers-overview"></a>Descrição geral de acionadores

Todas as aplicações lógicas começam com um acionador, que define as chamadas de instanciar e iniciar um fluxo de trabalho de aplicação lógica. Estas são as categorias de Acionador gerais:

* R *consulta* acionador, que verifica o ponto final de um serviço em intervalos regulares

* R *push* acionador, que cria uma subscrição para um ponto final e fornece um *URL de retorno de chamada* para que o ponto final pode notificar o acionador quando ocorre o evento especificado ou os dados estão disponíveis. O acionador, em seguida, aguarda para resposta do ponto de extremidade antes acionadas. 

Os acionadores têm esses elementos de nível superior, apesar de alguns são opcionais:  
  
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
| <*trigger-name*> | String | O nome do acionador | 
| <*trigger-type*> | String | O tipo de Acionador, como "Http" ou "ApiConnection" | 
| <*trigger-inputs*> | Objeto JSON | As entradas que definem o comportamento do acionador | 
| <*time-unit*> | String | A unidade de tempo que descreve a frequência com que o acionador é acionado: "Segunda", "Minuto", "Hour", "Day", "Week", "Mês" | 
| <*number-of-time-units*> | Número inteiro | Um valor que especifica a frequência com que o acionador é acionado com base na frequência, o que é o número de unidades de tempo de espera até que o acionador é acionado novamente <p>Seguem-se os intervalos mínimos e máximo: <p>-Mês: 1-16 meses </br>-Dia: dias de 1 a 500 </br>-Hora: 1-12 000 horas </br>-Minuto: 1-72,000 minutos </br>-Segundo: 1-9,999,999 segundos<p>Por exemplo, se o intervalo é de 6 e a frequência é "Mês", a periodicidade é a cada 6 meses. | 
|||| 

*Opcional*

| Value | Type | Descrição | 
|-------|------|-------------| 
| <*array-with-conditions*> | Array | Uma matriz que contém um ou mais [condições](#trigger-conditions) que determinar se deve executar o fluxo de trabalho. Disponível apenas para acionadores. | 
| <*runtime-config-options*> | Objeto JSON | Pode alterar o comportamento de tempo de execução do acionador definindo `runtimeConfiguration` propriedades. Para obter mais informações, consulte [definições de configuração de tempo de execução](#runtime-config-options). | 
| <*splitOn-expression*> | String | Para acionadores que retornam uma matriz, pode especificar uma expressão que [divide ou *debatches* ](#split-on-debatch) matriz de itens em várias instâncias de fluxo de trabalho para processamento. | 
| <*operation-option*> | String | Pode alterar o comportamento predefinido ao definir o `operationOptions` propriedade. Para obter mais informações, consulte [opções de operação](#operation-options). | 
|||| 

## <a name="trigger-types-list"></a>Lista de tipos de Acionador

Cada tipo de Acionador tem uma interface diferente e entradas que definem o comportamento do acionador. 

### <a name="built-in-triggers"></a>Acionadores incorporados

| Tipo de acionador | Descrição | 
|--------------|-------------| 
| [**HTTP**](#http-trigger) | Verifica ou *inquéritos* qualquer ponto final. Este ponto final deve estar em conformidade com um contrato de Acionador específico usando um padrão assíncrono "202" ou retornando uma matriz. | 
| [**HTTPWebhook**](#http-webhook-trigger) | Cria um ponto final que pode ser chamado para a aplicação lógica, mas chama o URL especificado para registar ou anular o registo. |
| [**Periodicidade**](#recurrence-trigger) | É acionado com base numa agenda definida. Pode definir uma data futura e a hora para trabalharem este acionador. Com base na frequência, também pode especificar tempos e dias para a execução de seu fluxo de trabalho. | 
| [**Pedido**](#request-trigger)  | Cria um ponto final que pode ser chamado para a aplicação lógica e também é conhecido como um acionador "manual". Por exemplo, veja [chamar, acionar, ou aninhar fluxos de trabalho com pontos de extremidade HTTP](../logic-apps/logic-apps-http-endpoint.md). | 
||| 

### <a name="managed-api-triggers"></a>Acionadores de API geridos

| Tipo de acionador | Descrição | 
|--------------|-------------| 
| [**ApiConnection**](#apiconnection-trigger) | Verifica ou *inquéritos* um ponto de extremidade usando [APIs geridas pela Microsoft](../connectors/apis-list.md). | 
| [**ApiConnectionWebhook**](#apiconnectionwebhook-trigger) | Cria um ponto final que pode ser chamado para a aplicação lógica chamando [APIs geridas pela Microsoft](../connectors/apis-list.md) para subscrever e anular a subscrição. | 
||| 

## <a name="triggers---detailed-reference"></a>Acionadores - referência detalhada

<a name="apiconnection-trigger"></a>

### <a name="apiconnection-trigger"></a>Acionador de APIConnection  

Este acionador verifica ou *inquéritos* um ponto de extremidade usando [APIs geridas pela Microsoft](../connectors/apis-list.md) para que os parâmetros para este acionador pode diferir com base no ponto final. Muitas secções nesta definição de Acionador são opcionais. Comportamento do acionador depende se pretende ou não estão incluídas secções.

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
| <*APIConnection_trigger_name*> | String | O nome do acionador | 
| <*connection-name*> | String | O nome para a ligação para a API gerenciada que utiliza o fluxo de trabalho | 
| <*method-type*> | String | O método HTTP para comunicar com a API gerenciada: "GET", "COLOCAR", "POST", "PATCH", "DELETE" | 
| <*api-operation*> | String | A operação de API de chamada | 
| <*time-unit*> | String | A unidade de tempo que descreve a frequência com que o acionador é acionado: "Segunda", "Minuto", "Hour", "Day", "Week", "Mês" | 
| <*number-of-time-units*> | Número inteiro | Um valor que especifica a frequência com que o acionador é acionado com base na frequência, o que é o número de unidades de tempo de espera até que o acionador é acionado novamente <p>Seguem-se os intervalos mínimos e máximo: <p>-Mês: 1-16 meses </br>-Dia: dias de 1 a 500 </br>-Hora: 1-12 000 horas </br>-Minuto: 1-72,000 minutos </br>-Segundo: 1-9,999,999 segundos<p>Por exemplo, se o intervalo é de 6 e a frequência é "Mês", a periodicidade é a cada 6 meses. | 
|||| 

*Opcional*

| Value | Type | Descrição | 
|-------|------|-------------| 
| <*retry-behavior*> | Objeto JSON | Personaliza o comportamento de repetição para falhas intermitentes, que têm o 408, 429 e código de estado de 5XX e quaisquer exceções de conetividade. Para obter mais informações, consulte [políticas de repetição](../logic-apps/logic-apps-exception-handling.md#retry-policies). | 
| <*query-parameters*> | Objeto JSON | Quaisquer parâmetros de consulta para incluir com a API de chamam. Por exemplo, o `"queries": { "api-version": "2018-01-01" }` adiciona o objeto `?api-version=2018-01-01` para a chamada. | 
| <*max-runs*> | Número inteiro | Por predefinição, instâncias de fluxo de trabalho de aplicação lógica são executados ao mesmo tempo, ou em paralelo até a [limite predefinido](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Para alterar este limite, definindo uma nova <*contagem*> valor, veja [simultaneidade de Acionador de alteração](#change-trigger-concurrency). | 
| <*max-runs-queue*> | Número inteiro | Quando a aplicação lógica já está em execução o número máximo de instâncias, que pode alterar com base na `runtimeConfiguration.concurrency.runs` propriedade, quaisquer novas execuções são colocadas nesta fila de cópia de segurança o [limite predefinido](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Para alterar o limite predefinido, consulte [limitam execuções de espera da alteração](#change-waiting-runs). | 
| <*splitOn-expression*> | String | Para acionadores que retornam as matrizes, essa expressão referencia a matriz de usar para que pode criar e executar uma instância de fluxo de trabalho para cada item da matriz, em vez de usar um loop "for each". <p>Por exemplo, esta expressão representa um item na matriz devolvida no conteúdo do corpo do acionador: `@triggerbody()?['value']` |
| <*operation-option*> | String | Pode alterar o comportamento predefinido ao definir o `operationOptions` propriedade. Para obter mais informações, consulte [opções de operação](#operation-options). |
||||

*Saídas*
 
| Elemento | Type | Descrição |
|---------|------|-------------|
| Cabeçalhos | Objeto JSON | Os cabeçalhos da resposta |
| corpo | Objeto JSON | O corpo da resposta |
| Código de estado | Número inteiro | O código de estado da resposta |
|||| 

*Exemplo*

Esta definição de Acionador verifica a existência de correio eletrónico todos os dias dentro da caixa de entrada para uma conta do Outlook do Office 365: 

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

### <a name="apiconnectionwebhook-trigger"></a>Acionador de ApiConnectionWebhook

Este acionador envia um pedido de subscrição para um ponto de extremidade através de um [API gerida pela Microsoft](../connectors/apis-list.md), fornece um *URL de retorno de chamada* para onde o ponto final pode enviar uma resposta e aguarda para o ponto final responder. Para obter mais informações, consulte [as assinaturas do ponto de extremidade](#subscribe-unsubscribe).

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
| <*connection-name*> | String | O nome para a ligação para a API gerenciada que utiliza o fluxo de trabalho | 
| <*body-content*> | Objeto JSON | Qualquer conteúdo da mensagem para enviar como carga para a API gerenciada | 
|||| 

*Opcional*

| Value | Type | Descrição | 
|-------|------|-------------| 
| <*retry-behavior*> | Objeto JSON | Personaliza o comportamento de repetição para falhas intermitentes, que têm o 408, 429 e código de estado de 5XX e quaisquer exceções de conetividade. Para obter mais informações, consulte [políticas de repetição](../logic-apps/logic-apps-exception-handling.md#retry-policies). | 
| <*query-parameters*> | Objeto JSON | Quaisquer parâmetros de consulta para incluir com a chamada de API <p>Por exemplo, o `"queries": { "api-version": "2018-01-01" }` adiciona o objeto `?api-version=2018-01-01` para a chamada. | 
| <*max-runs*> | Número inteiro | Por predefinição, instâncias de fluxo de trabalho de aplicação lógica são executados ao mesmo tempo, ou em paralelo até a [limite predefinido](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Para alterar este limite, definindo uma nova <*contagem*> valor, veja [simultaneidade de Acionador de alteração](#change-trigger-concurrency). | 
| <*max-runs-queue*> | Número inteiro | Quando a aplicação lógica já está em execução o número máximo de instâncias, que pode alterar com base na `runtimeConfiguration.concurrency.runs` propriedade, quaisquer novas execuções são colocadas nesta fila de cópia de segurança o [limite predefinido](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Para alterar o limite predefinido, consulte [limitam execuções de espera da alteração](#change-waiting-runs). | 
| <*splitOn-expression*> | String | Para acionadores que retornam as matrizes, essa expressão referencia a matriz de usar para que pode criar e executar uma instância de fluxo de trabalho para cada item da matriz, em vez de usar um loop "for each". <p>Por exemplo, esta expressão representa um item na matriz devolvida no conteúdo do corpo do acionador: `@triggerbody()?['value']` |
| <*operation-option*> | String | Pode alterar o comportamento predefinido ao definir o `operationOptions` propriedade. Para obter mais informações, consulte [opções de operação](#operation-options). | 
|||| 

*Exemplo*

Esta definição de Acionador assina a API do Office 365 Outlook, um URL de retorno de chamada para o ponto final da API e aguarda que o ponto final para responder quando chega um novo e-mail.

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

Este acionador verifica ou consulta o ponto final especificado com base na agenda de periodicidade especificados. Resposta do ponto de extremidade determina se o fluxo de trabalho é executada.

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
| <*method-type*> | String | O método HTTP a utilizar para consultar o ponto final especificado: "GET", "COLOCAR", "POST", "PATCH", "DELETE" | 
| <*endpoint-URL*> | String | O HTTP ou HTTPS URL para o ponto final para consultar <p>Tamanho máximo da cadeia de caracteres: 2 KB | 
| <*time-unit*> | String | A unidade de tempo que descreve a frequência com que o acionador é acionado: "Segunda", "Minuto", "Hour", "Day", "Week", "Mês" | 
| <*number-of-time-units*> | Número inteiro | Um valor que especifica a frequência com que o acionador é acionado com base na frequência, o que é o número de unidades de tempo de espera até que o acionador é acionado novamente <p>Seguem-se os intervalos mínimos e máximo: <p>-Mês: 1-16 meses </br>-Dia: dias de 1 a 500 </br>-Hora: 1-12 000 horas </br>-Minuto: 1-72,000 minutos </br>-Segundo: 1-9,999,999 segundos<p>Por exemplo, se o intervalo é de 6 e a frequência é "Mês", a periodicidade é a cada 6 meses. | 
|||| 

*Opcional*

| Value | Type | Descrição | 
|-------|------|-------------| 
| <*header-content*> | Objeto JSON | Os cabeçalhos para enviar com o pedido <p>Por exemplo, para definir o idioma e o tipo de um pedido: <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` |
| <*body-content*> | String | O conteúdo da mensagem a enviar como payload com o pedido | 
| <*método de autenticação*> | Objeto JSON | O método o pedido utiliza para autenticação. Para obter mais informações, consulte [autenticação de saída do Scheduler](../scheduler/scheduler-outbound-authentication.md). Além do Scheduler, o `authority` propriedade é suportada. Quando não especificado, o valor predefinido é `https://login.windows.net`, mas pode usar um valor diferente, como`https://login.windows\-ppe.net`. |
| <*retry-behavior*> | Objeto JSON | Personaliza o comportamento de repetição para falhas intermitentes, que têm o 408, 429 e código de estado de 5XX e quaisquer exceções de conetividade. Para obter mais informações, consulte [políticas de repetição](../logic-apps/logic-apps-exception-handling.md#retry-policies). |  
 <*query-parameters*> | Objeto JSON | Quaisquer parâmetros de consulta para incluir com o pedido <p>Por exemplo, o `"queries": { "api-version": "2018-01-01" }` adiciona o objeto `?api-version=2018-01-01` ao pedido. | 
| <*max-runs*> | Número inteiro | Por predefinição, instâncias de fluxo de trabalho de aplicação lógica são executados ao mesmo tempo, ou em paralelo até a [limite predefinido](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Para alterar este limite, definindo uma nova <*contagem*> valor, veja [simultaneidade de Acionador de alteração](#change-trigger-concurrency). | 
| <*max-runs-queue*> | Número inteiro | Quando a aplicação lógica já está em execução o número máximo de instâncias, que pode alterar com base na `runtimeConfiguration.concurrency.runs` propriedade, quaisquer novas execuções são colocadas nesta fila de cópia de segurança o [limite predefinido](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Para alterar o limite predefinido, consulte [limitam execuções de espera da alteração](#change-waiting-runs). | 
| <*operation-option*> | String | Pode alterar o comportamento predefinido ao definir o `operationOptions` propriedade. Para obter mais informações, consulte [opções de operação](#operation-options). | 
|||| 

*Saídas*

| Elemento | Type | Descrição |
|---------|------|-------------| 
| Cabeçalhos | Objeto JSON | Os cabeçalhos da resposta | 
| corpo | Objeto JSON | O corpo da resposta | 
| Código de estado | Número inteiro | O código de estado da resposta | 
|||| 

*Requisitos para pedidos recebidos*

Para funcionar bem com a sua aplicação lógica, o ponto final tem de estar em conformidade com um padrão de Acionador específico ou um contrato e reconhecer estas propriedades:  
  
| Resposta | Necessário | Descrição | 
|----------|----------|-------------| 
| Código de estado | Sim | O "200 OK" código de estado inicia a execução. Qualquer outro código de estado não iniciar uma execução. | 
| Cabeçalho retry-after | Não | O número de segundos até que a aplicação lógica consulta o ponto final novamente | 
| Cabeçalho de localização | Não | O URL em que o intervalo de consulta seguinte. Se não for especificado, o URL original é utilizado. | 
|||| 

*Comportamentos de exemplo para diferentes solicitações*

| Código de estado | Repetir após | Comportamento | 
|-------------|-------------|----------|
| 200 | {num} | Executar o fluxo de trabalho, em seguida, verificar novamente para obter mais dados após a periodicidade definida. | 
| 200 | 10 segundos | Executar o fluxo de trabalho, em seguida, verificar novamente para obter mais dados após 10 segundos. |  
| 202 | 60 segundos | Não acione o fluxo de trabalho. Da próxima tentativa ocorre num minuto, sujeitos a periodicidade definido. Se a periodicidade definida é de menos de um minuto, o cabeçalho retry-after tem precedência. Caso contrário, é utilizada a periodicidade definida. | 
| 400 | {num} | Pedido inválido, não execute o fluxo de trabalho. Se não `retryPolicy` é definida, a política predefinida é utilizado. Depois do número de tentativas foi atingido, o acionador verifica novamente para os dados após a periodicidade definida. | 
| 500 | {num}| Erro de servidor, não executar o fluxo de trabalho. Se não `retryPolicy` é definida, a política predefinida é utilizado. Depois do número de tentativas foi atingido, o acionador verifica novamente para os dados após a periodicidade definida. | 
|||| 

<a name="http-webhook-trigger"></a>

### <a name="httpwebhook-trigger"></a>Acionador de HTTPWebhook  

Este acionador torna a sua aplicação lógica pode ser chamada através da criação de um ponto final que pode registrar uma assinatura ao chamar o URL de ponto final especificado. Quando cria este acionador no seu fluxo de trabalho, um pedido de saída faz a chamada para registar a subscrição. Dessa forma, o acionador pode começar a ouvir eventos. Quando uma operação faz este acionador inválido, um pedido de saída torna a chamada para cancelar a subscrição. Para obter mais informações, consulte [as assinaturas do ponto de extremidade](#subscribe-unsubscribe).

Também pode especificar [limites assíncronas](#asynchronous-limits) num **HTTPWebhook** acionador.
Comportamento do acionador depende as secções que se utilizar ou omita a. 

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

Alguns valores, tais como <*tipo de método*>, estão disponíveis para ambos os `"subscribe"` e `"unsubscribe"` objetos.

*Necessário*

| Value | Type | Descrição | 
|-------|------|-------------| 
| <*method-type*> | String | O método HTTP a utilizar para o pedido de subscrição: "GET", "PUT", "POST", "PATCH" ou "Eliminar" | 
| <*endpoint-subscribe-URL*> | String | O URL de ponto final para onde enviar o pedido de subscrição | 
|||| 

*Opcional*

| Value | Type | Descrição | 
|-------|------|-------------| 
| <*method-type*> | String | O método HTTP a utilizar para a solicitação de cancelamento: "GET", "PUT", "POST", "PATCH" ou "Eliminar" | 
| <*endpoint-unsubscribe-URL*> | String | O URL de ponto final para onde enviar a solicitação de cancelamento | 
| <*body-content*> | String | Qualquer conteúda enviar o pedido de subscrição ou cancelamento de mensagem | 
| <*método de autenticação*> | Objeto JSON | O método o pedido utiliza para autenticação. Para obter mais informações, consulte [autenticação de saída do Scheduler](../scheduler/scheduler-outbound-authentication.md). |
| <*retry-behavior*> | Objeto JSON | Personaliza o comportamento de repetição para falhas intermitentes, que têm o 408, 429 e código de estado de 5XX e quaisquer exceções de conetividade. Para obter mais informações, consulte [políticas de repetição](../logic-apps/logic-apps-exception-handling.md#retry-policies). | 
| <*max-runs*> | Número inteiro | Por predefinição, instâncias de fluxo de trabalho de aplicação lógica são executados ao mesmo tempo, ou em paralelo até a [limite predefinido](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Para alterar este limite, definindo uma nova <*contagem*> valor, veja [simultaneidade de Acionador de alteração](#change-trigger-concurrency). | 
| <*max-runs-queue*> | Número inteiro | Quando a aplicação lógica já está em execução o número máximo de instâncias, que pode alterar com base na `runtimeConfiguration.concurrency.runs` propriedade, quaisquer novas execuções são colocadas nesta fila de cópia de segurança o [limite predefinido](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Para alterar o limite predefinido, consulte [limitam execuções de espera da alteração](#change-waiting-runs). | 
| <*operation-option*> | String | Pode alterar o comportamento predefinido ao definir o `operationOptions` propriedade. Para obter mais informações, consulte [opções de operação](#operation-options). | 
|||| 

*Saídas* 

| Elemento | Type | Descrição |
|---------|------|-------------| 
| Cabeçalhos | Objeto JSON | Os cabeçalhos da resposta | 
| corpo | Objeto JSON | O corpo da resposta | 
| Código de estado | Número inteiro | O código de estado da resposta | 
|||| 

*Exemplo*

Este acionador cria uma subscrição para o ponto final especificado, fornece um URL de retorno de chamada exclusivos e aguarda artigos sobre tecnologia recentemente publicado.

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

### <a name="recurrence-trigger"></a>Acionador de periodicidade  

Este acionador é executado com base na agenda de periodicidade especificados e fornece uma forma fácil para criar um fluxo de trabalho regularmente em execução. 

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

| Value | Type | Descrição | 
|-------|------|-------------| 
| <*time-unit*> | String | A unidade de tempo que descreve a frequência com que o acionador é acionado: "Segunda", "Minuto", "Hour", "Day", "Week", "Mês" | 
| <*number-of-time-units*> | Número inteiro | Um valor que especifica a frequência com que o acionador é acionado com base na frequência, o que é o número de unidades de tempo de espera até que o acionador é acionado novamente <p>Seguem-se os intervalos mínimos e máximo: <p>-Mês: 1-16 meses </br>-Dia: dias de 1 a 500 </br>-Hora: 1-12 000 horas </br>-Minuto: 1-72,000 minutos </br>-Segundo: 1-9,999,999 segundos<p>Por exemplo, se o intervalo é de 6 e a frequência é "Mês", a periodicidade é a cada 6 meses. | 
|||| 

*Opcional*

| Value | Type | Descrição | 
|-------|------|-------------| 
| <*start-date-time-with-format-YYYY-MM-DDThh:mm:ss*> | String | A data de início e a hora neste formato: <p>AAAA-MM-ddTHH se especificar um fuso horário <p>-ou- <p>AAAA-MM-: ssZ se não especificar um fuso horário <p>Por exemplo, se quiser 18 de Setembro de 2017, às 14:00, em seguida, especifique "2017-09-18T14:00:00" e especificar um fuso horário, como "Hora padrão do Pacífico", ou "2017-09-18T14:00:00Z" sem um fuso horário. <p>**Nota:** A hora de início tem de seguir a [especificação de tempo de data ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations) na [formato de hora UTC data](https://en.wikipedia.org/wiki/Coordinated_Universal_Time), mas sem uma [posun UTC místního](https://en.wikipedia.org/wiki/UTC_offset). Se não especificar um fuso horário, tem de adicionar a letra "Z" no final, sem quaisquer espaços. Este "Z" refere-se para o equivalente [tempo nautical](https://en.wikipedia.org/wiki/Nautical_time). <p>Para agendamentos simples, a hora de início é a primeira ocorrência, enquanto para agendas complexas, o acionador não dispara qualquer mais cedo do que a hora de início. Para obter mais informações sobre datas de início e horas, consulte [criar e agendar tarefas em execução regularmente](../connectors/connectors-native-recurrence.md). | 
| <*time-zone*> | String | Aplica-se apenas quando especificar uma hora de início porque este acionador não aceita [posun UTC místního](https://en.wikipedia.org/wiki/UTC_offset). Especifique o fuso horário que pretende aplicar. | 
| <*one-or-more-hour-marks*> | Número inteiro ou matriz de números inteiros | Se especificar "Dia" ou "Week" para `frequency`, pode especificar um ou mais números inteiros de 0 a 23, separados por vírgulas, como as horas do dia que deseja executar o fluxo de trabalho. <p>Por exemplo, se especificar "10", "12" e "14", obter 10 AM e PM 2 como as marcas de hora 12 PM. | 
| <*one-or-more-minute-marks*> | Número inteiro ou matriz de números inteiros | Se especificar "Day" ou "Week" para `frequency`, pode especificar um ou mais números inteiros de 0 a 59, separados por vírgulas, como os minutos da hora quando quiser executar o fluxo de trabalho. <p>Por exemplo, pode especificar "30" como a marca de minuto e usando o exemplo anterior de horas do dia, obtém 10:30, 12 17:30 e 2 17:30. | 
| weekDays | Cadeia de caracteres ou matriz de cadeia de caracteres | Se especificar "Week" para `frequency`, pode especificar um ou mais dias, separados por vírgulas, quando deseja executar o fluxo de trabalho: "Segunda-feira", "Terça-feira", "Quarta-feira", "Quinta-feira", "Sexta-feira", "Sábado" e "Domingo" | 
| <*max-runs*> | Número inteiro | Por predefinição, instâncias de fluxo de trabalho de aplicação lógica são executados ao mesmo tempo, ou em paralelo até a [limite predefinido](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Para alterar este limite, definindo uma nova <*contagem*> valor, veja [simultaneidade de Acionador de alteração](#change-trigger-concurrency). | 
| <*max-runs-queue*> | Número inteiro | Quando a aplicação lógica já está em execução o número máximo de instâncias, que pode alterar com base na `runtimeConfiguration.concurrency.runs` propriedade, quaisquer novas execuções são colocadas nesta fila de cópia de segurança o [limite predefinido](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Para alterar o limite predefinido, consulte [limitam execuções de espera da alteração](#change-waiting-runs). | 
| <*operation-option*> | String | Pode alterar o comportamento predefinido ao definir o `operationOptions` propriedade. Para obter mais informações, consulte [opções de operação](#operation-options). | 
|||| 

*Exemplo 1*

Este acionador de periodicidade básica é executada diariamente:

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

Pode especificar uma data de início e hora para a ativação do acionador. Este acionador de periodicidade começa na data especificada e, em seguida, é acionado diariamente:

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

Este acionador de periodicidade começa em 9 de Setembro de 2017 em às 14:00 e é acionado semanalmente cada segunda-feira às 10:30, 12 17:30 e 2 17:30, horário do Pacífico:

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

Para obter mais informações e exemplos para este acionador, consulte [criar e agendar tarefas em execução regularmente](../connectors/connectors-native-recurrence.md).

<a name="request-trigger"></a>

### <a name="request-trigger"></a>Acionador de pedido

Este acionador torna a sua aplicação lógica pode ser chamada através da criação de um ponto final que pode aceitar os pedidos recebidos. Para este acionador, forneça um esquema JSON que descreve e valida a carga ou entradas que o acionador recebe da solicitação recebida. O esquema também facilita a propriedades de Acionador para referência de ações subsequentes no fluxo de trabalho. 

Para chamar este acionador, tem de utilizar o `listCallbackUrl` API, que está descrito com o [API de REST do serviço de fluxo de trabalho](https://docs.microsoft.com/rest/api/logic/workflows). Para saber como utilizar este acionador como um ponto final HTTP, veja [chamar, acionar, ou aninhar fluxos de trabalho com pontos de extremidade HTTP](../logic-apps/logic-apps-http-endpoint.md).

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

| Value | Type | Descrição | 
|-------|------|-------------| 
| <*property-name*> | String | O nome de uma propriedade no esquema JSON, que descreve o payload | 
| <*property-type*> | String | O tipo de propriedade | 
|||| 

*Opcional*

| Value | Type | Descrição | 
|-------|------|-------------| 
| <*method-type*> | String | O método que solicitações de entrada tem de utilizar para chamar a sua aplicação lógica: "GET", "COLOCAR", "POST", "PATCH", "DELETE" |
| <*relative-path-for-accepted-parameter*> | String | O caminho relativo para o parâmetro que pode aceitar o URL do ponto de extremidade | 
| <*required-properties*> | Array | Uma ou mais propriedades que necessitam de valores | 
| <*max-runs*> | Número inteiro | Por predefinição, instâncias de fluxo de trabalho de aplicação lógica são executados ao mesmo tempo, ou em paralelo até a [limite predefinido](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Para alterar este limite, definindo uma nova <*contagem*> valor, veja [simultaneidade de Acionador de alteração](#change-trigger-concurrency). | 
| <*max-runs-queue*> | Número inteiro | Quando a aplicação lógica já está em execução o número máximo de instâncias, que pode alterar com base na `runtimeConfiguration.concurrency.runs` propriedade, quaisquer novas execuções são colocadas nesta fila de cópia de segurança o [limite predefinido](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Para alterar o limite predefinido, consulte [limitam execuções de espera da alteração](#change-waiting-runs). | 
| <*operation-option*> | String | Pode alterar o comportamento predefinido ao definir o `operationOptions` propriedade. Para obter mais informações, consulte [opções de operação](#operation-options). | 
|||| 

*Exemplo*

Este acionador Especifica que uma solicitação de entrada tem de utilizar o método HTTP POST para chamar o acionador e inclui um esquema que valida a entrada da solicitação recebida: 

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

## <a name="trigger-conditions"></a>Condições de acionamento

Para qualquer acionador e apenas acionadores, pode incluir uma matriz que contém uma ou mais expressões para condições que determinam se o fluxo de trabalho deve ser executado. Para adicionar o `conditions` propriedade a um acionador na sua aplicação lógica, abrir a aplicação lógica no editor de vista de código.

Por exemplo, pode especificar que um acionador é acionado quando um Web site retorna apenas um erro de servidor interno referenciando o código de estado do acionador no `conditions` propriedade:

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

Por predefinição, um acionador é acionado apenas depois de obter um "200 OK" resposta. Quando uma expressão fizer referência a código de estado de um acionador, o comportamento predefinido do acionador é substituído. Então, se pretender que o acionador a acionar para mais do que um código de estado, por exemplo, "200" e o código de estado "201", tem de incluir esta expressão como sua condição: 

`@or(equals(triggers().code, 200),equals(triggers().code, 201))` 

<a name="split-on-debatch"></a>

## <a name="trigger-multiple-runs"></a>Acionar várias execuções

Se o acionador retorna uma matriz para a sua aplicação lógica processar, às vezes, um loop "for each" poderá demorar demasiado tempo a processar cada item da matriz. Em vez disso, pode utilizar o **SplitOn** propriedade no seu acionador para *debatch* a matriz. Divisões, divida os itens de matriz e inicia uma nova instância de aplicação lógica que é executada para cada item da matriz. Essa abordagem é útil, por exemplo, quando quer consultar um ponto de extremidade que pode devolver vários itens novos entre intervalos de consulta.
Para o número máximo de matriz de itens que **SplitOn** pode processar numa execução de aplicação lógica única, consulte [limites e configuração](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). 

> [!NOTE]
> Não é possível usar **SplitOn** com um padrão de resposta síncrona. Qualquer fluxo de trabalho que utiliza **SplitOn** e inclui uma resposta de ação é executada de forma assíncrona e enviará imediatamente uma `202 ACCEPTED` resposta.

Se o ficheiro de Swagger do acionador descreve uma carga que é uma matriz, o **SplitOn** propriedade é automaticamente adicionada ao seu acionador. Caso contrário, adicione esta propriedade no payload de resposta que tenha a matriz que pretende debatch. 

*Exemplo*

Suponha que tenha uma API que retorna essa resposta: 
  
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
 
A aplicação lógica precisa apenas o conteúdo da matriz no `Rows`, para que possa criar um acionador semelhante a este exemplo:

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
> Se utilizar o `SplitOn` de comando, não é possível obter as propriedades que estão fora da matriz. Portanto, neste exemplo, não é possível obter o `status` devolvido de propriedade na resposta da API.
> 
> Para evitar uma falha se o `Rows` propriedade não existir, este exemplo utiliza o `?` operador.

A definição de fluxo de trabalho pode agora utilizar `@triggerBody().name` para obter o `name` valores, que são `"customer-name-one"` de primeira execução e `"customer-name-two"` da segunda execução. Então, o acionador produz olhada, como nestes exemplos:

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

O Azure Logic Apps fornece vários tipos de ação - cada uma com diferentes entradas que definem o comportamento exclusivo de uma ação. 

Ações têm esses elementos de alto nível, embora alguns são opcionais:

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

| Value | Type | Descrição | 
|-------|------|-------------|
| <*action-name*> | String | O nome da ação | 
| <*action-type*> | String | O tipo de ação, por exemplo, "Http" ou "ApiConnection"| 
| <*input-name*> | String | O nome de entrada que define o comportamento da ação | 
| <*input-value*> | Vários | O valor de entrada, que pode ser uma cadeia de caracteres, inteiros, objeto JSON e assim por diante | 
| <*previous-trigger-or-action-status*> | Objeto JSON | O nome e o resultado de estado para o acionador ou ação que tem de executar imediatamente antes de pode executar esta ação atual | 
|||| 

*Opcional*

| Value | Type | Descrição | 
|-------|------|-------------|
| <*retry-behavior*> | Objeto JSON | Personaliza o comportamento de repetição para falhas intermitentes, que têm o 408, 429 e código de estado de 5XX e quaisquer exceções de conetividade. Para obter mais informações, consulte as políticas de repetição. | 
| <*runtime-config-options*> | Objeto JSON | Para algumas ações, pode alterar o comportamento da ação em tempo de execução definindo `runtimeConfiguration` propriedades. Para obter mais informações, consulte [definições de configuração de tempo de execução](#runtime-config-options). | 
| <*operation-option*> | String | Para algumas ações, pode alterar o comportamento predefinido ao definir o `operationOptions` propriedade. Para obter mais informações, consulte [opções de operação](#operation-options). | 
|||| 

## <a name="action-types-list"></a>Lista de tipos de ação

Seguem-se alguns tipos de ação frequentemente utilizadas: 

* [Tipos de ações incorporadas](#built-in-actions) como nestes exemplos e muito mais: 

  * [**HTTP** ](#http-action) para chamar pontos finais através de HTTP ou HTTPS

  * [**Resposta** ](#response-action) para responder a pedidos

  * [**Função** ](#function-action) para chamar as funções do Azure

  * Ações de operação de dados, como [ **aderir**](#join-action), [ **Compose**](#compose-action), [ **tabela** ](#table-action), [ **Selecione**](#select-action)e outras pessoas que criam ou transformar dados de várias entradas

  * [**Fluxo de trabalho** ](#workflow-action) para chamar outro fluxo de trabalho de aplicação lógica

* [Gerido tipos de ação de API](#managed-api-actions) tal como [ **ApiConnection** ](#apiconnection-action) e [ **ApiConnectionWebHook** ](#apiconnectionwebhook-action) que chamar vários APIs e conectores geridos pela Microsoft, por exemplo, do Azure Service Bus, Outlook do Office 365, Power BI, armazenamento de Blobs do Azure, OneDrive, GitHub e muito mais

* [Controlar os tipos de ação de fluxo de trabalho](#control-workflow-actions) tal como [ **se**](#if-action), [ **Foreach**](#foreach-action), [ **comutador**  ](#switch-action), [ **Âmbito**](#scope-action), e [ **até**](#until-action), que contêm outras ações e ajudá-lo a organizar a execução de fluxo de trabalho

<a name="built-in-actions"></a>

### <a name="built-in-actions"></a>Ações incorporadas

| Tipo de ação | Descrição | 
|-------------|-------------| 
| [**Compor**](#compose-action) | Cria uma única saída a partir de entradas, que podem ter vários tipos. | 
| [**Função**](#function-action) | Chama uma função do Azure. | 
| [**HTTP**](#http-action) | Chama um ponto final HTTP. | 
| [**Join**](#join-action) | Cria uma cadeia de caracteres a partir de todos os itens numa matriz e separa esses itens com um caráter delimitador especificado. | 
| [**Parse JSON**](#parse-json-action) | Cria conteúdo tokens amigável de utilizador a partir das propriedades em JSON. Em seguida, pode referenciar essas propriedades, incluindo os tokens na sua aplicação lógica. | 
| [**Query**](#query-action) | Cria uma matriz de itens na outra matriz com base numa condição ou filtro. | 
| [**Resposta**](#response-action) | Cria uma resposta a uma chamada de entrada ou a pedido. | 
| [**Select**](#select-action) | Cria uma matriz com objetos JSON, transformando os itens a partir de outra matriz com base no mapa especificado. | 
| [**Table**](#table-action) | Cria uma tabela CSV ou HTML de uma matriz. | 
| [**Terminate**](#terminate-action) | Interrompe um fluxo de trabalho ativamente em execução. | 
| [**Wait**](#wait-action) | Interrompe o fluxo de trabalho durante um período de tempo especificado ou até a data e hora especificadas. | 
| [**Fluxo de trabalho**](#workflow-action) | Aninha um fluxo de trabalho dentro de outro fluxo de trabalho. | 
||| 

<a name="managed-api-actions"></a>

### <a name="managed-api-actions"></a>Ações de API geridas

| Tipo de ação | Descrição | 
|-------------|-------------|  
| [**ApiConnection**](#apiconnection-action) | Chama um ponto de final HTTP utilizando um [API gerida pela Microsoft](../connectors/apis-list.md). | 
| [**ApiConnectionWebhook**](#apiconnectionwebhook-action) | Funciona como Webhook de HTTP, mas usa um [API gerida pela Microsoft](../connectors/apis-list.md). | 
||| 

<a name="control-workflow-actions"></a>

### <a name="control-workflow-actions"></a>Ações de fluxo de trabalho de controle

Estas ações ajudam a controlar a execução de fluxo de trabalho e incluir outras ações. De fora de uma ação de fluxo de trabalho de controle, pode fazem referência diretamente ações dentro dessa ação de fluxo de trabalho de controle. Por exemplo, se tem um `Http` ação dentro de um âmbito, pode referenciar o `@body('Http')` expressão em qualquer lugar no fluxo de trabalho. No entanto, ações que existem dentro de uma ação de fluxo de trabalho de controle podem apenas "executar após" outras ações que estão na mesma estrutura de fluxo de trabalho de controle.

| Tipo de ação | Descrição | 
|-------------|-------------| 
| [**ForEach**](#foreach-action) | Execute as mesmas ações num loop para cada item numa matriz. | 
| [**If**](#if-action) | Executadas ações com base em se a condição especificada é true ou false. | 
| [**Âmbito**](#scope-action) | Execute ações com base no estado do grupo de um conjunto de ações. | 
| [**Switch**](#switch-action) | Execute ações organizadas em casos quando valores de expressões, objetos ou tokens correspondem aos valores especificados por cada caso. | 
| [**Until**](#until-action) | Execute ações num loop até que a condição especificada for verdadeira. | 
|||  

## <a name="actions---detailed-reference"></a>Ações - referência detalhada

<a name="apiconnection-action"></a>

### <a name="apiconnection-action"></a>Ação de APIConnection

Esta ação envia um pedido HTTP para um [API gerida pela Microsoft](../connectors/apis-list.md) e necessita de informações sobre a API e parâmetros além de uma referência a uma ligação válida. 

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

| Value | Type | Descrição | 
|-------|------|-------------| 
| <*action-name*> | String | O nome da ação fornecida pelo conector do | 
| <*api-name*> | String | O nome da API gerida pela Microsoft que é utilizado para a ligação | 
| <*method-type*> | String | O método HTTP para chamar a API: "GET", "PUT", "POST", "PATCH" ou "Eliminar" | 
| <*api-operation*> | String | A operação de API de chamada | 
|||| 

*Opcional*

| Value | Type | Descrição | 
|-------|------|-------------| 
| <*other-action-specific-input-properties*> | Objeto JSON | Quaisquer outras propriedades de entrada que se aplicam a esta ação específica | 
| <*retry-behavior*> | Objeto JSON | Personaliza o comportamento de repetição para falhas intermitentes, que têm o 408, 429 e código de estado de 5XX e quaisquer exceções de conetividade. Para obter mais informações, consulte [políticas de repetição](../logic-apps/logic-apps-exception-handling.md#retry-policies). | 
| <*query-parameters*> | Objeto JSON | Quaisquer parâmetros de consulta para incluir com a API de chamam. <p>Por exemplo, o `"queries": { "api-version": "2018-01-01" }` adiciona o objeto `?api-version=2018-01-01` para a chamada. | 
| <*other-action-specific-properties*> | Objeto JSON | Outras propriedades que se aplicam a esta ação específica | 
|||| 

*Exemplo*

Esta definição descreve a **enviar um e-mail** ação para o conector do Outlook do Office 365, que é uma API gerida pela Microsoft: 

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

### <a name="apiconnectionwebhook-action"></a>Ação de APIConnectionWebhook

Esta ação envia um pedido de subscrição através de HTTP para um ponto de extremidade através de um [API gerida pela Microsoft](../connectors/apis-list.md), fornece um *URL de retorno de chamada* para onde o ponto final pode enviar uma resposta e aguarda para o ponto final para responda. Para obter mais informações, consulte [as assinaturas do ponto de extremidade](#subscribe-unsubscribe).

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

Alguns valores, tais como <*tipo de método*>, estão disponíveis para ambos os `"subscribe"` e `"unsubscribe"` objetos.

*Necessário*

| Value | Type | Descrição | 
|-------|------|-------------| 
| <*action-name*> | String | O nome da ação fornecida pelo conector do | 
| <*method-type*> | String | O método HTTP a utilizar para subscrever ou anular a subscrição de um ponto de extremidade: "GET", "PUT", "POST", "PATCH" ou "Eliminar" | 
| <*api-subscribe-URL*> | String | O URI a utilizar para subscrever a API | 
|||| 

*Opcional*

| Value | Type | Descrição | 
|-------|------|-------------| 
| <*api-unsubscribe-URL*> | String | O URI a utilizar para anular a subscrição da API | 
| <*header-content*> | Objeto JSON | Quaisquer cabeçalhos para enviar o pedido <p>Por exemplo, para definir o idioma e escreva num pedido: <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` |
| <*body-content*> | Objeto JSON | Qualquer conteúdo da mensagem a enviar no pedido | 
| <*método de autenticação*> | Objeto JSON | O método o pedido utiliza para autenticação. Para obter mais informações, consulte [autenticação de saída do Scheduler](../scheduler/scheduler-outbound-authentication.md). |
| <*retry-behavior*> | Objeto JSON | Personaliza o comportamento de repetição para falhas intermitentes, que têm o 408, 429 e código de estado de 5XX e quaisquer exceções de conetividade. Para obter mais informações, consulte [políticas de repetição](../logic-apps/logic-apps-exception-handling.md#retry-policies). | 
| <*query-parameters*> | Objeto JSON | Quaisquer parâmetros de consulta para incluir com a chamada de API <p>Por exemplo, o `"queries": { "api-version": "2018-01-01" }` adiciona o objeto `?api-version=2018-01-01` para a chamada. | 
| <*other-action-specific-input-properties*> | Objeto JSON | Quaisquer outras propriedades de entrada que se aplicam a esta ação específica | 
| <*other-action-specific-properties*> | Objeto JSON | Outras propriedades que se aplicam a esta ação específica | 
|||| 

Também pode especificar limites numa **ApiConnectionWebhook** ação da mesma forma como [limites HTTP assíncronas](#asynchronous-limits).

<a name="compose-action"></a>

### <a name="compose-action"></a>Ação de composição

Esta ação cria uma única saída de várias entradas, incluindo expressões. A saída e de entradas, podem ter qualquer tipo que o Azure Logic Apps suporta de forma nativa, como matrizes, objetos JSON, XML e binário.
Em seguida, pode utilizar o resultado da ação em outras ações. 

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
| <*inputs-to-compose*> | Qualquer | As entradas para a criação de uma única saída | 
|||| 

*Exemplo 1*

Esta definição de ação intercala `abcdefg ` com um espaço à direita e o valor `1234`:

```json
"Compose": {
   "type": "Compose",
   "inputs": "abcdefg 1234",
   "runAfter": {}
},
```

Eis a saída que esta ação cria:

`abcdefg 1234`

*Exemplo 2*

Esta definição de ação intercala uma variável de cadeia de caracteres que contém `abcdefg` e uma variável de inteiro que contém `1234`:

```json
"Compose": {
   "type": "Compose",
   "inputs": "@{variables('myString')}@{variables('myInteger')}",
   "runAfter": {}
},
```

Eis a saída que esta ação cria:

`"abcdefg1234"`

<a name="function-action"></a>

### <a name="function-action"></a>Ação de função

Esta ação chama um criado anteriormente [função do Azure](../azure-functions/functions-create-first-azure-function.md).

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

| Value | Type | Descrição | 
|-------|------|-------------|  
| <*Azure-function-ID*> | String | O ID de recurso para a função do Azure que pretende chamar. Este é o formato para este valor:<p>"/subscriptions/<*Azure-subscription-ID*>/resourceGroups/<*Azure-resource-group*>/providers/Microsoft.Web/sites/<*Azure-function-app-name*>/functions/<*Azure-function-name*>" | 
| <*method-type*> | String | O método HTTP a utilizar para chamar a função: "GET", "PUT", "POST", "PATCH" ou "Eliminar" <p>Se não for especificado, a predefinição é o método "POST". | 
||||

*Opcional*

| Value | Type | Descrição | 
|-------|------|-------------|  
| <*header-content*> | Objeto JSON | Quaisquer cabeçalhos para enviar com a chamada <p>Por exemplo, para definir o idioma e escreva num pedido: <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` |
| <*body-content*> | Objeto JSON | Qualquer conteúdo da mensagem a enviar no pedido | 
| <*query-parameters*> | Objeto JSON | Quaisquer parâmetros de consulta para incluir com a chamada de API <p>Por exemplo, o `"queries": { "api-version": "2018-01-01" }` adiciona o objeto `?api-version=2018-01-01` para a chamada. | 
| <*other-action-specific-input-properties*> | Objeto JSON | Quaisquer outras propriedades de entrada que se aplicam a esta ação específica | 
| <*other-action-specific-properties*> | Objeto JSON | Outras propriedades que se aplicam a esta ação específica | 
||||

Ao guardar a aplicação lógica, o motor do Logic Apps executa estas verificações a função de referenciado:

* O fluxo de trabalho tem de ter acesso à função.

* O fluxo de trabalho pode utilizar apenas um acionador HTTP padrão ou um acionador de webhook JSON genérica. 

  O motor do Logic Apps obtém e armazena em cache o URL do acionador, que é utilizado no tempo de execução. No entanto, se qualquer operação invalida o URL em cache, o **função** ação falhar em tempo de execução. Para corrigir este problema, guarde a aplicação lógica novamente para que a aplicação lógica recebe e armazena em cache o URL de Acionador novamente.

* A função não pode ter qualquer rota definida.

* São permitidos apenas "function" e níveis de autorização "anonymous". 

*Exemplo*

Esta definição de ação chama a função de "GetProductID" criada anteriormente:

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

### <a name="http-action"></a>Ação de HTTP

Esta ação envia um pedido para o ponto final especificado e verifica a resposta para determinar se o fluxo de trabalho deve ser executado. 

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

| Value | Type | Descrição | 
|-------|------|-------------| 
| <*method-type*> | String | O método a utilizar para enviar o pedido: "GET", "PUT", "POST", "PATCH" ou "Eliminar" | 
| <*HTTP-or-HTTPS-endpoint-URL*> | String | O HTTP ou HTTPS ponto final para chamar. Tamanho máximo da cadeia de caracteres: 2 KB | 
|||| 

*Opcional*

| Value | Type | Descrição | 
|-------|------|-------------| 
| <*header-content*> | Objeto JSON | Quaisquer cabeçalhos para enviar com o pedido <p>Por exemplo, para definir o idioma e tipo: <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` |
| <*body-content*> | Objeto JSON | Qualquer conteúdo da mensagem a enviar no pedido | 
| <*retry-behavior*> | Objeto JSON | Personaliza o comportamento de repetição para falhas intermitentes, que têm o 408, 429 e código de estado de 5XX e quaisquer exceções de conetividade. Para obter mais informações, consulte [políticas de repetição](../logic-apps/logic-apps-exception-handling.md#retry-policies). | 
| <*query-parameters*> | Objeto JSON | Quaisquer parâmetros de consulta para incluir com o pedido <p>Por exemplo, o `"queries": { "api-version": "2018-01-01" }` adiciona o objeto `?api-version=2018-01-01` para a chamada. | 
| <*other-action-specific-input-properties*> | Objeto JSON | Quaisquer outras propriedades de entrada que se aplicam a esta ação específica | 
| <*other-action-specific-properties*> | Objeto JSON | Outras propriedades que se aplicam a esta ação específica | 
|||| 

*Exemplo*

Esta definição de ação obtém as notícias mais recentes ao enviar um pedido para o ponto final especificado:

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

### <a name="join-action"></a>Junte-se a ação

Esta ação cria uma cadeia de caracteres a partir de todos os itens numa matriz e separa esses itens com o caráter delimitador especificado. 

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
| <*array*> | Array | A matriz ou uma expressão que fornece os itens de origem. Se especificar uma expressão, coloque essa expressão com aspas duplas. | 
| <*delimiter*> | Cadeia de caracteres único | O caráter que separa cada item na cadeia de caracteres | 
|||| 

*Exemplo*

Suponha que tenha uma variável de "myIntegerArray" criado anteriormente que contém esta matriz de número inteiro: 

`[1,2,3,4]` 

Esta definição de ação obtém os valores da variável utilizando o `variables()` numa expressão de função e cria essa cadeia de caracteres com esses valores separados por vírgulas: `"1,2,3,4"`

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

### <a name="parse-json-action"></a>Analisar a ação de JSON

Esta ação cria campos amigáveis ou *tokens* das propriedades no conteúdo JSON. Em seguida, pode acessar essas propriedades na sua aplicação lógica utilizando os tokens em vez disso. Por exemplo, quando deseja usar a saída do JSON de serviços como o Azure Service Bus e Azure Cosmos DB, pode incluir esta ação na sua aplicação lógica, de modo a que pode referenciar mais facilmente os dados que dados de saída. 

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
| <*JSON-source*> | Objeto JSON | O que pretende analisar o conteúdo JSON | 
| <*JSON-schema*> | Objeto JSON | O esquema JSON que descreve o conteúdo JSON, o que a ação utiliza para analisar a origem de conteúdo JSON subjacente. <p>**Sugestão**: No estruturador de aplicações lógicas, pode fornecer o esquema ou fornecer um payload de exemplo, para que a ação pode gerar o esquema. | 
|||| 

*Exemplo*

Esta definição de ação cria estes tokens que pode utilizar em seu fluxo de trabalho de aplicação lógica, mas apenas em ações essa execução seguinte a **Parse JSON** ação: 

`FirstName`, `LastName`, e `Email`

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

Neste exemplo, a propriedade "content" Especifica o conteúdo JSON para a ação analisar. Também pode fornecer este conteúdo JSON como o payload de exemplo para gerar o esquema.

```json
"content": {
   "Member": { 
      "FirstName": "Sophie",
      "LastName": "Owen",
      "Email": "Sophie.Owen@contoso.com"
   }
},
```

A propriedade "schema" Especifica o esquema JSON utilizado para descrever o conteúdo JSON:

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

Esta ação cria uma matriz de itens na outra matriz com base numa condição especificada ou o filtro.

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
| <*array*> | Array | A matriz ou uma expressão que fornece os itens de origem. Se especificar uma expressão, coloque essa expressão com aspas duplas. |
| <*condition-or-filter*> | String | A condição utilizada para filtrar itens na matriz de origem <p>**Nota**: Se não existem valores satisfaçam a condição, em seguida, a ação cria uma matriz vazia. |
|||| 

*Exemplo*

Esta definição de ação cria uma matriz que tem valores maiores que o valor especificado, o que é dois:

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

Esta ação cria o payload de resposta a um pedido HTTP. 

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
| <*response-status-code*> | Número inteiro | O código de estado HTTP que é enviado para a solicitação de entrada. O código de padrão é "200 OK", mas o código pode ser qualquer código de estado válido que comece com 2xx, 4xx ou 5xx, mas não com 3xxx. | 
|||| 

*Opcional*

| Value | Type | Descrição | 
|-------|------|-------------| 
| <*response-headers*> | Objeto JSON | Um ou mais cabeçalhos a incluir com a resposta | 
| <*response-body*> | Vários | O corpo da resposta, que pode ser uma cadeia de caracteres, o objeto JSON ou o conteúdo até mesmo binário a partir de uma ação anterior | 
|||| 

*Exemplo*

Esta definição de ação cria uma resposta a um pedido HTTP com o código de estado especificado, o corpo da mensagem e a cabeçalhos de mensagens:

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

Ao contrário de outras ações, o **resposta** ação tem restrições especiais: 

* O fluxo de trabalho pode utilizar o **resposta** ação apenas quando o fluxo de trabalho é iniciado com um acionador de pedido HTTP, que significa que o fluxo de trabalho tem de ser acionada por um pedido HTTP.

* O fluxo de trabalho pode utilizar o **resposta** ação em qualquer lugar *exceto* dentro **Foreach** loops, **até que** loops, incluindo sequenciais loops, e ramificações em paralelo. 

* A solicitação HTTP original obtém resposta de seu fluxo de trabalho apenas quando todas as ações necessárias pela **resposta** ação concluído dentro do [limite de tempo limite do pedido HTTP](../logic-apps/logic-apps-limits-and-config.md#request-limits).

  No entanto, se o seu fluxo de trabalho chama outra aplicação de lógica como um fluxo de trabalho aninhado, o fluxo de trabalho principal aguarda até que a conclusão do fluxo de trabalho aninhado, não importa quanto tempo passa antes de concluir o fluxo de trabalho aninhado.

* Quando o fluxo de trabalho utiliza o **resposta** ação e um padrão de resposta síncrona, o fluxo de trabalho também não é possível utilizar o **splitOn** comando na definição do acionador porque esse comando cria várias execuções. Verifique neste caso, quando o método PUT é usado e se for VERDADEIRO, devolver uma resposta de "Solicitação incorreta".

  Caso contrário, se o seu fluxo de trabalho utiliza o **splitOn** comando e um **resposta** ação, o fluxo de trabalho é executado de forma assíncrona e retorna imediatamente uma resposta "202 aceite".

* Quando atinge a execução do fluxo de trabalho do **resposta** ação, mas a solicitação de entrada já recebeu uma resposta, o **resposta** ação está marcada como "Falha" devido ao conflito. E, consequentemente, a execução da aplicação lógica também está marcada com estado de "Falhado".

<a name="select-action"></a>

### <a name="select-action"></a>Ação de seleção

Esta ação cria uma matriz com objetos JSON, transformando os itens a partir de outra matriz com base no mapa especificado. A matriz de saída e a matriz de origem sempre têm o mesmo número de itens. Apesar de não é possível alterar o número de objetos da matriz de saída, pode adicionar ou remover propriedades e os respetivos valores entre esses objetos. O `select` propriedade especifica, pelo menos, um par de chave-valor que definem o mapa para transformar itens na matriz de origem. Um par chave-valor representa uma propriedade e o respetivo valor todos os objetos da matriz de saída. 

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
| <*array*> | Array | A matriz ou uma expressão que fornece os itens de origem. Certifique-se de que coloque uma expressão com aspas duplas. <p>**Nota**: Se a matriz de origem estiver vazia, a ação cria uma matriz vazia. | 
| <*key-name*> | String | O nome de propriedade atribuído para o resultado de <*expressão*> <p>Para adicionar uma nova propriedade de todos os objetos da matriz de saída, forneça um <*nome da chave*> para essa propriedade e um <*expressão*> para o valor da propriedade. <p>Para remover uma propriedade de todos os objetos da matriz, omita o <*nome da chave*> para essa propriedade. | 
| <*expression*> | String | A expressão que transforma o item da matriz de origem e atribui o resultado de <*nome da chave*> | 
|||| 

O **selecione** ação cria uma matriz como saída, para que qualquer ação que deseja usar esta saída deve aceitar uma matriz ou tem de converter a matriz para o tipo que aceita a ação de consumidor. Por exemplo, para converter a matriz de saída para uma cadeia de caracteres, pode passar essa matriz para o **Compose** ação e, em seguida, referenciar a saída do **Compose** ação no seu outras ações.

*Exemplo*

Esta definição de ação cria uma matriz de objeto JSON a partir de uma matriz de número inteiro. A ação itera através da matriz de origem, obtém a cada valor de número inteiro utilizando o `@item()` expressão e atribui a cada valor para o "`number`" propriedade em cada objeto JSON: 

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

Segue-se a matriz que esta ação cria:

`[ { "number": 1 }, { "number": 2 }, { "number": 3 } ]`

Para utilizar esta matriz de saída em outras ações, passar a saída para um **Compose** ação:

```json
"Compose": {
   "type": "Compose",
   "inputs": "@body('Select')",
   "runAfter": {
      "Select": [ "Succeeded" ]
   }
},
```

Em seguida, pode utilizar a saída do **Compose** ação em suas outras ações, por exemplo, o **Outlook do Office 365 - enviar um e-mail** ação:

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

### <a name="table-action"></a>Ação de tabela

Esta ação cria uma tabela CSV ou HTML de uma matriz. Para as matrizes com objetos JSON, esta ação cria automaticamente os cabeçalhos de coluna de nomes de propriedades dos objetos. Para as matrizes com outros tipos de dados, tem de especificar os valores e cabeçalhos de coluna. Por exemplo, essa matriz inclui as propriedades de "ID" e "Product_Name" que esta ação pode utilizar para os nomes de cabeçalho de coluna:

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
| < CSV *ou* HTML >| String | O formato para a tabela que pretende criar | 
| <*array*> | Array | A matriz ou uma expressão que fornece os itens de origem para a tabela <p>**Nota**: Se a matriz de origem estiver vazia, a ação cria uma tabela vazia. | 
|||| 

*Opcional*

Para especificar ou personalizar os cabeçalhos de coluna e os valores, utilize o `columns` matriz. Quando `header-value` pares de tem o mesmo nome de cabeçalho, seus valores são apresentados na mesma coluna sob esse nome de cabeçalho. Caso contrário, cada cabeçalho exclusivo define uma única coluna.

| Value | Type | Descrição | 
|-------|------|-------------| 
| <*column-name*> | String | O nome do cabeçalho para uma coluna | 
| <*column-value*> | Qualquer | O valor dessa coluna | 
|||| 

*Exemplo 1*

Suponha que tenha uma variável de "myItemArray" criado anteriormente que atualmente contém essa matriz: 

`[ {"ID": 0, "Product_Name": "Apples"}, {"ID": 1, "Product_Name": "Oranges"} ]`

Esta definição de ação cria uma tabela CSV da variável de "myItemArray". A expressão utilizada pelos `from` propriedade obtém a matriz de "myItemArray" ao utilizar o `variables()` função: 

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

Segue-se a tabela CSV que esta ação cria: 

```
ID,Product_Name 
0,Apples 
1,Oranges 
```

*Exemplo 2*

Esta definição de ação cria uma tabela HTML da variável de "myItemArray". A expressão utilizada pelos `from` propriedade obtém a matriz de "myItemArray" ao utilizar o `variables()` função: 

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

Segue-se a tabela HTML que esta ação cria: 

<table><thead><tr><th>ID</th><th>Product_Name</th></tr></thead><tbody><tr><td>0</td><td>Maçãs</td></tr><tr><td>1</td><td>Laranjas</td></tr></tbody></table>

*Exemplo 3*

Esta definição de ação cria uma tabela HTML da variável de "myItemArray". No entanto, neste exemplo substitui os nomes de cabeçalho de coluna padrão com "Stock_ID" e "Descrição" e adiciona a palavra "Organic" para os valores na coluna "Descrição".

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

Segue-se a tabela HTML que esta ação cria: 

<table><thead><tr><th>Stock_ID</th><th>Descrição</th></tr></thead><tbody><tr><td>0</td><td>Maçãs orgânicas</td></tr><tr><td>1</td><td>Laranjas orgânicas</td></tr></tbody></table>

<a name="terminate-action"></a>

### <a name="terminate-action"></a>Terminar a ação

Esta ação impede a execução para a instância de fluxo de trabalho da aplicação de lógica, cancela todas as ações em curso, ignora todas as restantes ações e devolve o estado especificado. Por exemplo, pode utilizar o **Terminate** ação quando a aplicação lógica deve sair completamente de um Estado de erro. Esta ação não afeta a ações já concluídas e não pode aparecer dentro **Foreach** e **até que** loops, incluindo loops seqüenciais. 

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
| <*status*> | String | O estado a devolver para a execução: "Falha", "Cancelada" ou "Foi concluída com êxito" |
|||| 

*Opcional*

As propriedades para o objeto "runStatus" aplicam-se apenas quando a propriedade "runStatus" está definida para o status de "Falhado".

| Value | Type | Descrição | 
|-------|------|-------------| 
| <*error-code-or-name*> | String | O código ou um nome para o erro |
| <*error-message*> | String | A mensagem ou o texto que descreve o erro e quaisquer ações o utilizador de aplicação pode tirar | 
|||| 

*Exemplo*

Esta definição de ação deixa de uma execução de fluxo de trabalho, define o estado de execução para "Falhado" e devolve o estado, um código de erro e uma mensagem de erro:

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

Esta ação coloca em pausa a execução de fluxo de trabalho para o intervalo especificado, ou até à hora especificada, mas não ambos. 

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

*Período de tempo especificado*

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
| <*number-of-units*> | Número inteiro | Para o **atraso** ação, o número de unidades de espera | 
| <*interval*> | String | Para o **atraso** ação, o intervalo de espera: "Segunda", "Minuto", "Hour", "Day", "Week", "Mês" | 
| <*date-time-stamp*> | String | Para o **atraso até** ação, a data e hora para retomar a execução. Este valor tem de utilizar o [formato de hora UTC data](https://en.wikipedia.org/wiki/Coordinated_Universal_Time). | 
|||| 

*Exemplo 1*

Esta definição de ação interrompe o fluxo de trabalho para 15 minutos:

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

Esta definição de ação interrompe o fluxo de trabalho até o período de tempo especificado:

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

Esta ação chama outra aplicação de lógica criada anteriormente, o que significa que pode incluir e reutilizar outros fluxos de trabalho de aplicação lógica. Também pode utilizar as saídas resultantes o filho ou *aninhada* aplicação lógica nas ações após a aplicação lógica aninhada, desde que a aplicação de lógica de subordinados devolve uma resposta.

O motor do Logic Apps verifica o acesso ao acionador que pretende chamar, por isso, certifique-se de que pode acessar esse acionador. Além disso, a aplicação lógica aninhada tem de cumprir estes critérios:

* Um acionador torna a aplicação lógica aninhada podem ser chamados, por exemplo, um [pedir](#request-trigger) ou [HTTP](#http-trigger) acionador

* A mesma subscrição do Azure que a sua aplicação de lógica principal

* Para utilizar as saídas da aplicação lógica aninhada na sua aplicação de lógica principal, a aplicação lógica aninhada tem de ter uma [resposta](#response-action) ação 

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
| <*nested-logic-app-name*> | String | O nome para a aplicação lógica que pretende chamar | 
| <*trigger-name*> | String | O nome para o acionador na aplicação lógica aninhada deseja chamar | 
| <*Azure-subscription-ID*> | String | O ID de subscrição do Azure para a aplicação lógica aninhada |
| <*Azure-resource-group*> | String | O nome do grupo de recursos do Azure para a aplicação lógica aninhada |
| <*nested-logic-app-name*> | String | O nome para a aplicação lógica que pretende chamar |
||||

*Opcional*

| Value | Type | Descrição | 
|-------|------|-------------|  
| <*header-content*> | Objeto JSON | Quaisquer cabeçalhos para enviar com a chamada | 
| <*body-content*> | Objeto JSON | Qualquer conteúdo da mensagem a enviar com a chamada | 
||||

*Saídas*

Saídas desta ação variam com base na ação de resposta da aplicação lógica aninhada. Se a aplicação lógica aninhada não inclui uma ação de resposta, as saídas estão vazias.

*Exemplo*

Após a conclusão com êxito a ação de "Start_search", esta definição de ação de fluxo de trabalho chama outra aplicação de lógica com o nome "Get_product_information", que passa nas entradas de especificado: 

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

## <a name="control-workflow-action-details"></a>Detalhes de ação de fluxo de trabalho de controle

<a name="foreach-action"></a>

### <a name="foreach-action"></a>Ação de foreach

Esta ação loop itera através de uma matriz e realiza ações em cada item da matriz. Por predefinição, o loop "for each" é executada em paralelo para um número máximo de loops. Para este valor máximo, consulte [limites e configuração](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Saiba mais [como criar "for each" loops](../logic-apps/logic-apps-control-flow-loops.md#foreach-loop).

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
| <*ação-1... n*> | String | Os nomes das ações que são executadas em cada item da matriz | 
| <*action-definition-1...n*> | Objeto JSON | As definições das ações que executar | 
| <*for-each-expression*> | String | A expressão que faz referência a cada item na matriz especificada | 
|||| 

*Opcional*

| Value | Type | Descrição | 
|-------|------|-------------| 
| <*count*> | Número inteiro | Por predefinição, o loop "for each" iterações executam ao mesmo tempo, ou em paralelo, até a [limite predefinido](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Para alterar este limite, definindo uma nova <*contagem*> valor, veja [alterar loop "for each" simultaneidade](#change-for-each-concurrency). | 
| <*operation-option*> | String | Para executar um loop "for each" em seqüência, em vez de em paralelo, defina <*opção de operação*> para `Sequential` ou <*contagem*> para `1`, mas não ambos. Para obter mais informações, consulte [executar "for each" faz um loop sequencialmente](#sequential-for-each). | 
|||| 

*Exemplo*

Esse loop "for each" envia um e-mail para cada item da matriz, que contém anexos num email de entrada. O loop envia uma mensagem de e-mail, incluindo o anexo, para uma pessoa que analisa o anexo.

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

Para especificar apenas uma matriz que é passada como saída do acionador, esta expressão obtém a <*nome da matriz*> matriz do corpo do acionador. Para evitar uma falha se a matriz não existir, a expressão usa o `?` operador:

`@triggerBody()?['<array-name>']` 

<a name="if-action"></a>

### <a name="if-action"></a>Se ação

Esta ação, o que é um *instrução condicional*, avalia uma expressão que representa uma condição e executa um ramo diferente com base em se a condição for verdadeira ou FALSO. Se a condição for verdadeira, a condição é marcada com estado de "Êxito". Saiba mais [como criar instruções condicionais](../logic-apps/logic-apps-control-flow-conditional-statement.md).

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

| Value | Type | Descrição | 
|-------|------|-------------| 
| <*condição*> | Objeto JSON | A condição, o que pode ser uma expressão, para avaliar | 
| <*action-1*> | Objeto JSON | A ação de execução quando <*condição*> avalia como verdadeiro | 
| <*action-definition*> | Objeto JSON | A definição da ação | 
| <*action-2*> | Objeto JSON | A ação de execução quando <*condição*> for avaliada como falsa | 
|||| 

As ações no `actions` ou `else` objetos obtém estes Estados terem:

* "Foi concluída com êxito" depois de executar e concluída com êxito
* "Falha" quando executar e efetuar a ativação
* "Ignorada" quando o respetivo ramo não é executado

*Exemplo*

Esta condição especifica que, quando a variável de número inteiro tem um valor maior que zero, o fluxo de trabalho verifica um Web site. Se a variável for zero ou menos, o fluxo de trabalho verifica a outro Web site.

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

#### <a name="how-conditions-use-expressions"></a>Como condições de utilizam expressões

Aqui estão alguns exemplos que mostram como pode utilizar expressões nas condições:
  
| JSON | Resultado | 
|------|--------| 
| "expression": "@parameters('<*hasSpecialAction*>')" | Para expressões booleanas apenas, a condição transmite para qualquer valor que é avaliada como true. <p>Para converter outros tipos de valor booleano, utilizar estas funções: `empty()` ou `equals()`. | 
| "expression": "@greater(actions('<*action*>').output.value, parameters('<*threshold*>'))" | Para funções de comparação, a ação é executada apenas quando a saída do <*ação*> é mais do que o <*limiar*> valor. | 
| "expression": "@or(greater(actions('<*action*>').output.value, parameters('<*threshold*>')), less(actions('<*same-action*>').output.value, 100))" | Para funções de lógica e a criação de aninhados expressões booleanas, a ação é executada quando a saída do <*ação*> é mais do que o <*limiar*> valor ou em 100. | 
| "expression": "@equals(length(actions('<*action*>').outputs.errors), 0))" | Pode utilizar funções de matriz para verificar se a matriz tem todos os itens. A ação é executada quando o `errors` matriz está vazia. | 
||| 

<a name="scope-action"></a>

### <a name="scope-action"></a>Ação de âmbito

Esta ação agrupa logicamente ações em *âmbitos*, que obter seu próprio Estado após as ações em que o âmbito termine a execução. Em seguida, pode utilizar o estado de âmbito para determinar se executar outras ações. Saiba mais [como criar âmbitos](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md).

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
| <*inner-action-1...n*> | Objeto JSON | Uma ou mais ações que são executados dentro do escopo |
| <*entradas de ação*> | Objeto JSON | As entradas para cada ação |
|||| 

<a name="switch-action"></a>

### <a name="switch-action"></a>Ação de Switch

Esta ação, também conhecida como um *mudar a instrução*, organiza outras ações no *casos*e atribui um valor para cada caso, exceto caso padrão, se existir. Quando o seu fluxo de trabalho é executada, o **comutador** ação compara o valor de uma expressão, objeto ou token contra os valores especificados para cada caso. Se o **comutador** ação localiza um caso de correspondência, o fluxo de trabalho é executado apenas as ações para esse caso. Sempre que o **comutador** execuções de ação, ambos os casos correspondente apenas um existe ou não correspondências existem. Não se existir nenhuma correspondência, o **comutador** ação executa as ações padrão. Saiba mais [como criar declarações do comutador](../logic-apps/logic-apps-control-flow-switch-statement.md).

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
| <*expression-object-or-token*> | Varia | A expressão, o objeto JSON ou o token para avaliar | 
| <*action-name*> | String | O nome da ação a ser executado para o caso de correspondência | 
| <*action-definition*> | Objeto JSON | A definição para a ação de execução para o caso de correspondência | 
| <*matching-value*> | Varia | O valor a comparar com o resultado avaliado | 
|||| 

*Opcional*

| Value | Type | Descrição | 
|-------|------|-------------| 
| <*default-action-name*> | String | O nome da ação predefinida para ser executada quando não existe nenhum caso correspondente | 
| <*default-action-definition*> | Objeto JSON | A definição da ação ser executada quando não existe nenhum caso correspondente | 
|||| 

*Exemplo*

Esta definição de ação avalia se a pessoa a responder para o e-mail de pedido de aprovação selecionado a opção "Aprovar" ou a opção "Rejeitar". Com base nessa opção, o **comutador** ação executa as ações para o caso correspondente, que consiste em Enviar outro e-mail para o dispositivo de resposta, mas com diferentes palavras utilizadas em cada caso. 

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

Esta ação de loop contém ações que executar até que a condição especificada for verdadeira. O loop verifica a condição como última etapa depois de tem de executar todas as outras ações. Pode incluir mais do que uma ação no `"actions"` objeto e a ação tem de definir, pelo menos, um limite. Saiba mais [como criar "loops até"](../logic-apps/logic-apps-control-flow-loops.md#until-loop). 

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
| <*action-name*> | String | O nome para a ação que pretende executar dentro do loop | 
| <*action-type*> | String | O tipo de ação que pretende executar | 
| <*entradas de ação*> | Vários | As entradas para a ação de execução | 
| <*condição*> | String | A condição ou expressão a avaliar Afinal de contas as ações no loop termine a execução | 
| <*loop-count*> | Número inteiro | O limite para o maior número de loops, que pode executar a ação. A predefinição `count` valor é 60. | 
| <*loop-timeout*> | String | O limite de tempo mais longo que pode executar o loop. A predefinição `timeout` é o valor `PT1H`, que é o necessário [formato ISO 8601](https://en.wikipedia.org/wiki/ISO_8601). |
|||| 

*Exemplo*

Esta definição de ação de loop envia um pedido HTTP para o URL especificado, até que uma das seguintes condições seja cumprida: 

* O pedido obtém uma resposta com o "200 OK" código de estado.
* O loop foi executado 60 vezes.
* O loop foi executado durante uma hora.

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

## <a name="webhooks-and-subscriptions"></a>As assinaturas e Webhooks

Ações e acionadores baseados em Webhook regularmente não verificar os pontos finais, mas aguarde eventos específicos ou dados nesses pontos de extremidade em vez disso. Estes acionadores e ações *subscrever* para os pontos de extremidade, fornecendo um *URL de retorno de chamada* onde o ponto final pode enviar as respostas.

O `subscribe` chamada acontece quando o fluxo de trabalho de alterações de qualquer forma, por exemplo, quando as credenciais são renovadas ou quando são alterados os parâmetros de entrada para um acionador ou ação. Esta chamada utiliza os mesmos parâmetros como ações de HTTP padrão. 

O `unsubscribe` chamada ocorre automaticamente quando uma operação faz com que o acionador ou ação inválido, por exemplo:

* A eliminar ou desativar o acionador. 
* A eliminar ou desativar o fluxo de trabalho. 
* Eliminação ou desativação da subscrição. 

Para oferecer suporte a essas chamadas, o `@listCallbackUrl()` expressão retorna um exclusivo "URL de retorno de chamada" para o acionador ou ação. Este URL representa um identificador exclusivo para os pontos finais que utilizar a API de REST do serviço. Os parâmetros para esta função são o mesmo que o acionador de webhook ou uma ação.

<a name="asynchronous-limits"></a>

## <a name="change-asynchronous-duration"></a>Altere a duração assíncrona

Para acionadores e ações, pode limitar a duração para o padrão assíncrono para um intervalo de tempo específico, adicionando o `limit.timeout` propriedade. Dessa forma, se a ação não foi concluída quando o mercado de intervalo, o estado da ação está marcado como `Cancelled` com o `ActionTimedOut` código. O `timeout` propriedade utiliza [formato ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations). 

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

Pode alterar o comportamento de tempo de execução padrão para acionadores e ações com essas `runtimeConfiguration` propriedades na definição do acionador ou ação.

| Propriedade | Tipo | Descrição | Ação ou acionador | 
|----------|------|-------------|-------------------| 
| `runtimeConfiguration.concurrency.runs` | Número inteiro | Alteração da [ *limite predefinido* ](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits) no número de instâncias de aplicações lógicas que podem ser executadas ao mesmo tempo, ou em paralelo. Este valor pode ajudar a limitar o número de pedidos a receber de sistemas back-end. <p>Definir o `runs` propriedade `1` funciona da mesma forma que a definição a `operationOptions` propriedade para `SingleInstance`. Pode definir a propriedade, mas não ambos. <p>Para alterar o limite predefinido, consulte [simultaneidade de Acionador de alteração](#change-trigger-concurrency) ou [acionar instâncias sequencialmente](#sequential-trigger). | Todos os acionadores | 
| `runtimeConfiguration.concurrency.maximumWaitingRuns` | Número inteiro | Alteração da [ *limite predefinido* ](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits) no número de instâncias de aplicações lógicas, que pode aguardar para ser executada quando a aplicação lógica já está a executar o número máximo de instâncias em simultâneo. Pode alterar o limite de simultaneidade no `concurrency.runs` propriedade. <p>Para alterar o limite predefinido, consulte [limitam execuções de espera da alteração](#change-waiting-runs). | Todos os acionadores | 
| `runtimeConfiguration.concurrency.repetitions` | Número inteiro | Alteração da [ *limite predefinido* ](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits) no número de "para cada um" loop iterações que podem ser executadas ao mesmo tempo, ou em paralelo. <p>Definir o `repetitions` propriedade `1` funciona da mesma forma que a definição a `operationOptions` propriedade para `SingleInstance`. Pode definir a propriedade, mas não ambos. <p>Para alterar o limite predefinido, consulte [alterar "for each" simultaneidade](#change-for-each-concurrency) ou [executar "for each" faz um loop sequencialmente](#sequential-for-each). | Ação: <p>[Foreach](#foreach-action) | 
||||| 

<a name="operation-options"></a>

## <a name="operation-options"></a>Opções de operação

Pode alterar o comportamento predefinido para acionadores e ações com o `operationOptions` propriedade na definição de Acionador ou ação.

| Opção de operação | Type | Descrição | Ação ou acionador | 
|------------------|------|-------------|-------------------| 
| `DisableAsyncPattern` | String | Execute ações com base em HTTP de forma síncrona, em vez de assíncrona. <p><p>Para definir esta opção, veja [executar ações de forma síncrona](#asynchronous-patterns). | Ações: <p>[ApiConnection](#apiconnection-action), <br>[HTTP](#http-action), <br>[Resposta](#response-action) | 
| `OptimizedForHighThroughput` | String | Alteração da [limite predefinido](../logic-apps/logic-apps-limits-and-config.md#throughput-limits) o número de execuções de ação por 5 minutos para o [limite máximo](../logic-apps/logic-apps-limits-and-config.md#throughput-limits). <p><p>Para definir esta opção, veja [executado no modo de alto débito](#run-high-throughput-mode). | Todas as ações | 
| `Sequential` | String | Execute "for each" loop iterações um por vez, em vez de todos ao mesmo tempo em paralelo. <p>Esta opção funciona da mesma forma que a definição do `runtimeConfiguration.concurrency.repetitions` propriedade `1`. Pode definir a propriedade, mas não ambos. <p><p>Para definir esta opção, veja [executar "for each" faz um loop sequencialmente](#sequential-for-each).| Ação: <p>[Foreach](#foreach-action) | 
| `SingleInstance` | String | O acionador para cada instância da aplicação lógica são executados sequencialmente e aguarde pela execução anteriormente Active Directory para concluir antes de acionar a instância seguinte da aplicação lógica. <p><p>Esta opção funciona da mesma forma que a definição do `runtimeConfiguration.concurrency.runs` propriedade `1`. Pode definir a propriedade, mas não ambos. <p>Para definir esta opção, veja [acionar instâncias sequencialmente](#sequential-trigger). | Todos os acionadores | 
||||

<a name="change-trigger-concurrency"></a>

### <a name="change-trigger-concurrency"></a>Simultaneidade de Acionador de alteração

Por predefinição, instâncias de aplicações lógicas executadas ao mesmo tempo, em simultâneo, ou em paralelo até a [limite predefinido](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Portanto, cada instância de Acionador é acionado antes da instância anterior da aplicação lógica termina a respetiva execução. Este limite ajuda a controlar o número de pedidos a receber de sistemas back-end. 

Para alterar o limite predefinido, pode utilizar o editor de vista de código ou o estruturador de aplicações lógicas como alterar a definição de simultaneidade por meio do designer adiciona ou atualiza o `runtimeConfiguration.concurrency.runs` propriedade na definição do acionador subjacente e vice-versa. Esta propriedade controla o número máximo de instâncias de aplicações lógicas que podem ser executadas em paralelo. 

> [!NOTE] 
> Se definir o acionador para ser executado em seqüência usando o designer ou o editor de vista de código, não define o acionador `operationOptions` propriedade `SingleInstance` no editor de vista de código. Caso contrário, receberá um erro de validação. Para obter mais informações, consulte [acionar instâncias sequencialmente](#sequential-trigger).

#### <a name="edit-in-code-view"></a>Editar no modo de exibição de código 

No subjacentes definição do acionador, adicionar ou atualizar o `runtimeConfiguration.concurrency.runs` propriedade para um valor entre `1` e `50` , inclusivamente.

Eis um exemplo que limita as execuções simultâneas para 10 instâncias:

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

#### <a name="edit-in-logic-apps-designer"></a>Editar no estruturador de aplicações lógicas

1. No canto superior direito do acionador, escolha o botão de reticências (...) e, em seguida, escolha **definições**.

2. Sob **controlo de simultaneidade**, defina **limite** para **no**. 

3. Arrastar o **grau de paralelismo** controlo de deslize para o valor desejado. Para executar a aplicação lógica em seqüência, arraste o valor de controlo de deslize para **1**.

<a name="change-for-each-concurrency"></a>

### <a name="change-for-each-concurrency"></a>Alterar a simultaneidade "for each"

Por predefinição, o ciclo "for" each iterações executam ao mesmo tempo, ou em paralelo, até a [limite predefinido](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Para alterar o limite predefinido, pode utilizar o editor de vista de código ou o estruturador de aplicações lógicas como alterar a definição de simultaneidade por meio do designer adiciona ou atualiza o `runtimeConfiguration.concurrency.repetitions` propriedade na ação "for each" subjacentes definição e vice-versa. Esta propriedade controla o número máximo de iterações que podem ser executadas em paralelo.

> [!NOTE] 
> Se definir a ação "for each" para ser executado em seqüência usando o designer ou o editor de vista de código, não defina a ação `operationOptions` propriedade `Sequential` no editor de vista de código. Caso contrário, receberá um erro de validação. Para obter mais informações, consulte [executar "for each" faz um loop sequencialmente](#sequential-for-each).

#### <a name="edit-in-code-view"></a>Editar no modo de exibição de código 

Na definição de "for each" subjacentes, adicionar ou atualizar o `runtimeConfiguration.concurrency.repetitions` propriedade para um valor entre `1` e `50` , inclusivamente. 

Eis um exemplo que limita as execuções simultâneas para 10 iterações:

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

#### <a name="edit-in-logic-apps-designer"></a>Editar no estruturador de aplicações lógicas

1. Na **para cada** ação, no canto superior direito, selecione o botão de reticências (...) e, em seguida, escolha **definições**.

2. Sob **controlo de simultaneidade**, defina **controlo de simultaneidade** para **no**. 

3. Arrastar o **grau de paralelismo** controlo de deslize para o valor desejado. Para executar a aplicação lógica em seqüência, arraste o valor de controlo de deslize para **1**.

<a name="change-waiting-runs"></a>

### <a name="change-waiting-runs-limit"></a>Alterar a aguardar o limite de execuções

Por predefinição, instâncias de aplicações lógicas executadas ao mesmo tempo, em simultâneo, ou em paralelo até a [limite predefinido](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Cada instância de Acionador é acionado antes da instância da aplicação lógica anteriormente ativo termina a respetiva execução. Embora possa [alterar este limite predefinido](#change-trigger-concurrency), quando o número de instâncias de aplicação lógica atinge o limite de simultaneidade novo, quaisquer outras novas instâncias têm de aguardar para ser executado. 

O número de execuções que pode aguardar também tem um [limite predefinido](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits), que pode alterar. No entanto, depois que a aplicação lógica atinge o limite de execuções de espera, o motor do Logic Apps aceita já não novas execuções. Os acionadores de pedido e webhook devolvem 429 erros e acionadores recorrentes começar a ignorar tentativas de consulta.

Para alterar o limite predefinido no execuções de espera, em subjacentes definição do acionador, adicione a `runtimeConfiguration.concurency.maximumWaitingRuns` propriedade com um valor entre `0` e `100`. 

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

### <a name="trigger-instances-sequentially"></a>Acionar instâncias sequencialmente

Para executar a lógica de cada instância da aplicação apenas após a conclusão da instância anterior em execução, defina o gatilho para são executados sequencialmente. Pode utilizar o editor de vista de código ou o estruturador de aplicações lógicas, porque também alterar a definição de simultaneidade por meio do designer adiciona ou atualiza o `runtimeConfiguration.concurrency.runs` propriedade na definição do acionador subjacente e vice-versa. 

> [!NOTE] 
> Ao definir um acionador para ser executado em seqüência usando o designer ou o editor de vista de código, não define o acionador `operationOptions` propriedade `Sequential` no editor de vista de código. Caso contrário, receberá um erro de validação. 

#### <a name="edit-in-code-view"></a>Editar no modo de exibição de código

Na definição do acionador, definiu, seja destas propriedades, mas não ambos. 

Definir o `runtimeConfiguration.concurrency.runs` propriedade `1`:

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

*- ou -*

Definir o `operationOptions` propriedade `SingleInstance`:

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

#### <a name="edit-in-logic-apps-designer"></a>Editar no estruturador de aplicações lógicas

1. No canto superior direito do acionador, escolha o botão de reticências (...) e, em seguida, escolha **definições**.

2. Sob **controlo de simultaneidade**, defina **limite** para **no**. 

3. Arrastar o **grau de paralelismo** controlo de deslize para o número `1`. 

<a name="sequential-for-each"></a>

### <a name="run-for-each-loops-sequentially"></a>Execute "for each" faz um loop em sequência

Para executar um loop "for each" iteração apenas após a conclusão da iteração anterior em execução, defina a ação "for each" para são executados sequencialmente. Pode utilizar o editor de vista de código ou o estruturador de aplicações lógicas, porque a alteração de simultaneidade da ação por meio do designer também adiciona ou atualiza o `runtimeConfiguration.concurrency.repetitions` propriedade na definição da ação subjacente e vice-versa. 

> [!NOTE] 
> Ao definir uma ação "for each" para executar sequencialmente através da utilização do designer ou um editor de vista de código, não defina a ação `operationOptions` propriedade `Sequential` no editor de vista de código. Caso contrário, receberá um erro de validação. 

#### <a name="edit-in-code-view"></a>Editar no modo de exibição de código

Na definição da ação, definiu, seja destas propriedades, mas não ambos. 

Definir o `runtimeConfiguration.concurrency.repetitions` propriedade `1`:

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

*- ou -*

Definir o `operationOptions` propriedade `Sequential`:

```json
"For_each" {
   "type": "Foreach",
   "actions": { "<actions-to-run>" },
   "foreach": "<for-each-expression>",
   "runAfter": {},
   "operationOptions": "Sequential"
}
```

#### <a name="edit-in-logic-apps-designer"></a>Editar no estruturador de aplicações lógicas

1. Na **para cada** canto do canto superior direito da ação, escolha o botão de reticências (...) e, em seguida, escolha **definições**.

2. Sob **controlo de simultaneidade**, defina **controlo de simultaneidade** para **no**. 

3. Arrastar o **grau de paralelismo** controlo de deslize para o número `1`. 

<a name="asynchronous-patterns"></a>

### <a name="run-actions-synchronously"></a>Executar ações de forma síncrona

Por predefinição, todas as ações com base em HTTP seguem o padrão de operação assíncrona padrão. Esse padrão Especifica que quando uma ação baseada em HTTP envia um pedido para o ponto final especificado, o servidor remoto envia uma resposta "202 aceite". Esta resposta significa que o servidor aceita o pedido para processamento. O motor do Logic Apps continua verificando o URL especificado pelo cabeçalho de localização da resposta até a paragem do processamento, que é qualquer resposta não 202.

No entanto, os pedidos têm um tempo limite limitar, portanto, para ações de longa execução, pode desativar o comportamento assíncrono adicionando e definindo a `operationOptions` propriedade `DisableAsyncPattern` em entradas da ação.
  
```json
"<some-long-running-action>": {
   "type": "Http",
   "inputs": { "<action-inputs>" },
   "operationOptions": "DisableAsyncPattern",
   "runAfter": {}
}
```

<a name="run-high-throughput-mode"></a>

### <a name="run-in-high-throughput-mode"></a>Executar no modo de alto débito

Para uma única execução da aplicação lógica, o número de ações que executar a cada 5 minutos tem um [limite predefinido](../logic-apps/logic-apps-limits-and-config.md#throughput-limits). Para aumentar este limite para o [máxima](../logic-apps/logic-apps-limits-and-config.md#throughput-limits) possíveis, defina o `operationOptions` propriedade `OptimizedForHighThroughput`. Esta definição coloca a sua aplicação lógica no modo de "débitos". 

> [!NOTE]
> Modo de alto débito está em pré-visualização. Também é possível distribuir uma carga de trabalho em mais do que uma aplicação de lógica, conforme necessário.

```json
"<action-name>": {
   "type": "<action-type>",
   "inputs": { "<action-inputs>" },
   "operationOptions": "OptimizedForHighThroughput",
   "runAfter": {}
}
```

<a name="connector-authentication"></a>

## <a name="authenticate-http-triggers-and-actions"></a>Autenticação HTTP acionadores e ações

Pontos de extremidade HTTP suportam diferentes tipos de autenticação. Pode configurar a autenticação para estas HTTP acionadores e ações:

* [HTTP](../connectors/connectors-native-http.md)
* [HTTP + Swagger](../connectors/connectors-native-http-swagger.md)
* [Webhook de HTTP](../connectors/connectors-native-webhook.md)

Aqui estão os tipos de autenticação, que pode configurar:

* [Autenticação básica](#basic-authentication)
* [Autenticação de certificado de cliente](#client-certificate-authentication)
* [Autenticação do Active Directory (Azure AD) OAuth do Azure](#azure-active-directory-oauth-authentication)

> [!IMPORTANT]
> Certifique-se de que proteger informações confidenciais que lida com a definição de fluxo de trabalho da aplicação lógica. Utilizar parâmetros seguros e codificar dados conforme necessário. Para obter mais informações sobre o uso e a proteção de parâmetros, consulte [proteger a sua aplicação lógica](../logic-apps/logic-apps-securing-a-logic-app.md#secure-action-parameters).

<a name="basic-authentication"></a>

### <a name="basic-authentication"></a>Autenticação básica

Para [autenticação básica](../active-directory-b2c/active-directory-b2c-custom-rest-api-netfw-secure-basic.md) ao utilizar o Azure Active Directory, a definição de Acionador ou ação pode incluir um `authentication` objeto JSON, que tem as propriedades especificadas pela tabela seguinte. Para aceder a valores de parâmetro em tempo de execução, pode utilizar o `@parameters('parameterName')` expressão, que é fornecido pela [linguagem de definição de fluxo de trabalho](https://aka.ms/logicappsdocs). 

| Propriedade | Necessário | Value | Descrição | 
|----------|----------|-------|-------------| 
| **tipo** | Sim | "Básico" | O tipo de autenticação a utilizar, que é "Básico" aqui | 
| **username** | Sim | "@parameters('userNameParam')" | O nome de utilizador para autenticar o acesso para o ponto de extremidade do serviço de destino |
| **password** | Sim | "@parameters('passwordParam')" | A palavra-passe para autenticar o acesso para o ponto de extremidade do serviço de destino |
||||| 

Neste exemplo de definição da ação de HTTP, o `authentication` secção especifica `Basic` autenticação. Para obter mais informações sobre o uso e a proteção de parâmetros, consulte [proteger a sua aplicação lógica](../logic-apps/logic-apps-securing-a-logic-app.md#secure-action-parameters).

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
> Certifique-se de que proteger informações confidenciais que lida com a definição de fluxo de trabalho da aplicação lógica. Utilizar parâmetros seguros e codificar dados conforme necessário. Para obter mais informações sobre como proteger a parâmetros, consulte [proteger a sua aplicação lógica](../logic-apps/logic-apps-securing-a-logic-app.md#secure-action-parameters).

<a name="client-certificate-authentication"></a>

### <a name="client-certificate-authentication"></a>Autenticação de certificado de cliente

Para [autenticação baseada em certificado](../active-directory/authentication/active-directory-certificate-based-authentication-get-started.md) com o Azure Active Directory, a definição de Acionador ou ação pode incluir um `authentication` objeto JSON, que tem as propriedades especificadas pela tabela seguinte. Para aceder a valores de parâmetro em tempo de execução, pode utilizar o `@parameters('parameterName')` expressão, que é fornecido pela [linguagem de definição de fluxo de trabalho](https://aka.ms/logicappsdocs). Para limites no número de certificados de cliente pode utilizar, consulte [limites e configuração para o Azure Logic Apps](../logic-apps/logic-apps-limits-and-config.md).

| Propriedade | Necessário | Value | Descrição |
|----------|----------|-------|-------------|
| **tipo** | Sim | "ClientCertificate" | O tipo de autenticação a utilizar Secure Sockets Layer (SSL) para certificados de cliente. Embora sejam suportados certificados autoassinados, não são suportados certificados autoassinados para SSL. |
| **pfx** | Sim | "@parameters('pfxParam') | O conteúdo codificado em base64 a partir de um ficheiro Personal Information Exchange (PFX) |
| **password** | Sim | "@parameters('passwordParam')" | A palavra-passe para aceder ao ficheiro PFX |
||||| 

Neste exemplo de definição da ação de HTTP, o `authentication` secção especifica `ClientCertificate` autenticação. Para obter mais informações sobre o uso e a proteção de parâmetros, consulte [proteger a sua aplicação lógica](../logic-apps/logic-apps-securing-a-logic-app.md#secure-action-parameters).

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
> Certifique-se de que proteger informações confidenciais que lida com a definição de fluxo de trabalho da aplicação lógica. Utilizar parâmetros seguros e codificar dados conforme necessário. Para obter mais informações sobre como proteger a parâmetros, consulte [proteger a sua aplicação lógica](../logic-apps/logic-apps-securing-a-logic-app.md#secure-action-parameters).

<a name="azure-active-directory-oauth-authentication"></a>

### <a name="azure-active-directory-ad-oauth-authentication"></a>Autenticação do Active Directory (AD) OAuth do Azure

Para [autenticação de OAuth do Azure AD](../active-directory/develop/authentication-scenarios.md), a definição de Acionador ou ação pode incluir um `authentication` objeto JSON, que tem as propriedades especificadas pela tabela seguinte. Para aceder a valores de parâmetro em tempo de execução, pode utilizar o `@parameters('parameterName')` expressão, que é fornecido pela [linguagem de definição de fluxo de trabalho](https://aka.ms/logicappsdocs).

| Propriedade | Necessário | Value | Descrição |
|----------|----------|-------|-------------|
| **tipo** | Sim | `ActiveDirectoryOAuth` | O tipo de autenticação a utilizar, que é "ActiveDirectoryOAuth" para o OAuth do Azure AD |
| **authority** | Não | <*URL-for-authority-token-issuer*> | O URL para a autoridade que fornece o token de autenticação |
| **tenant** | Sim | <*tenant-ID*> | O ID de inquilino para o inquilino do Azure AD |
| **Público-alvo** | Sim | <*resource-to-authorize*> | O recurso que pretende utilizar para autorização, por exemplo, `https://management.core.windows.net/` |
| **clientId** | Sim | <*client-ID*> | O ID de cliente para a aplicação a solicitar autorização |
| **credentialType** | Sim | "Certificate" ou "Segredo" | Utiliza o tipo de credencial do cliente para solicitar autorização. Esta propriedade e valor não aparecem na sua definição subjacente, mas determina os parâmetros necessários para o tipo de credencial. |
| **pfx** | Sim, apenas para o tipo de credencial "Certificate" | "@parameters('pfxParam') | O conteúdo codificado em base64 a partir de um ficheiro Personal Information Exchange (PFX) |
| **password** | Sim, apenas para o tipo de credencial "Certificate" | "@parameters('passwordParam')" | A palavra-passe para aceder ao ficheiro PFX |
| **secret** | Sim, apenas para o "Segredo" tipo de credencial | "@parameters('secretParam')" | O segredo do cliente para solicitar autorização |
|||||

Neste exemplo de definição da ação de HTTP, o `authentication` secção especifica `ActiveDirectoryOAuth` tipo de credencial de autenticação e o "segredo". Para obter mais informações sobre o uso e a proteção de parâmetros, consulte [proteger a sua aplicação lógica](../logic-apps/logic-apps-securing-a-logic-app.md#secure-action-parameters).

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
> Certifique-se de que proteger informações confidenciais que lida com a definição de fluxo de trabalho da aplicação lógica. Utilizar parâmetros seguros e codificar dados conforme necessário. Para obter mais informações sobre como proteger a parâmetros, consulte [proteger a sua aplicação lógica](../logic-apps/logic-apps-securing-a-logic-app.md#secure-action-parameters).

## <a name="next-steps"></a>Passos Seguintes

* Saiba mais sobre [linguagem de definição de fluxo de trabalho](../logic-apps/logic-apps-workflow-definition-language.md)
