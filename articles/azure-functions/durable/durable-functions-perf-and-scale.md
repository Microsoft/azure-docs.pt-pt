---
title: Desempenho e reduzir horizontalmente funções duráveis - Azure
description: Introdução para a extensão de funções duráveis para as funções do Azure.
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 03/14/2019
ms.author: azfuncdf
ms.openlocfilehash: 3c9227a34c1b7208210b84b5b7d64ecdc8654a83
ms.sourcegitcommit: 8a59b051b283a72765e7d9ac9dd0586f37018d30
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58286385"
---
# <a name="performance-and-scale-in-durable-functions-azure-functions"></a>Desempenho e dimensionamento nas funções durável (funções do Azure)

Para otimizar o desempenho e escalabilidade, é importante compreender as características dimensionamento [funções duráveis](durable-functions-overview.md).

Para compreender o comportamento de dimensionamento, terá de compreender alguns dos detalhes do fornecedor de armazenamento do Azure subjacente.

## <a name="history-table"></a>Tabela de histórico

O **histórico** é uma tabela de armazenamento do Azure que contém os eventos de histórico de todas as instâncias de orquestração dentro de um hub de tarefa. O nome desta tabela tem o formato *TaskHubName*histórico. Como as instâncias são executadas, novas linhas são adicionadas a esta tabela. O ID de instância da orquestração deriva a chave de partição desta tabela. Um ID de instância é aleatório na maioria dos casos, o que garante a distribuição ideal de partições internas no armazenamento do Azure.

