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
ms.openlocfilehash: 1ce983ee739a4a124a93c7913f092b23dfec3cbd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98901133"
---
## <a name="what-is-a-replication-task"></a>O que é uma tarefa de replicação?

Uma tarefa de replicação recebe eventos de uma fonte e encaminha-os para um alvo.
A maioria das tarefas de replicação irá encaminhar eventos inalterados e, no máximo, executar mapeamento entre estruturas de metadados se os protocolos de origem e alvo diferirem. 

As tarefas de replicação são geralmente apátridas, o que significa que não partilham efeitos estatais ou secundários através de execuções sequenciais ou paralelas de uma tarefa. Isso também se aplica ao loteamento e à corrente, que ambos podem ser implementados em cima do estado existente de um fluxo. 

Isto torna as tarefas de replicação diferentes das tarefas de agregação, que são geralmente imponentes, e são o domínio de quadros e serviços de análise como [o Azure Stream Analytics](../articles/stream-analytics/stream-analytics-introduction.md).

## <a name="replication-applications-and-tasks-in-azure-functions"></a>Aplicações e tarefas de replicação em Funções Azure

Em Funções Azure, uma tarefa de replicação é implementada usando um [gatilho](../articles/azure-functions/functions-triggers-bindings.md) que adquire uma ou mais mensagem de entrada de uma fonte configurada e uma [ligação de saída](../articles/azure-functions/functions-triggers-bindings.md#binding-direction) que reencaminha mensagens copiadas da fonte para um alvo configurado. 

| Acionador  | Saída |
|----------|--------|
| [Azure Event Hubs gatilho](../articles/azure-functions/functions-bindings-event-hubs-trigger.md?tabs=csharp) | [Azure Event hubs de produção](../articles/azure-functions/functions-bindings-event-hubs-output.md?tabs=csharp) |
| [Gatilho do ônibus de serviço Azure](../articles/azure-functions/functions-bindings-service-bus-trigger.md?tabs=csharp) | [Vinculação de saída do autocarro da Azure Service](../articles/azure-functions/functions-bindings-service-bus-output.md?tabs=csharp)
| [Gatilho do hub Azure IoT](../articles/azure-functions/functions-bindings-event-iot-trigger.md?tabs=csharp) | [Ligação de saída do Azure IoT Hub](../articles/azure-functions/functions-bindings-event-iot-output.md?tabs=csharp)
| [Gatilho da grelha de eventos Azure](../articles/azure-functions/functions-bindings-event-grid-trigger.md?tabs=csharp) | [Ligação de saída da grelha de eventos Azure](../articles/azure-functions/functions-bindings-event-grid-output.md?tabs=csharp)
| [Acionador do Armazenamento de Filas do Azure](../articles/azure-functions/functions-bindings-storage-queue-trigger.md?tabs=csharp) | [Ligação de saída de armazenamento de fila Azure](../articles/azure-functions/functions-bindings-storage-queue-output.md?tabs=csharp)
| [Gatilho Apache Kafka](https://github.com/azure/azure-functions-kafka-extension) | [Vinculação de saída Apache Kafka](https://github.com/azure/azure-functions-kafka-extension)
| [Gatilho RabbitMQ](https://github.com/azure/azure-functions-rabbitmq-extension) | [Ligação de saída RabbitMQ](https://github.com/azure/azure-functions-rabbitmq-extension) 
| | [Ligação de saída dos Hubs de Notificação Azure](../articles/azure-functions/functions-bindings-notification-hubs.md)
||[Vinculação de saída do serviço Azure SignalR](../articles/azure-functions/functions-bindings-signalr-service-output.md?tabs=csharp)
||[Ligação de saída Twilio SendGrid](../articles/azure-functions/functions-bindings-sendgrid.md?tabs=csharp)

As tarefas de replicação são implementadas como na aplicação de replicação através dos mesmos métodos de implementação que qualquer outra aplicação de Funções Azure. Pode configurar várias tarefas na mesma aplicação. 

Com o Azure Functions Premium, várias aplicações de replicação podem partilhar o mesmo conjunto de recursos subjacente, chamado Plano de Serviço de Aplicações. Isto significa que pode facilmente colocar tarefas de replicação escritas em .NET com tarefas de replicação que são escritas em Java, por exemplo. Isso importará se quiser tirar partido de bibliotecas específicas, como o Apache Camel, que só estão disponíveis para Java e se essas são a melhor opção para um determinado caminho de integração, mesmo que prefira uma linguagem e tempo de execução diferentes para si outras tarefas de replicação. 

Sempre que disponível, deve preferir os gatilhos orientados para o lote em vez de gatilhos que entregam eventos ou mensagens individuais e deve sempre obter a estrutura completa do evento ou da mensagem em vez de confiar nas [expressões de ligação](../articles/azure-functions/functions-bindings-expressions-patterns.md)de parâmetros da Azure Function .

O nome da função deve refletir o par de origem e alvo que está a ligar, e deve prefixar referências a cadeias de ligação ou outros elementos de configuração nos ficheiros de configuração da aplicação com esse nome. 

### <a name="data-and-metadata-mapping"></a>Mapeamento de dados e metadados

Uma vez decidido um par de entradas de gatilho e ligação de saída, terá de realizar algum mapeamento entre os diferentes tipos de evento ou mensagem, a menos que o tipo do seu gatilho e a saída sejam os mesmos.

Para tarefas simples de replicação que copiam mensagens entre Os Centros de Eventos e o Service Bus, não tem de escrever o seu próprio código, mas pode apoiar-se numa [biblioteca de serviços públicos que é fornecida](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/src/Azure.Messaging.Replication) com as amostras de replicação.

### <a name="retry-policy"></a>Política de Repetição

Para evitar a perda de dados durante o evento de disponibilidade em ambos os lados de uma função de replicação, é necessário configurar a política de reagem para ser robusto. Consulte a documentação do [Azure Functions sobre as retrações](../articles/azure-functions/functions-bindings-error-pages.md) para configurar a política de retíria. 

As definições de política escolhidas para o exemplo projetam no [repositório](https://github.com/Azure-Samples/azure-messaging-replication-dotnet) de amostras configurar uma estratégia exponencial de backoff com intervalos de retry de 5 segundos a 15 minutos com infinitas retrações para evitar a perda de dados. 

Para o Service Bus, reveja a secção "usar o [suporte de repreensão em cima da resiliência do gatilho"](../articles/azure-functions/functions-bindings-error-pages.md#using-retry-support-on-top-of-trigger-resilience) para entender a interação dos gatilhos e a contagem máxima de entrega definida para a fila.

### <a name="setting-up-a-replication-application-host"></a>Criação de um anfitrião de aplicação de replicação

Uma aplicação de replicação é um anfitrião de execução para uma ou mais tarefas de replicação. 

É uma aplicação Azure Functions que está configurada para executar no plano de consumo ou (recomendado) num plano Azure Functions Premium. Todas as aplicações de replicação devem ser executadas sob uma [identidade gerida atribuída pelo sistema ou pelo utilizador.](../articles/app-service/overview-managed-identity.md) 

Os modelos ligados do Azure Resource Manager (ARM) criam e configuram uma aplicação de replicação com:

* uma conta de armazenamento Azure para rastrear o progresso da replicação e para os registos,
* uma identidade gerida pelo sistema, e 
* Integração de Azure Monitoring e Application Insights para monitorização.

As aplicações de replicação que devem aceder aos Centros de Eventos ligados a uma rede virtual Azure (VNet) devem utilizar o plano Azure Functions Premium e ser configuradas para se anexarem ao mesmo VNet, que é também uma das opções disponíveis.


|       | Implementar | Visualizar  
|-------|------------------|--------------|---------------|
| **Plano de Consumo de Funções Azure** | [![Implementar no Azure](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.svg?sanitize=true)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-messaging-replication-dotnet%2Fmain%2Ftemplates%2FAconsumption%2Fazuredeploy.json)|[![Visualizar](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/visualizebutton.svg?sanitize=true)](http://armviz.io/#/?load=https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-messaging-replication-dotnet%2Fmain%2Ftemplates%2Fconsumption%2Fazuredeploy.json)
| **Plano Premium de Funções Azure** |[![Implementar no Azure](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.svg?sanitize=true)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-messaging-replication-dotnet%2Fmain%2Ftemplates%2Fpremium%2Fazuredeploy.json) | [![Visualizar](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/visualizebutton.svg?sanitize=true)](http://armviz.io/#/?load=https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-messaging-replication-dotnet%2Fmain%2Ftemplates%2Fpremium%2Fazuredeploy.json)
| **Plano Premium de Funções Azure com VNet** | [![Implementar no Azure](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.svg?sanitize=true)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-messaging-replication-dotnet%2Fmain%2Ftemplates%2Fpremium-vnet%2Fazuredeploy.json)|[![Visualizar](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/visualizebutton.svg?sanitize=true)](http://armviz.io/#/?load=https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-messaging-replication-dotnet%2Fmain%2Ftemplates%2Fpremium-vnet%2Fazuredeploy.json)


### <a name="examples"></a>Exemplos

O [repositório](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/) de amostras contém vários exemplos de tarefas de replicação que copiam eventos entre centros de eventos e/ou entre entidades de Service Bus.

Para copiar evento entre Centros de Eventos, utilize um Event Hub Trigger com uma ligação de saída do Event Hub:

```csharp
[FunctionName("telemetry")]
[ExponentialBackoffRetry(-1, "00:00:05", "00:05:00")]
public static Task Telemetry(
    [EventHubTrigger("telemetry", ConsumerGroup = "$USER_FUNCTIONS_APP_NAME.telemetry", Connection = "telemetry-source-connection")] EventData[] input,
    [EventHub("telemetry-copy", Connection = "telemetry-target-connection")] EventHubClient outputClient,
    ILogger log)
{
    return EventHubReplicationTasks.ForwardToEventHub(input, outputClient, log);
}
```

Para copiar mensagens entre entidades do Service Bus, utilize o gatilho do Service Bus e a ligação de saída:

```csharp
[FunctionName("jobs-transfer")]
[ExponentialBackoffRetry(-1, "00:00:05", "00:05:00")]
public static Task JobsTransfer(
    [ServiceBusTrigger("jobs-transfer", Connection = "jobs-transfer-source-connection")] Message[] input,
    [ServiceBus("jobs", Connection = "jobs-target-connection")] IAsyncCollector<Message> output,
    ILogger log)
{
    return ServiceBusReplicationTasks.ForwardToServiceBus(input, output, log);
}
```

Os métodos de ajuda podem facilitar a replicação entre os Centros de Eventos e o Service Bus:

| Origem      | Destino      | Ponto de entrada 
|-------------|-------------|------------------------------------------------------------------------
| Hub de Eventos   | Hub de Eventos   | `Azure.Messaging.Replication.EventHubReplicationTasks.ForwardToEventHub`
| Hub de Eventos   | Service Bus | `Azure.Messaging.Replication.EventHubReplicationTasks.ForwardToServiceBus`
| Service Bus | Hub de Eventos   | `Azure.Messaging.Replication.ServiceBusReplicationTasks.ForwardToEventHub`
| Service Bus | Service Bus | `Azure.Messaging.Replication.ServiceBusReplicationTasks.ForwardToServiceBus`


### <a name="monitoring"></a>Monitorização

Para saber como pode monitorizar a sua aplicação de replicação, consulte a secção de [monitorização](../articles/azure-functions/configure-monitoring.md) da documentação Azure Functions.

Uma ferramenta visual particularmente útil para monitorizar tarefas de replicação é o Mapa de [Aplicações](../articles/azure-monitor/app/app-map.md)Insights , que é gerado automaticamente a partir das informações de monitorização capturadas e permite explorar a fiabilidade e desempenho da fonte de tarefa de replicação e transferências de alvo.

Para obter informações imediatas de diagnóstico, pode trabalhar com a ferramenta do portal [Live Metrics,](../articles/azure-monitor/app/live-stream.md) que fornece uma visualização de baixa latência dos detalhes do registo.

## <a name="next-steps"></a>Passos seguintes

* [Implementações de funções Azure](../articles/azure-functions/functions-deployment-technologies.md)
* [Diagnóstico de Funções Azure](../articles/azure-functions/functions-diagnostics.md)
* [Opções de networking de funções Azure](../articles/azure-functions/functions-networking-options.md)
* [Insights de Aplicação Azure](../articles/azure-monitor/app/app-insights-overview.md)