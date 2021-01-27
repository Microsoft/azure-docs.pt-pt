---
title: incluir ficheiro
description: incluir ficheiro
services: service-bus-messaging, event-hubs
author: spelluru
ms.service: service-bus-messaging, event-hubs
ms.topic: include
ms.date: 12/12/2020
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 9bc641d680d927c44814f6814ebf6a6dde958c9e
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/27/2021
ms.locfileid: "98901132"
---
As Funções Azure permitem criar tarefas de replicação apenas de configuração que se apoiem num ponto de entrada pré-construído. As [amostras de replicação baseadas na configuração para funções Azure](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/config) ilustram como alavancar [os ajudantes pré-construídos](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/src/Azure.Messaging.Replication) no seu próprio código ou evitar manusear completamente o código e apenas usar a configuração.

## <a name="create-a-replication-task"></a>Criar uma tarefa de replicação
Para criar tal tarefa de replicação, primeiro crie uma nova pasta por baixo da pasta do projeto. O nome da nova pasta é o nome da função, por exemplo `europeToAsia` ou `telemetry` . O nome não tem qualquer relação direta com as entidades de mensagens que estão a ser utilizadas e serve apenas para que as identifique. Pode criar dezenas de funções no mesmo projeto.

Em seguida, crie um `function.json` ficheiro na pasta. O ficheiro configura a função. Comece com o seguinte conteúdo:

``` JSON
{
    "configurationSource": "config",
    "bindings" : [
        {
            "direction": "in",
            "name": "input" 
        },
        {
            "direction": "out",
            "name": "output"
        }
    ],
    "retry": {
        "strategy": "exponentialBackoff",
        "maxRetryCount": -1,
        "minimumInterval": "00:00:05",
        "maximumInterval": "00:05:00"
    },
    "disabled": false,
    "scriptFile": "../bin/Azure.Messaging.Replication.dll",
    "entryPoint": "Azure.Messaging.Replication.*"
}
```

Nesse ficheiro, é necessário completar três etapas de configuração que dependem das entidades que pretende ligar:

