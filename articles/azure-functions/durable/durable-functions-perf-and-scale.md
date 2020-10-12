---
title: Desempenho e escala em Funções Duradouras - Azure
description: Conheça as características de escala únicas da extensão de Funções Duradouras para Funções Azure.
author: cgillum
ms.topic: conceptual
ms.date: 11/03/2019
ms.author: azfuncdf
ms.openlocfilehash: 56a9861f0e25e1dcdf741cfdf5c8830dd9b6fc1f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91325815"
---
# <a name="performance-and-scale-in-durable-functions-azure-functions"></a>Performance and scale in Durable Functions (Azure Functions) (Desempenho e dimensionamento no Durable Functions [Funções do Azure])

Para otimizar o desempenho e a escalabilidade, é importante compreender as características únicas de escalagem das [Funções Duráveis.](durable-functions-overview.md)

Para entender o comportamento da escala, você tem que entender alguns dos detalhes do fornecedor de armazenamento Azure subjacente.

## <a name="history-table"></a>Tabela de história

A tabela **História** é uma tabela de Armazenamento Azure que contém os eventos de história para todos os casos de orquestração dentro de um centro de tarefas. O nome desta tabela está na forma *TaskHubName*History. À medida que os casos são executados, novas linhas são adicionadas a esta tabela. A chave de partição desta tabela deriva do exemplo de identificação da orquestração. Um iD de caso é aleatório na maioria dos casos, o que garante uma distribuição ótima de divisórias internas no Azure Storage.

Quando uma instância de orquestração precisa de ser executada, as linhas apropriadas da tabela História são carregadas na memória. Estes *eventos de história* são então reproduzidos no código de função do orquestrador para o colocar de volta no seu estado previamente posto de controlo. O uso da história da execução para reconstruir o estado desta forma é influenciado pelo [padrão de Sourcing de Eventos.](/azure/architecture/patterns/event-sourcing)

## <a name="instances-table"></a>Tabela de casos

A tabela **Instâncias** é outra tabela de Armazenamento Azure que contém os estatutos de todas as instâncias de orquestração e entidade dentro de um centro de tarefas. À medida que os casos são criados, novas linhas são adicionadas a esta tabela. A chave de partição desta tabela é a identificação de instância de orquestração ou chave de entidade e a chave de linha é uma constante fixa. Há uma linha por orquestração ou instância de entidade.

