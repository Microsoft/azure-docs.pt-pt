---
title: Desempenho e escala no Durable Functions-Azure
description: Introdução à extensão de Durable Functions para Azure Functions.
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 03/14/2019
ms.author: azfuncdf
ms.openlocfilehash: ed0fe22903412d4164fb3a85dbd9afafdc7023e6
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70098007"
---
# <a name="performance-and-scale-in-durable-functions-azure-functions"></a>Desempenho e escala em Durable Functions (Azure Functions)

Para otimizar o desempenho e a escalabilidade, é importante entender as características exclusivas de dimensionamento de [Durable Functions](durable-functions-overview.md).

Para entender o comportamento da escala, você precisa entender alguns dos detalhes do provedor de armazenamento do Azure subjacente.

## <a name="history-table"></a>Tabela de histórico

A tabela de **histórico** é uma tabela de armazenamento do Azure que contém os eventos de histórico de todas as instâncias de orquestração em um hub de tarefas. O nome desta tabela está no formato *TaskHubName*histórico. Conforme as instâncias são executadas, novas linhas são adicionadas a essa tabela. A chave de partição dessa tabela é derivada da ID da instância da orquestração. Uma ID de instância é aleatória na maioria dos casos, o que garante a distribuição ideal de partições internas no armazenamento do Azure.