1. [Configure a direção de entrada](#configure-the-input-direction)
2. [Configure a direção de saída](#configure-the-output-direction)
3. [Configure o ponto de entrada](#configure-the-entry-point)

### <a name="configure-the-input-direction"></a>Configure a direção de entrada

#### <a name="event-hub-input"></a>Entrada do Centro de Eventos

Se quiser receber eventos a partir de um Centro de Eventos, adicione informações de configuração à secção superior dentro de "encadernações" que define

* **tipo** - o tipo "eventHubTrigger".
* **conexão** - o nome do valor das definições de aplicação para a cadeia de ligação Event Hub. 
* **eventHubName** - o nome do Event Hub dentro do espaço de nome identificado pela cadeia de ligação.
* **consumerGroup** - o nome do grupo de consumidores. Tenha em conta que o nome está incluído em sinais por cento e, portanto, também se refere a um valor de definições de aplicação.

```JSON
    ...
    "bindings" : [
        {
            "direction": "in",
            "type": "eventHubTrigger",
            "connection": "functionname-source-connection",
            "eventHubName": "eventHubA",
            "consumerGroup" : "%functionname-source-consumergroup%",
            "name": "input" 
        }
    ...
```

#### <a name="service-bus-queue-input"></a>Entrada da fila do ônibus de serviço

Se quiser receber eventos a partir de uma fila de ônibus de serviço, adicione informações de configuração à secção superior dentro de "encadernações" que define

* **tipo** - o tipo "serviceBusTrigger".
* **conexão** - o nome do valor das definições de aplicação para a cadeia de ligação Service Bus.
* **queueName** - o nome da fila de autocarros de serviço dentro do espaço de nome identificado pela cadeia de ligação.

```JSON
    ...
    "bindings" : [
        {
            "direction": "in",
            "type": "serviceBusTrigger",
            "connection": "functionname-source-connection",
            "queueName": "queue-a",
            "name": "input" 
        }
    ...
```

#### <a name="service-bus-topic-input"></a>Entrada tópico de ônibus de serviço

Se quiser receber eventos a partir de um tópico de Service Bus, adicione informações de configuração à secção superior dentro de "encadernações" que define

* **tipo** - o tipo "serviceBusTrigger".
* **conexão** - o nome do valor das definições de aplicação para a cadeia de ligação Service Bus. Este valor deve ser `{FunctionName}-source-connection` se quiser utilizar os scripts fornecidos.
* **temaName** - o nome do Tópico de Autocarro de Serviço dentro do espaço de nome identificado pela cadeia de ligação.
* **nome de subscrição** - o nome da Assinatura de Autocarro de Serviço no tópico dado dentro do espaço de nome identificado pela cadeia de ligação.

```JSON
    ...
    "bindings" : [
        {
            "direction": "in",
            "type": "serviceBusTrigger",
            "connection": "functionname-source-connection",
            "topicName": "topic-x",
            "subscriptionName" : "sub-y",
            "name": "input" 
        }
    ...
```

### <a name="configure-the-output-direction"></a>Configure a direção de saída

#### <a name="event-hub-output"></a>Saída do Centro de Eventos

Se pretender encaminhar eventos para um Centro de Eventos, adicione informações de configuração à secção inferior dentro de "encadernações" que define

* **tipo** - o tipo "eventHub".
* **conexão** - o nome do valor das definições de aplicação para a cadeia de ligação Event Hub. 
* **eventHubName** - o nome do Event Hub dentro do espaço de nome identificado pela cadeia de ligação.

```JSON
    ...
    "bindings" : [
        {
            ...
        },
        {
            "direction": "out",
            "type": "eventHub",
            "connection": "functionname-target-connection",
            "eventHubName": "eventHubB",
            "name": "output" 
        }
    ...
```

#### <a name="service-bus-queue-output"></a>Saída da fila do autocarro de serviço

Se pretender encaminhar eventos para uma fila de autocarros de serviço, adicione informações de configuração à secção inferior dentro de "encadernações" que define

* **tipo** - o tipo "serviceBus".
* **conexão** - o nome do valor das definições de aplicação para a cadeia de ligação Service Bus. 
* **queueName** - o nome da fila do Service Bus dentro do espaço de nome identificado pela cadeia de ligação.

```JSON
    ...
    "bindings" : [
        {
            ...
        },
        {
            "direction": "out",
            "type": "serviceBus",
            "connection": "functionname-target-connection",
            "queueName": "queue-b",
            "name": "output" 
        }
    ...
```

#### <a name="service-bus-topic-output"></a>Saída de tópico de ônibus de serviço

Se pretender encaminhar eventos para um Tópico de Autocarro de Serviço, adicione informações de configuração à secção inferior dentro de "encadernações" que define

* **tipo** - o tipo "serviceBus".
* **conexão** - o nome do valor das definições de aplicação para a cadeia de ligação Service Bus. 
* **temaName** - o nome do tópico Service Bus dentro do espaço de nome identificado pela cadeia de ligação.

```JSON
    ...
    "bindings" : [
        {
            ...
        },
        {
            "direction": "out",
            "type": "serviceBus",
            "connection": "functionname-target-connection",
            "topicName": "topic-b",
            "name": "output" 
        }
    ...
```

### <a name="configure-the-entry-point"></a>Configure o ponto de entrada

A configuração do ponto de entrada escolhe uma das tarefas padrão de replicação. Se estiver a modificar o `Azure.Messaging.Replication` projeto, também pode adicionar tarefas e encaminhá-las para cá. Por exemplo:

```JSON
    ...
    "scriptFile": "../dotnet/bin/Azure.Messaging.Replication.dll",
    "entryPoint": "Azure.Messaging.Replication.EventHubReplicationTasks.ForwardToEventHub"
    ...
```

O quadro a seguir dá-lhe os valores corretos para combinações de fontes e alvos:

| Origem      | Destino      | Ponto de entrada 
|-------------|-------------|------------------------------------------------------------------------
| Hub de Eventos   | Hub de Eventos   | `Azure.Messaging.Replication.EventHubReplicationTasks.ForwardToEventHub`
| Hub de Eventos   | Service Bus | `Azure.Messaging.Replication.EventHubReplicationTasks.ForwardToServiceBus`
| Service Bus | Hub de Eventos   | `Azure.Messaging.Replication.ServiceBusReplicationTasks.ForwardToEventHub`
| Service Bus | Service Bus | `Azure.Messaging.Replication.ServiceBusReplicationTasks.ForwardToServiceBus`

### <a name="retry-policy"></a>Política de Repetição

Consulte a documentação do [Azure Functions sobre as retrações](../articles/azure-functions/functions-bindings-error-pages.md) para configurar a política de retíria. As definições políticas escolhidas ao longo dos projetos neste repositório configuram uma estratégia exponencial de backoff com intervalos de retry de 5 segundos a 5 minutos com infinitas retrações para evitar a perda de dados.

Para o Service Bus, reveja a secção "usar o [suporte de repreensão em cima da resiliência do gatilho"](../articles/azure-functions/functions-bindings-error-pages.md#using-retry-support-on-top-of-trigger-resilience) para entender a interação dos gatilhos e a contagem máxima de entrega definida para a fila.

### <a name="build-deploy-and-configure"></a>Construir, implantar e configurar

Enquanto estiver focado na configuração, as tarefas ainda requerem a construção de uma aplicação implantável e para configurar os anfitriões das Funções Azure de modo a que tenha todas as informações necessárias para se ligar aos pontos finais determinados. 

Isto é ilustrado, juntamente com scripts reutilizáveis, nas [amostras de replicação baseadas na configuração para funções Azure](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/config).