Quando uma instância da orquestração precisa ser executado, as linhas apropriadas da tabela do histórico são carregadas na memória. Estes *eventos de histórico* , em seguida, são reproduzidos no código de função do orchestrator para recuperá-lo novamente para o seu estado anteriormente foi efetuada a verificação. A utilização de histórico de execução para reconstruir o estado dessa maneira é influenciada pela [padrão de origem do evento](https://docs.microsoft.com/azure/architecture/patterns/event-sourcing).

## <a name="instances-table"></a>Tabela de instâncias

O **instâncias** é outra tabela de armazenamento do Azure que contém os Estados de todas as instâncias de orquestração dentro de um hub de tarefa. Como são criadas instâncias, novas linhas são adicionadas a esta tabela. A chave de partição desta tabela é o ID de instância da orquestração e a chave de linha é uma constante fixa. Há uma linha por instância de orquestração.

Esta tabela é usada para satisfazer os pedidos de consulta de instância do [GetStatusAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_GetStatusAsync_System_String_) (.NET) e `getStatus` (JavaScript) APIs, bem como a [consulta de estado HTTP API](durable-functions-http-api.md#get-instance-status). Ele é mantido eventualmente consistente com o conteúdo do **histórico** tabela mencionado anteriormente. A utilização de uma tabela de armazenamento do Azure separada para atender com eficiência a operações de consulta de instância dessa maneira é influenciada pela [padrão de comando e segregação de responsabilidade de consulta (CQRS)](https://docs.microsoft.com/azure/architecture/patterns/cqrs).

## <a name="internal-queue-triggers"></a>Acionadores da fila interna

Funções do Orchestrator e as funções de atividade são acionadas por filas internas no hub de tarefa da aplicação de funções. Utilizar as filas dessa maneira fornece garantias de entrega de mensagens confiável de "pelo-menos-uma vez". Existem dois tipos de filas em funções duráveis: os **fila de controlo** e o **fila de item de trabalho**.

### <a name="the-work-item-queue"></a>A fila de item de trabalho

Existe uma fila de item de trabalho por hub de tarefas nas funções durável. Ele é uma fila básica e tem um comportamento semelhante a qualquer outro `queueTrigger` fila nas funções do Azure. Esta fila é utilizada para acionar sem monitoração de estado *funções de atividade* por retirar da fila uma única mensagem por vez. Cada uma dessas mensagens contém entradas de função de atividade e metadados adicionais, por exemplo, o qual função a ser executada. Quando uma aplicação de funções duráveis aumenta horizontalmente para várias VMs, estas VMs em todos os competem para adquirir o trabalho da fila de item de trabalho.

### <a name="control-queues"></a>Fila (s) de controle

Existem várias *controlar filas* por hub de tarefas nas funções durável. R *fila de controle* é mais sofisticado do que a fila de item de trabalho mais simples. Filas de controle são utilizadas para acionar as funções do orchestrator com monitoração de estado. Uma vez que as instâncias de função do orchestrator são singletons com monitoração de estado, não é possível utilizar um modelo de consumidores concorrentes para distribuir a carga entre VMs. Em vez disso, as mensagens do orchestrator são com balanceamento de carga entre as filas de controle. Obter mais detalhes sobre esse comportamento podem ser encontrados nas secções subsequentes.

Filas de controle contêm uma variedade de tipos de mensagem de ciclo de vida de orquestração. Os exemplos incluem [mensagens de controlo do orchestrator](durable-functions-instance-management.md), a função de atividade *resposta* mensagens e mensagens de timer. Até 32 mensagens serão removidos da fila de uma fila de controle numa única pesquisa. Essas mensagens contêm dados de carga, bem como metadados, incluindo a instância de orquestração destina. Se várias mensagens de retirada da fila destinam-se para a mesma instância de orquestração, eles serão processados como um lote.

### <a name="queue-polling"></a>Consulta de fila

A extensão de tarefas durável implementa um aleatório exponencial término algoritmo para minimizar o efeito da fila de inatividade de consulta nos custos de transação de armazenamento. Quando é encontrada uma mensagem, o tempo de execução verifica imediatamente para outra mensagem; Quando não é encontrada nenhuma mensagem, ele aguarda um período de tempo antes de tentar novamente. Depois de tentativas falhadas subsequentes para obter uma mensagem de fila, o tempo de espera continua a aumentar até atingir o tempo de espera máximo, o que está predefinida para 30 segundos.

O atraso máximo de sondagem é configurável através da `maxQueuePollingInterval` propriedade no [ficheiro Host. JSON](../functions-host-json.md#durabletask). Definir este tipo como um valor mais alto pode resultar em latências de processamento de mensagens superior. Latências maiores esperadas apenas depois de períodos de inatividade. Definir este tipo como um valor inferior poderá resultar em custos de armazenamento superiores, devido a transações de armazenamento maior.

> [!NOTE]
> Quando em execução nos planos de consumo de funções do Azure e o Premium, o [controlador de dimensionamento de funções do Azure](../functions-scale.md#how-the-consumption-plan-works) pesquisará cada fila de controle e o item de trabalho uma vez a cada 10 segundos. Esta consulta adicional é necessária para determinar quando ativar a instâncias de aplicações de função e para tomar decisões de dimensionamento. No momento da redação, este segundo intervalo 10 é constante e não pode ser configurado.

## <a name="storage-account-selection"></a>Seleção de conta de armazenamento

As filas, tabelas e blobs utilizados pelas funções duráveis são criados numa conta de armazenamento do Azure configurada. A conta a utilizar pode ser especificada utilizando o `durableTask/azureStorageConnectionStringName` definição **Host. JSON** ficheiro.

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

Se não for especificado, a predefinição `AzureWebJobsStorage` é utilizada a conta de armazenamento. Para cargas de trabalho sensíveis a desempenho, no entanto, configurar uma conta de armazenamento não predefinido é recomendado. Funções duráveis intensamente utiliza o armazenamento do Azure e com uma conta de armazenamento dedicado isola a utilização do armazenamento de funções duráveis do uso interno através do anfitrião de funções do Azure.

## <a name="orchestrator-scale-out"></a>Escalamento do Orchestrator

Funções de atividade são aumentados horizontalmente e sem estado automaticamente ao adicionar as VMs. Funções do Orchestrator, por outro lado, são *particionada* por um ou mais filas de controle. O número de filas do controlo está definido no **Host. JSON** ficheiro. Os seguintes conjuntos de Trecho de código do Host. JSON de exemplo da `durableTask/partitionCount` propriedade `3`.

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

Um concentrador de tarefa pode ser configurado com entre 1 e 16 partições. Se não for especificado, o número de partições de predefinido é **4**.

Ao aumentar horizontalmente para várias instâncias de anfitrião de função (normalmente em diferentes VMs), cada instância adquire um bloqueio em um das filas de controle. Esses bloqueios são implementados internamente como concessões de armazenamento de BLOBs e certifique-se de que uma instância da orquestração é executado apenas numa instância de host único por vez. Se um hub de tarefas estiver configurado com três filas de controle, instâncias de orquestração podem ser com balanceamento de carga entre VMs até três. VMs adicionais podem ser adicionadas para aumentar a capacidade de execução da função de atividade.

O diagrama seguinte ilustra como o host de funções do Azure interage com as entidades de armazenamento num ambiente aumentado horizontalmente.

![Diagrama de dimensionamento](./media/durable-functions-perf-and-scale/scale-diagram.png)

Conforme mostrado no diagrama anterior, todas as VMs competem por mensagens na fila de item de trabalho. No entanto, apenas três VMs podem adquirir as mensagens de filas de controle, e cada VM bloqueia uma fila de controlo único.

As instâncias de orquestração são distribuídas em todas as instâncias de fila de controle. A distribuição é feita ao encriptar o ID de instância da orquestração. IDs de instância por predefinição são GUIDs aleatórios, garantindo que instâncias igualmente são distribuídas por todas as filas de controle.

Em termos gerais, as funções do orchestrator destinam-se para ser leve e não devem requerer grandes quantidades de capacidade de computação. Por isso não é necessário criar um grande número de controlo de partições de fila para obter excelente débito. Deve ser feito a maior parte do trabalho pesado em funções de atividade sem monitoração de estado, que podem ser ampliadas infinitamente.

## <a name="auto-scale"></a>Dimensionamento Automático

Como todas as funções do Azure em execução no plano de consumo funções duráveis suporta o dimensionamento automático através de [controlador de dimensionamento de funções do Azure](../functions-scale.md#runtime-scaling). O controlador de escala monitoriza a latência de todas as filas através da emissão de periodicamente _peek_ comandos. Com base nas latências das mensagens peeked, o controlador de escala será decida se pretende adicionar ou remover as VMs.

Se o controlador de dimensionamento determina que latências de mensagem de fila de controle sejam demasiado altas, adicionará a instâncias de VM até que a latência de mensagem diminui a um nível aceitável ou atingir o número de partições de fila de controle. Da mesma forma, o controlador de escala irá adicionar continuamente instâncias de VM se latências de fila de item de trabalho são elevada, independentemente do número de partições.

## <a name="thread-usage"></a>Uso de threads

As funções do Orchestrator são executadas num único thread para se certificar de que execução pode ser determinística entre repetições muitos. Devido a esta execução de thread único, é importante que threads de função do orchestrator não executar tarefas de intensiva da CPU, realizar e/s ou bloquear por qualquer motivo. Qualquer trabalho que podem exigir a e/s, bloquear, ou vários threads devem ser movidos para funções de atividade.

As funções de atividade ter todos os mesmos comportamentos como funções de acionada por fila regulares. Com segurança podem fazer e/s, executar operações intensivas de CPU e utilizar vários threads. Como os acionadores de atividade são sem monitoração de estado, eles podem livremente aumentar horizontalmente para um número não vinculado de VMs.

## <a name="concurrency-throttles"></a>Limitações de simultaneidade

As funções do Azure suporta várias funções simultaneamente dentro de uma instância de aplicação única em execução. Esta execução simultânea ajuda a aumentar a paralelismo e minimiza o número de "arranques a frio" que um aplicativo típico irá ocorrer ao longo do tempo. No entanto, elevada simultaneidade pode resultar numa utilização de memória elevada por VM. Consoante as necessidades da aplicação de função, poderá ser necessário limitar a simultaneidade de por instância para evitar a possibilidade de memória em situações de carga elevada.

Ambas as atividades função e orchestrator função limites de simultaneidade podem ser configurados no **Host. JSON** ficheiro. As definições relevantes são `durableTask/maxConcurrentActivityFunctions` e `durableTask/maxConcurrentOrchestratorFunctions` , respetivamente.

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

No exemplo anterior, um máximo de 10 funções do orchestrator e 10 funções de atividade pode executar numa única VM ao mesmo tempo. Se não for especificado, o número de execuções simultâneas de atividade e orchestrator da função está limitado a 10 vezes o número de núcleos na VM.

> [!NOTE]
> Estas definições são úteis para ajudar a gerir a memória e utilização da CPU numa única VM. No entanto, ao aumentar horizontalmente em várias VMs, cada VM terá seu próprio conjunto de limites. Estas definições não podem ser utilizadas para controlar a simultaneidade num nível global.

## <a name="orchestrator-function-replay"></a>Repetição de função do Orchestrator

Como mencionado anteriormente, as funções do orchestrator são reproduzidas com o conteúdo do **histórico** tabela. Por predefinição, o código de função do orchestrator é reproduzido sempre que um lote de mensagens são removidos da fila de uma fila de controle.

Esse comportamento de repetição agressiva pode ser desativado, permitindo **expandido sessões**. Quando sessões expandidas estiverem ativadas, as instâncias de função do orchestrator são guardadas na memória é possível processar mensagens de novas e mais tempo sem uma repetição completa. Sessões expandidas estão ativadas, definindo `durableTask/extendedSessionsEnabled` para `true` no **Host. JSON** ficheiro. O `durableTask/extendedSessionIdleTimeoutInSeconds` definição é utilizada para controlar o tempo que uma sessão ociosa será mantida na memória:

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

O efeito típico de sessões expandidas de ativação é reduzido e/s com a conta de armazenamento do Azure e geral melhor débito.

No entanto, uma potencial desvantagem desse recurso é essa função de orquestrador inativo instâncias permanecerá na memória mais tempo. Há dois efeitos estar atento:

1. Aumento do número geral de utilização de memória da aplicação de função.
2. Geral redução no débito, se existirem muitas execuções de função de orquestrador em simultâneo de curta duração.

Por exemplo, se `durableTask/extendedSessionIdleTimeoutInSeconds` está definido como 30 segundos, em seguida, um episódio da função de orquestrador de curta duração que executa em menos de 1 segundo ainda irá ocupar memória durante 30 segundos. Também irá contar contra o `durableTask/maxConcurrentOrchestratorFunctions` quota mencionado anteriormente, impedindo potencialmente outras funções do orchestrator a execução.

> [!NOTE]
> Estas definições só devem ser utilizadas depois de uma função de orquestrador foi totalmente desenvolvida e testada. O comportamento de repetição agressiva padrão é útil para detectar erros de idempotência em funções do orchestrator no momento de programação.

## <a name="performance-targets"></a>Metas de desempenho

Quando planear utilizar funções duráveis para uma aplicação de produção, é importante considerar os requisitos de desempenho logo no início do processo de planejamento. Esta secção abrange alguns cenários de utilização básica e os números de débito máximo esperado.

* **Execução de atividade seqüencial**: Este cenário descreve uma função de orquestrador que executa uma série de funções de atividade um após o outro. Ele se assemelhe mais à [encadeamento de função](durable-functions-sequence.md) exemplo.
* **Execução de atividade paralela**: Este cenário descreve uma função de orquestrador que executa muitas funções de atividade em paralelo utilizando a [fan-out, o fan-in](durable-functions-cloud-backup.md) padrão.
* **Resposta de processamento paralelo**: Este cenário é a segunda metade dos [fan-out, o fan-in](durable-functions-cloud-backup.md) padrão. Ele se concentra no desempenho do fan-in. É importante ter em atenção que ao contrário de fan-out, fan-in é feito por uma instância de função de orquestrador única e, por conseguinte, só pode ser executado numa única VM.
* **Processamento de eventos externos**: Este cenário representa uma instância de função de orquestrador único que aguarda [eventos externos](durable-functions-external-events.md), um de cada vez.

> [!TIP]
> Ao contrário de fan-out, as operações de fan-in são limitadas a uma única VM. Se a aplicação utiliza o fan-out, o padrão de fan-in e estiver preocupado com desempenho de fan-in, considere inferiores dividindo o fan-out da função de atividade em múltiplas [orquestrações secundárias](durable-functions-sub-orchestrations.md).

A tabela seguinte mostra a esperada *máximo* números de débito para os cenários descritos anteriormente. "Instância" refere-se para uma única instância de uma função de orquestrador em execução numa única pequena ([A1](../../virtual-machines/windows/sizes-previous-gen.md#a-series)) VM no serviço de aplicações do Azure. Em todos os casos, é assumido que [expandido sessões](#orchestrator-function-replay) estão ativadas. Os resultados reais podem variar consoante o trabalho de CPU ou e/s realizado pelo código da função.

| Cenário | Débito máximo |
|-|-|
| Execução de actividade sequencial | 5 atividades por segundo, por instância |
| Execução de actividade paralela (fan-Out) | 100 atividades por segundo, por instância |
| Processamento de resposta (fan-in) em paralelo | 150 respostas por segundo, por instância |
| Processamento de eventos externos | 50 eventos por segundo, por instância |

> [!NOTE]
> Esses números são atuais no momento do lançamento (GA) do v1.4.0 da extensão de funções duráveis. Esses números podem ser alterados ao longo do tempo, como a funcionalidade amadurece e que sejam feitas otimizações.

Se não vir os números de débito esperado e sua CPU e utilização de memória aparece em bom estado, certifique-se a causa está relacionado com [o estado de funcionamento da sua conta de armazenamento](../../storage/common/storage-monitoring-diagnosing-troubleshooting.md#troubleshooting-guidance). As funções duráveis extensão pode colocar significativa de carga numa conta de armazenamento do Azure e cargas suficientemente alta podem resultar na limitação de conta de armazenamento.

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Create your first durable function in C#](durable-functions-create-first-csharp.md) (Criar a sua primeira função durável em C#)