Quando uma instância de orquestração precisa ser executada, as linhas apropriadas da tabela de histórico são carregadas na memória. Esses *eventos de histórico* são então reproduzidos no código da função de orquestrador para que ele volte ao seu estado de ponto de verificação anterior. O uso do histórico de execução para recriar o estado dessa maneira é influenciado pelo [padrão de fornecimento do evento](https://docs.microsoft.com/azure/architecture/patterns/event-sourcing).

## <a name="instances-table"></a>Tabela de instâncias

A tabela de **instâncias** é outra tabela de armazenamento do Azure que contém os status de todas as instâncias de orquestração em um hub de tarefas. Conforme as instâncias são criadas, novas linhas são adicionadas a essa tabela. A chave de partição desta tabela é a ID da instância de orquestração e a chave de linha é uma constante fixa. Há uma linha por instância de orquestração.

Essa tabela é usada para satisfazer solicitações de consulta de instância das APIs [GetStatusAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_GetStatusAsync_System_String_) (.NET `getStatus` ) e (JavaScript), bem como a [API http de consulta de status](durable-functions-http-api.md#get-instance-status). Ele é mantido eventualmente consistente com o conteúdo da tabela de **histórico** mencionado anteriormente. O uso de uma tabela de armazenamento do Azure separada para satisfazer com eficiência as operações de consulta de instância dessa maneira é influenciada pelo [padrão de separação das operações de comando e de consulta (CQRS)](https://docs.microsoft.com/azure/architecture/patterns/cqrs).

## <a name="internal-queue-triggers"></a>Gatilhos de fila interna

Funções de orquestrador e funções de atividade são disparadas por filas internas no Hub de tarefas do aplicativo de funções. O uso de filas dessa maneira fornece garantias de entrega de mensagens "ao menos uma vez" confiáveis. Há dois tipos de filas no Durable Functions: a fila de **controle** e a **fila de itens de trabalho**.

### <a name="the-work-item-queue"></a>A fila de itens de trabalho

Há uma fila de item de trabalho por Hub de tarefa em Durable Functions. É uma fila básica e se comporta de forma semelhante a qualquer outra `queueTrigger` fila em Azure functions. Essa fila é usada para disparar *funções de atividade* sem monitoração de estado por meio da remoção da fila de uma única mensagem por vez. Cada uma dessas mensagens contém entradas de função de atividade e metadados adicionais, como a função a ser executada. Quando um aplicativo Durable Functions é dimensionado para várias VMs, todas essas VMs concorrem para adquirir o trabalho da fila de itens de trabalho.

### <a name="control-queues"></a>Fila (s) de controle

Há várias *filas de controle* por Hub de tarefas em Durable functions. Uma *fila de controle* é mais sofisticada do que a fila de itens de trabalho mais simples. Filas de controle são usadas para disparar as funções de orquestrador com estado. Como as instâncias de função do orquestrador são singletons com estado, não é possível usar um modelo de consumidor concorrente para distribuir carga entre VMs. Em vez disso, as mensagens do Orchestrator têm balanceamento de carga entre as filas de controle. Mais detalhes sobre esse comportamento podem ser encontrados nas seções subsequentes.

As filas de controle contêm uma variedade de tipos de mensagem de ciclo de vida de orquestração. Os exemplos incluem [mensagens de controle](durable-functions-instance-management.md)do Orchestrator, mensagens de *resposta* da função de atividade e mensagens de timer. Até 32 mensagens serão removidas da fila de controle em uma única sondagem. Essas mensagens contêm dados de carga, bem como metadados, incluindo a qual instância de orquestração ela se destina. Se várias mensagens removidas da fila forem destinadas à mesma instância de orquestração, elas serão processadas como um lote.

### <a name="queue-polling"></a>Sondagem de fila

A extensão de tarefa durável implementa um algoritmo de retirada exponencial aleatória para reduzir o efeito da sondagem de fila ociosa nos custos de transação de armazenamento. Quando uma mensagem é encontrada, o tempo de execução verifica imediatamente se há outra mensagem; quando nenhuma mensagem é encontrada, ela aguarda um período de tempo antes de tentar novamente. Após as tentativas subsequentes falharem em obter uma mensagem da fila, o tempo de espera continuará aumentando até atingir o tempo de espera máximo, cujo padrão é 30 segundos.

O atraso máximo de sondagem é configurável `maxQueuePollingInterval` por meio da propriedade no [arquivo host. JSON](../functions-host-json.md#durabletask). Definir isso para um valor mais alto pode resultar em latências de processamento de mensagens mais altas. As latências mais altas seriam esperadas somente após períodos de inatividade. Definir isso com um valor mais baixo pode resultar em custos de armazenamento mais altos devido a maiores transações de armazenamento.

> [!NOTE]
> Durante a execução nos planos de consumo Azure Functions e Premium, o [controlador de escala de Azure Functions](../functions-scale.md#how-the-consumption-and-premium-plans-work) sondará cada controle e a fila de itens de trabalho uma vez a cada 10 segundos. Essa sondagem adicional é necessária para determinar quando ativar instâncias do aplicativo de funções e tomar decisões de escala. No momento da gravação, esse intervalo de 10 segundos é constante e não pode ser configurado.

## <a name="storage-account-selection"></a>Seleção de conta de armazenamento

As filas, as tabelas e os BLOBs usados pelo Durable Functions são criados em uma conta de armazenamento do Azure configurada. A conta a ser usada pode ser especificada usando `durableTask/azureStorageConnectionStringName` a configuração no arquivo **host. JSON** .

### <a name="functions-1x"></a>Funções 1.x

```json
{
  "durableTask": {
    "azureStorageConnectionStringName": "MyStorageAccountAppSetting"
  }
}
```

### <a name="functions-2x"></a>Funções 2.x

```json
{
  "extensions": {
    "durableTask": {
      "azureStorageConnectionStringName": "MyStorageAccountAppSetting"
    }
  }
}
```

Se não for especificado, a `AzureWebJobsStorage` conta de armazenamento padrão será usada. No entanto, para cargas de trabalho sensíveis ao desempenho, é recomendável configurar uma conta de armazenamento não padrão. O Durable Functions usa muito o armazenamento do Azure e o uso de uma conta de armazenamento dedicada isola Durable Functions uso do armazenamento do uso interno pelo host Azure Functions.

## <a name="orchestrator-scale-out"></a>Expansão do Orchestrator

As funções de atividade são sem estado e são dimensionadas automaticamente adicionando VMs. As funções de orquestrador, por outro lado, são particionadas em uma ou mais filas de controle. O número de filas de controle é definido no arquivo **host. JSON** . O trecho de código host. JSON de exemplo `durableTask/partitionCount` a seguir `3`define a propriedade como.

### <a name="functions-1x"></a>Funções 1.x

```json
{
  "durableTask": {
    "partitionCount": 3
  }
}
```

### <a name="functions-2x"></a>Funções 2.x

```json
{
  "extensions": {
    "durableTask": {
      "partitionCount": 3
    }
  }
}
```

Um hub de tarefas pode ser configurado com entre 1 e 16 partições. Se não for especificado, a contagem de partições padrão será **4**.

Ao escalar horizontalmente para várias instâncias de host de função (normalmente em VMs diferentes), cada instância adquire um bloqueio em uma das filas de controle. Esses bloqueios são implementados internamente como concessões de armazenamento de BLOBs e garantem que uma instância de orquestração seja executada somente em uma única instância de host de cada vez. Se um hub de tarefas estiver configurado com três filas de controle, as instâncias de orquestração poderão ser balanceadas com balanceamento de carga em até três VMs. VMs adicionais podem ser adicionadas para aumentar a capacidade de execução da função de atividade.

O diagrama a seguir ilustra como o host Azure Functions interage com as entidades de armazenamento em um ambiente expandido.

![Diagrama de escala](./media/durable-functions-perf-and-scale/scale-diagram.png)

Conforme mostrado no diagrama anterior, todas as VMs concorrem para mensagens na fila de itens de trabalho. No entanto, somente três VMs podem adquirir mensagens de filas de controle e cada VM bloqueia uma única fila de controle.

As instâncias de orquestração são distribuídas entre todas as instâncias de fila de controle. A distribuição é feita com o hash da ID da instância da orquestração. As IDs de instância por padrão são GUIDs aleatórios, garantindo que as instâncias sejam distribuídas igualmente entre todas as filas de controle.

Em termos gerais, as funções de orquestrador são destinadas a serem leves e não devem exigir grandes quantidades de poder de computação. Portanto, não é necessário criar um grande número de partições de fila de controle para obter uma grande taxa de transferência. A maior parte do trabalho pesado deve ser feita em funções de atividade sem estado, o que pode ser reduzido infinitamente.

## <a name="auto-scale"></a>Dimensionamento Automático

Assim como todos os Azure Functions em execução no plano de consumo, Durable Functions dá suporte ao dimensionamento automático por meio do [controlador de escala de Azure Functions](../functions-scale.md#runtime-scaling). O controlador de escala monitora a latência de todas as filas emitindo comandos de _inspeção_ periodicamente. Com base nas latências das mensagens inspecionadas, o controlador de escala decidirá se deseja adicionar ou remover VMs.

Se o controlador de escala determinar que as latências de mensagem de fila de controle são muito altas, elas adicionarão instâncias de VM até que a latência da mensagem diminua para um nível aceitável ou atinja a contagem de partições da fila de controle. Da mesma forma, o controlador de escala adicionará continuamente instâncias de VM se as latências de fila de item de trabalho forem altas, independentemente da contagem de partições.

## <a name="thread-usage"></a>Uso de thread

As funções de orquestrador são executadas em um único thread para garantir que a execução possa ser determinística em várias repetições. Devido a essa execução de thread único, é importante que os threads de função do orquestrador não executem tarefas de uso intensivo de CPU, executem e/s ou bloqueiem por qualquer motivo. Qualquer trabalho que possa exigir e/s, bloqueio ou vários threads deve ser movido para funções de atividade.

As funções de atividade têm os mesmos comportamentos que as funções disparadas por fila regulares. Eles podem fazer e/s com segurança, executar operações com uso intensivo de CPU e usar vários threads. Como os gatilhos de atividade são sem estado, eles podem ser redimensionados livremente para um número não associado de VMs.

## <a name="concurrency-throttles"></a>Restrições de simultaneidade

O Azure Functions dá suporte à execução de várias funções simultaneamente em uma única instância de aplicativo. Essa execução simultânea ajuda a aumentar o paralelismo e minimiza o número de "inícios frios" que um aplicativo típico terá ao longo do tempo. No entanto, a alta simultaneidade pode resultar em alto uso de memória por VM. Dependendo das necessidades do aplicativo de funções, pode ser necessário limitar a simultaneidade por instância para evitar a possibilidade de ficar sem memória em situações de carga alta.

Os limites de simultaneidade da função de atividade e do orquestrador podem ser configurados no arquivo **host. JSON** . As configurações relevantes são `durableTask/maxConcurrentActivityFunctions` e `durableTask/maxConcurrentOrchestratorFunctions` , respectivamente.

### <a name="functions-1x"></a>Funções 1.x

```json
{
  "durableTask": {
    "maxConcurrentActivityFunctions": 10,
    "maxConcurrentOrchestratorFunctions": 10
  }
}
```

### <a name="functions-2x"></a>Funções 2.x

```json
{
  "extensions": {
    "durableTask": {
      "maxConcurrentActivityFunctions": 10,
      "maxConcurrentOrchestratorFunctions": 10
    }
  }
}
```

No exemplo anterior, um máximo de 10 funções de orquestrador e 10 funções de atividade podem ser executados em uma única VM simultaneamente. Se não for especificado, o número de atividades simultâneas e execuções de função de orquestrador será limitado a 10 vezes o número de núcleos na VM.

> [!NOTE]
> Essas configurações são úteis para ajudar a gerenciar a memória e o uso da CPU em uma única VM. No entanto, quando dimensionado horizontalmente em várias VMs, cada VM terá seu próprio conjunto de limites. Essas configurações não podem ser usadas para controlar a simultaneidade em um nível global.

## <a name="orchestrator-function-replay"></a>Reprodução de função de orquestrador

Conforme mencionado anteriormente, as funções de orquestrador são reproduzidas usando o conteúdo da tabela de **histórico** . Por padrão, o código da função de orquestrador é repetido sempre que um lote de mensagens é removido da fila de uma fila de controle.

Esse comportamento agressivo de repetição pode ser desabilitado habilitando as **sessões estendidas**. Quando as sessões estendidas são habilitadas, as instâncias de função de orquestrador são mantidas em memória por mais tempo e novas mensagens podem ser processadas sem uma reprodução completa. As sessões estendidas são habilitadas `true` pela configuração `durableTask/extendedSessionsEnabled` para no arquivo **host. JSON** . A `durableTask/extendedSessionIdleTimeoutInSeconds` configuração é usada para controlar quanto tempo uma sessão ociosa será mantida na memória:

### <a name="functions-1x"></a>Funções 1.x

```json
{
  "durableTask": {
    "extendedSessionsEnabled": true,
    "extendedSessionIdleTimeoutInSeconds": 30
  }
}
```

### <a name="functions-2x"></a>Funções 2.x

```json
{
  "extensions": {
    "durableTask": {
      "extendedSessionsEnabled": true,
      "extendedSessionIdleTimeoutInSeconds": 30
    }
  }
}
```

O efeito típico da habilitação de sessões estendidas é a redução de e/s em relação à conta de armazenamento do Azure e à taxa de transferência geral aprimorada.

No entanto, uma possível desvantagem desse recurso é que as instâncias de função de orquestrador ociosa permanecerão na memória por mais tempo. Há dois efeitos a serem considerados:

1. Aumento geral no uso de memória do aplicativo de funções.
2. Redução geral na taxa de transferência se houver muitas execuções de função de orquestrador simultâneas e de curta duração.

Por exemplo, se `durableTask/extendedSessionIdleTimeoutInSeconds` for definido como 30 segundos, um episódio da função de orquestrador de curta duração que é executado em menos de 1 segundo ainda ocupará memória por 30 segundos. Ele também contará em relação `durableTask/maxConcurrentOrchestratorFunctions` à cota mencionada anteriormente, potencialmente impedindo que outras funções de orquestrador sejam executadas.

> [!NOTE]
> Essas configurações só devem ser usadas depois que uma função de orquestrador tiver sido totalmente desenvolvida e testada. O comportamento de reprodução agressiva padrão é útil para detectar erros de Idempotência em funções de orquestrador em tempo de desenvolvimento.

## <a name="performance-targets"></a>Metas de desempenho

Ao planejar o uso de Durable Functions para um aplicativo de produção, é importante considerar os requisitos de desempenho no início do processo de planejamento. Esta seção aborda alguns cenários de uso básico e os números de taxa de transferência máximos esperados.

* **Execução de atividade sequencial**: Este cenário descreve uma função de orquestrador que executa uma série de funções de atividade uma após a outra. Ele é mais parecido com o exemplo de [encadeamento de funções](durable-functions-sequence.md) .
* **Execução de atividade paralela**: Este cenário descreve uma função de orquestrador que executa muitas funções de atividade em paralelo usando o padrão [Fan-out e Fan-in](durable-functions-cloud-backup.md) .
* **Processamento de resposta paralelo**: Esse cenário é a segunda metade do padrão [Fan-out e Fan-in](durable-functions-cloud-backup.md) . Ele se concentra no desempenho do Fan-in. É importante observar que, ao contrário do fan-out, o Fan-in é feito por uma única instância de função de orquestrador e, portanto, só pode ser executado em uma única VM.
* **Processamento de eventos externos**: Esse cenário representa uma única instância de função de orquestrador que aguarda [eventos externos](durable-functions-external-events.md), um de cada vez.

> [!TIP]
> Diferentemente do fan-out, as operações de Fan-in são limitadas a uma única VM. Se o seu aplicativo usar o padrão Fan-out, de Fan-in e você estiver preocupado com o desempenho do Fan-in, considere subdividir a função de atividade Fan [](durable-functions-sub-orchestrations.md)-out em várias suborquestrações.

A tabela a seguir mostra os números de taxa de transferência máximos esperados para os cenários descritos anteriormente. "Instance" refere-se a uma única instância de uma função de orquestrador em execução em uma única VM pequena ([a1](../../virtual-machines/windows/sizes-previous-gen.md#a-series)) no serviço Azure app. Em todos os casos, supõe-se que as [sessões estendidas](#orchestrator-function-replay) estão habilitadas. Os resultados reais podem variar dependendo da CPU ou do trabalho de e/s executado pelo código da função.

| Cenário | Débito máximo |
|-|-|
| Execução de atividade sequencial | 5 atividades por segundo, por instância |
| Execução de atividade paralela (Fan-out) | 100 atividades por segundo, por instância |
| Processamento de resposta paralelo (Fan-in) | 150 respostas por segundo, por instância |
| Processamento de eventos externos | 50 eventos por segundo, por instância |

> [!NOTE]
> Esses números são atuais a partir da versão v 1.4.0 (GA) da extensão de Durable Functions. Esses números podem mudar ao longo do tempo à medida que o recurso amadurece e conforme as otimizações são feitas.

Se você não estiver vendo os números de taxa de transferência esperados e o uso de CPU e memória aparecer íntegro, verifique se a causa está relacionada à [integridade da sua conta de armazenamento](../../storage/common/storage-monitoring-diagnosing-troubleshooting.md#troubleshooting-guidance). A extensão de Durable Functions pode colocar uma carga significativa em uma conta de armazenamento do Azure e cargas suficientemente altas podem resultar na limitação da conta de armazenamento.

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Create your first durable function in C#](durable-functions-create-first-csharp.md) (Criar a sua primeira função durável em C#)