Esta tabela é utilizada para satisfazer pedidos de consulta de instâncias das `GetStatusAsync` APIs (.NET) e `getStatus` (JavaScript), bem como a [consulta de estado HTTP API](durable-functions-http-api.md#get-instance-status). É eventualmente mantido consistente com o conteúdo da tabela **História** mencionada anteriormente. A utilização de uma tabela de armazenamento Azure separada para satisfazer eficazmente as operações de consulta de instância desta forma é influenciada pelo [padrão de Segregação de Responsabilidade De Comando e Consulta (CQRS).](/azure/architecture/patterns/cqrs)

## <a name="internal-queue-triggers"></a>Gatilhos de fila internos

As funções de orquestrador e as funções de atividade são ambas desencadeadas por filas internas no centro de tarefas da aplicação de funções. A utilização de filas desta forma fornece garantias fiáveis de entrega de mensagens "pelo menos uma vez". Existem dois tipos de filas em Funções Duradouras: a fila de **controlo** e a **fila do artigo de trabalho**.

### <a name="the-work-item-queue"></a>A fila de artigos de trabalho

Existe uma fila de artigos de trabalho por centro de tarefas em Funções Duradouras. É uma fila básica e comporta-se da mesma forma que qualquer outra `queueTrigger` fila em Funções Azure. Esta fila é usada para desencadear *funções* de atividade apátrida, descodionando uma única mensagem de cada vez. Cada uma destas mensagens contém entradas de função de atividade e metadados adicionais, tais como qual a função a executar. Quando uma aplicação Funções Duradouras se escala para vários VMs, estes VMs competem para adquirir trabalho a partir da fila de artigos de trabalho.

### <a name="control-queues"></a>Filas de controlo

Existem *várias filas de controlo* por centro de tarefas em Funções Duradouras. Uma *fila de controlo* é mais sofisticada do que a fila de artigos de trabalho mais simples. As filas de controlo são usadas para desencadear as funções de orquestrador e entidade. Como os exemplos de função do orquestrador e da entidade são singletons imponentes, não é possível usar um modelo de consumidor concorrente para distribuir a carga por VMs. Em vez disso, as mensagens orquestradoras e de entidades são equilibradas em todas as filas de controlo. Mais detalhes sobre este comportamento podem ser encontrados em secções subsequentes.

As filas de controlo contêm uma variedade de tipos de mensagens de ciclo de vida de orquestração. Exemplos incluem [mensagens de controlo de orquestradores,](durable-functions-instance-management.md)mensagens *de resposta da função* de atividade e mensagens temporais. Até 32 mensagens serão retiradas de uma fila de controlo numa única sondagem. Estas mensagens contêm dados de carga útil, bem como metadados, incluindo qual a instância de orquestração para a qual se destina. Se várias mensagens desosseadas forem destinadas à mesma instância de orquestração, serão processadas como um lote.

### <a name="queue-polling"></a>Sondagem de fila

A extensão de tarefa durável implementa um algoritmo de back-off exponencial aleatório para reduzir o efeito da votação em fila ociosa nos custos de transação de armazenamento. Quando uma mensagem é encontrada, o tempo de execução verifica imediatamente outra mensagem; quando nenhuma mensagem é encontrada, espera por um período de tempo antes de tentar novamente. Após tentativas falhadas subsequentes de obter uma mensagem de fila, o tempo de espera continua a aumentar até atingir o tempo máximo de espera, o que não chega a 30 segundos.

O máximo de atraso nas sondagens é configurável através da `maxQueuePollingInterval` propriedade nahost.jsem [ arquivo](../functions-host-json.md#durabletask). Definir esta propriedade a um valor mais elevado pode resultar em latências de processamento de mensagens mais elevadas. As latências mais elevadas só seriam esperadas após períodos de inatividade. A fixação desta propriedade a um valor mais baixo pode resultar em custos de armazenamento mais elevados devido ao aumento das transações de armazenamento.

> [!NOTE]
> Ao executar os planos Azure Functions Consumption and Premium, o [Controlador de Escala de Funções Azure](../functions-scale.md#how-the-consumption-and-premium-plans-work) irá sondar cada fila de controlo e de produto de trabalho uma vez a cada 10 segundos. Esta sondagem adicional é necessária para determinar quando ativar instâncias de aplicações de função e tomar decisões de escala. No momento da escrita, este intervalo de 10 segundos é constante e não pode ser configurado.

### <a name="orchestration-start-delays"></a>Atrasos no início da orquestração
As instâncias de orquestração começam colocando uma `ExecutionStarted` mensagem numa das filas de controlo do centro de tarefas. Sob certas condições, pode observar atrasos de vários segundos entre quando uma orquestração está programada para ser executada e quando realmente começa a funcionar. Durante este intervalo de tempo, a instância de orquestração permanece no `Pending` estado. Há duas causas potenciais deste atraso:

1. **Filas de controlo retrostruidas**: Se a fila de controlo deste caso contiver um grande número de mensagens, pode demorar algum tempo até que a `ExecutionStarted` mensagem seja recebida e processada pelo tempo de funcionamento. Os atrasos nas mensagens podem acontecer quando as orquestrações estão a processar muitos eventos simultaneamente. Eventos que entram na fila de controlo incluem eventos de início de orquestração, conclusão de atividades, temporizadores duráveis, rescisão e eventos externos. Se este atraso acontecer em circunstâncias normais, considere criar um novo centro de tarefas com um maior número de divisórias. Configurar mais divisórias fará com que o tempo de execução crie mais filas de controlo para distribuição de carga.

2. **Atrasos nas sondagens**: Outra causa comum de atrasos na orquestração é o [comportamento de sondagens de back-off previamente descrito para filas de controlo](#queue-polling). No entanto, este atraso só é esperado quando uma aplicação é reduzida para dois ou mais casos. Se houver apenas uma instância de aplicação ou se a instância da aplicação que inicia a orquestração é também a mesma instância que está a sondar a fila de controlo alvo, então não haverá um atraso nas sondagens. Os atrasos nas sondagens podem ser reduzidos atualizando o **host.jsnas** definições, como descrito anteriormente.

## <a name="storage-account-selection"></a>Seleção de conta de armazenamento

As filas, mesas e bolhas utilizadas pelas Funções Duráveis são criadas numa conta de Armazenamento Azure configurada. A conta a utilizar pode ser especificada utilizando a `durableTask/storageProvider/connectionStringName` definição (ou `durableTask/azureStorageConnectionStringName` definição em Funções Duráveis 1.x) no **host.jsno** ficheiro.

### <a name="durable-functions-2x"></a>Funções duradouras 2.x

```json
{
  "extensions": {
    "durableTask": {
      "storageProvider": {
        "connectionStringName": "MyStorageAccountAppSetting"
      }
    }
  }
}
```

### <a name="durable-functions-1x"></a>Funções duradouras 1.x

```json
{
  "extensions": {
    "durableTask": {
      "azureStorageConnectionStringName": "MyStorageAccountAppSetting"
    }
  }
}
```

Se não for especificada, a conta de armazenamento predefinida `AzureWebJobsStorage` é utilizada. No entanto, para cargas de trabalho sensíveis ao desempenho, recomenda-se a configuração de uma conta de armazenamento não padrão. As Funções Duradouras utilizam fortemente o Armazenamento Azure e a utilização de uma conta de armazenamento dedicada isola o uso de funções duráveis a partir da utilização interna pelo anfitrião Azure Functions.

## <a name="orchestrator-scale-out"></a>Escala de orquestrador

As funções de atividade são apátridas e escalonada automaticamente adicionando VMs. As funções e entidades orquestradoras, por outro lado, são *divididas* através de uma ou mais filas de controlo. O número de filas de controlo é definido no **host.jsem** ficheiro. O exemplo a seguir host.jsno snippet define a `durableTask/storageProvider/partitionCount` propriedade (ou `durableTask/partitionCount` em Funções Duradouras 1.x) para `3` .

### <a name="durable-functions-2x"></a>Funções duradouras 2.x

```json
{
  "extensions": {
    "durableTask": {
      "storageProvider": {
          "partitionCount": 3
      }
    }
  }
}
```

### <a name="durable-functions-1x"></a>Funções duradouras 1.x

```json
{
  "extensions": {
    "durableTask": {
      "partitionCount": 3
    }
  }
}
```

Um centro de tarefas pode ser configurado com entre 1 e 16 divisórias. Se não for especificado, a contagem de divisórias predefinida é **de 4**.

Ao escalar para várias instâncias de anfitrião de funções (normalmente em VMs diferentes), cada instância adquire um bloqueio em uma das filas de controlo. Estes cadeados são implementados internamente como locações de armazenamento de bolhas e garantem que uma instância ou entidade de orquestração só funciona em um único caso de anfitrião de cada vez. Se um centro de tarefas estiver configurado com três filas de controlo, as instâncias de orquestração e as entidades podem ser equilibradas em cargas até três VMs. VMs adicionais podem ser adicionados para aumentar a capacidade de execução da função de atividade.

O diagrama seguinte ilustra como o hospedeiro Azure Functions interage com as entidades de armazenamento num ambiente de escala.

![Diagrama de escala](./media/durable-functions-perf-and-scale/scale-diagram.png)

Como mostrado no diagrama anterior, todos os VMs competem por mensagens na fila do item de trabalho. No entanto, apenas três VMs podem adquirir mensagens a partir de filas de controlo, e cada VM bloqueia uma única fila de controlo.

As instâncias e entidades de orquestração são distribuídas por todos os casos de fila de controlo. A distribuição é feita através do hashing do iD de instância da orquestração ou do nome da entidade e do par de chaves. Os IDs de instância de orquestração por padrão são GUIDs aleatórios, garantindo que as instâncias são distribuídas igualmente em todas as filas de controlo.

De um modo geral, as funções de orquestrador destinam-se a ser leves e não devem exigir grandes quantidades de poder de computação. Por conseguinte, não é necessário criar um grande número de divisórias de fila de controlo para obter grande produção para orquestrações. A maior parte do trabalho pesado deve ser feito em funções de atividade apátrida, que podem ser escalonadas infinitamente.

## <a name="auto-scale"></a>Escala automática

Tal como acontece com todas as Funções Azure em execução nos planos Consumption e Elastic Premium, as Funções Duradouras suportam a escala automática através do controlador de [escala Azure Functions](../functions-scale.md#runtime-scaling). O Controlador de Escala monitoriza a latência de todas as filas através da emissão periódica de comandos _de espreitar._ Com base nas latências das mensagens espreitadas, o Controlador de Escala decidirá se adiciona ou remove VMs.

Se o Controlador de Escala determinar que as latências da fila de controlo são demasiado elevadas, adicionará instâncias VM até que a latência da mensagem diminua para um nível aceitável ou atinja a contagem de partição da fila de controlo. Da mesma forma, o Controlador de Escala adicionará continuamente instâncias VM se as latências de fila de artigos de trabalho forem elevadas, independentemente da contagem de divisórias.

> [!NOTE]
> A partir das Funções Duradouras 2.0, as aplicações de função podem ser configuradas para funcionar em pontos finais de serviço protegidos por VNET no plano Elastic Premium. Nesta configuração, as Funções Duradouras acionam pedidos de escala iniciados em vez do Controlador de Escala.

## <a name="thread-usage"></a>Utilização do fio

As funções de orquestrador são executadas num único fio para garantir que a execução pode ser determinística em muitas repetições. Devido a esta execução de linha única, é importante que os fios de função do orquestrador não executem tarefas intensivas de CPU, façam I/O, ou bloqueiem por qualquer motivo. Qualquer trabalho que possa requerer I/S, bloqueio ou múltiplos fios deve ser movido para funções de atividade.

As funções de atividade têm todos os mesmos comportamentos que as funções regulares desencadeadas pela fila. Podem fazer I/O com segurança, executar operações intensivas de CPU e usar vários fios. Como os gatilhos de atividade são apátridas, podem escalar livremente para um número ilimitado de VMs.

As funções de entidade também são executadas num único fio e as operações são processadas uma de cada vez. No entanto, as funções da entidade não têm quaisquer restrições sobre o tipo de código que pode ser executado.

## <a name="concurrency-throttles"></a>Aceleradores de concurrency

O Azure Functions suporta executar várias funções simultaneamente numa única instância de aplicação. Esta execução simultânea ajuda a aumentar o paralelismo e minimiza o número de "arranques frios" que uma aplicação típica irá experimentar ao longo do tempo. No entanto, a elevada concordância pode esgotar os recursos do sistema por VM tais ligações de rede ou memória disponível. Dependendo das necessidades da aplicação de função, pode ser necessário acelerar a concordância por instância para evitar a possibilidade de ficar sem memória em situações de alta carga.

Os limites de atividade, orquestrador e de função da entidade podem ser configurados no **host.jsem** arquivo. As definições relevantes são `durableTask/maxConcurrentActivityFunctions` para funções de atividade e `durableTask/maxConcurrentOrchestratorFunctions` para funções de orquestrador e entidade.

### <a name="functions-20"></a>Funções 2.0

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

### <a name="functions-1x"></a>Funções 1.x

```json
{
  "durableTask": {
    "maxConcurrentActivityFunctions": 10,
    "maxConcurrentOrchestratorFunctions": 10
  }
}
```

No exemplo anterior, um máximo de 10 funções de orquestrador ou entidade e 10 funções de atividade podem funcionar em simultâneo com um único VM. Se não for especificado, o número de execuções de funções de orquestrador ou de entidade simultânea é limitado a 10X o número de núcleos no VM.

> [!NOTE]
> Estas definições são úteis para ajudar a gerir a memória e a utilização do CPU num único VM. No entanto, quando dimensionado através de vários VMs, cada VM tem o seu próprio conjunto de limites. Estas definições não podem ser usadas para controlar a conúnva a nível global.

## <a name="extended-sessions"></a>Sessões estendidas

As sessões estendidas são um cenário que mantém as orquestrações e entidades na memória mesmo depois de terminarem o processamento de mensagens. O efeito típico de permitir sessões prolongadas é reduzido I/O contra a conta de Armazenamento Azure e melhoramento geral.

Pode ativar sessões prolongadas definindo `durableTask/extendedSessionsEnabled` para ohost.js`true` ** no** ficheiro. A `durableTask/extendedSessionIdleTimeoutInSeconds` definição pode ser usada para controlar quanto tempo uma sessão ociosa será mantida na memória:

**Funções 2.0**
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

**Funções 1.0**
```json
{
  "durableTask": {
    "extendedSessionsEnabled": true,
    "extendedSessionIdleTimeoutInSeconds": 30
  }
}
```

Há duas potenciais desvantagens deste cenário a ter em conta:

1. Há um aumento geral no uso da memória de aplicações de função.
2. Pode haver uma diminuição geral da produção se houver muitas execuções simultâneas, orquestradoras de curta duração ou funções de entidade.

Como exemplo, se `durableTask/extendedSessionIdleTimeoutInSeconds` estiver definido para 30 segundos, então um episódio de função orquestrador ou de entidade de curta duração que executa em menos de 1 segundo ainda ocupa a memória por 30 segundos. Também conta contra a `durableTask/maxConcurrentOrchestratorFunctions` quota mencionada anteriormente, potencialmente impedindo que outras funções de orquestrador ou entidade funcionem.

Os efeitos específicos das sessões alargadas nas funções de orquestrador e de entidade são descritos nas secções seguintes.

> [!NOTE]
> Atualmente, as sessões alargadas só são suportadas em línguas .NET, como C# ou F#. A definição `extendedSessionsEnabled` `true` para outras plataformas pode levar a problemas de tempo de execução, tais como falhas silenciosas na execução de atividades e funções desencadeadas por orquestração.


### <a name="orchestrator-function-replay"></a>Repetição da função orquestradora

Como mencionado anteriormente, as funções de orquestrador são reproduzidas usando o conteúdo da tabela **História.** Por predefinição, o código de função do orquestrador é reproduzido sempre que um lote de mensagens é descoduado de uma fila de controlo. Mesmo que esteja a utilizar o padrão de fan-out, fan-in e aguarda que todas as tarefas sejam concluídas (por exemplo, usando `Task.WhenAll` em .NET ou `context.df.Task.all` em JavaScript), haverá repetições que ocorrem à medida que os lotes de respostas de tarefas são processados ao longo do tempo. Quando as sessões prolongadas são ativadas, as instâncias de função do orquestrador são realizadas na memória por mais tempo e novas mensagens podem ser processadas sem uma repetição completa da história.

A melhoria do desempenho das sessões alargadas é observada mais frequentemente nas seguintes situações:

* Quando há um número limitado de instâncias de orquestração a decorrer simultaneamente.
* Quando as orquestrações têm um grande número de ações sequenciais (por exemplo, centenas de chamadas de funções de atividade) que se completam rapidamente.
* Quando as orquestrações se acasa e os fãs num grande número de ações que se completam ao mesmo tempo.
* Quando as funções do orquestrador precisam de processar mensagens grandes ou fazer qualquer tratamento de dados intensivo de CPU.

Em todas as outras situações, não existe tipicamente uma melhoria observável do desempenho para funções orquestradoras.

> [!NOTE]
> Estas definições só devem ser utilizadas depois de uma função de orquestrador ter sido totalmente desenvolvida e testada. O comportamento de repetição agressiva padrão pode ser útil para detetar violações de [código de função do orquestrador](durable-functions-code-constraints.md) no momento do desenvolvimento, e é, portanto, desativado por padrão.

### <a name="entity-function-unloading"></a>Descarga de função da entidade

As funções da entidade processam até 20 operações num único lote. Assim que uma entidade termina o processamento de um lote de operações, persiste o seu estado e descarrega da memória. Pode atrasar a descarga de entidades da memória utilizando a definição de sessões estendidas. As entidades continuam a persistir as suas mudanças de estado como antes, mas permanecem na memória durante o período de tempo configurado para reduzir o número de cargas do Azure Storage. Esta redução de cargas do Azure Storage pode melhorar a produção global de entidades frequentemente acedidas.

## <a name="performance-targets"></a>Metas de desempenho

Ao planear utilizar Funções Duradouras para uma aplicação de produção, é importante considerar os requisitos de desempenho no início do processo de planeamento. Esta secção abrange alguns cenários básicos de utilização e os números máximos de produção esperados.

* **Execução de atividade sequencial**: Este cenário descreve uma função orquestradora que executa uma série de funções de atividade uma após a outra. Assemelha-se mais à amostra [de corrente de função.](durable-functions-sequence.md)
* **Execução paralela da atividade**: Este cenário descreve uma função orquestradora que executa muitas funções de atividade paralelamente utilizando o padrão [Fan-out, Fan-in.](durable-functions-cloud-backup.md)
* **Processamento de resposta paralela**: Este cenário é a segunda metade do padrão [Fan-out, Fan-in.](durable-functions-cloud-backup.md) Centra-se na performance do fã. É importante notar que, ao contrário do fan-out, o fan-in é feito por uma única instância de função orquestradora, e, portanto, só pode funcionar em um único VM.
* **Processamento de eventos externos**: Este cenário representa uma única função orquestradora que aguarda [eventos externos,](durable-functions-external-events.md)um de cada vez.
* **Processamento de operação de entidade**: Este cenário testa a rapidez com que uma _única_ [entidade contadora](durable-functions-entities.md) pode processar um fluxo constante de operações.

> [!TIP]
> Ao contrário do fan-out, as operações de fan-in limitam-se a um único VM. Se a sua aplicação utilizar o padrão de fan-out, fan-in e estiver preocupado com o desempenho dos fãs, considere sub-dividir a função de atividade desafiando-se em [várias sub-orquestrações](durable-functions-sub-orchestrations.md).

A tabela seguinte mostra os números *máximos* de produção esperados para os cenários previamente descritos. "Instance" refere-se a uma única instância de uma função orquestradora que funciona num único pequeno[(A1](../../virtual-machines/sizes-previous-gen.md)) VM em Azure App Service. Em todos os casos, presume-se que as [sessões prolongadas](#orchestrator-function-replay) estão habilitados. Os resultados reais podem variar dependendo do trabalho de CPU ou I/S realizado pelo código de função.

| Cenário | Débito máximo |
|-|-|
| Execução de atividade sequencial | 5 atividades por segundo, por exemplo |
| Execução paralela da atividade (fan-out) | 100 atividades por segundo, por exemplo |
| Processamento de resposta paralela (fan-in) | 150 respostas por segundo, por exemplo |
| Processamento externo de eventos | 50 eventos por segundo, por exemplo |
| Processamento de operação de entidade | 64 operações por segundo |

> [!NOTE]
> Estes números estão em vigor a partir da libertação v1.4.0 (GA) da extensão funções duráveis. Estes números podem mudar com o tempo à medida que a funcionalidade amadurece e à medida que as otimizações são feitas.

Se não estiver a ver os números de produção que espera e o uso do CPU e da memória parece saudável, verifique se a causa está relacionada com [a saúde da sua conta de armazenamento.](../../storage/common/storage-monitoring-diagnosing-troubleshooting.md#troubleshooting-guidance) A extensão funções duradouras pode colocar carga significativa numa conta de Armazenamento Azure e cargas suficientemente elevadas podem resultar em estrangulamento da conta de armazenamento.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Conheça a recuperação de desastres e geo-distribuição](durable-functions-disaster-recovery-geo-distribution.md)
