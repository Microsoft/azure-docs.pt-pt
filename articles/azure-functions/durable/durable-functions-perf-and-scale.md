---
title: Desempenho e escala em Funções Duráveis - Azure
description: Introdução à extensão de funções duráveis para funções azure.
author: cgillum
ms.topic: conceptual
ms.date: 11/03/2019
ms.author: azfuncdf
ms.openlocfilehash: 260811c4ae15b45de6f7bc1b22e3ed6dcea44259
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79277911"
---
# <a name="performance-and-scale-in-durable-functions-azure-functions"></a>Performance and scale in Durable Functions (Azure Functions) (Desempenho e dimensionamento no Durable Functions [Funções do Azure])

Para otimizar o desempenho e a escalabilidade, é importante compreender as características únicas de escala das [Funções Duráveis.](durable-functions-overview.md)

Para compreender o comportamento de escala, tem de compreender alguns dos detalhes do fornecedor de armazenamento azure subjacente.

## <a name="history-table"></a>Tabela de história

A tabela **História** é uma tabela de Armazenamento Azure que contém os eventos de história para todos os casos de orquestração dentro de um centro de tarefas. O nome desta tabela está na forma *TaskHubName*History. À medida que os casos correm, novas filas são adicionadas a esta mesa. A chave de partição desta tabela é derivada da identificação da orquestração. Uma identificação por exemplo é aleatória na maioria dos casos, o que garante uma distribuição ótima das divisórias internas no Armazenamento Azure.

Quando uma instância de orquestração precisa de ser executada, as linhas apropriadas da tabela história são carregadas na memória. Estes *eventos de história* são então reproduzidos no código de função orquestrador para levá-lo de volta ao seu estado previamente posto de controlo. O uso da história da execução para reconstruir o estado desta forma é influenciado pelo [padrão de Sourcing](https://docs.microsoft.com/azure/architecture/patterns/event-sourcing)de Eventos.

## <a name="instances-table"></a>Tabela de instâncias

A tabela **Instances** é outra tabela de Armazenamento Azure que contém os estatutos de todas as instâncias de orquestração e entidadedentro de um centro de tarefas. À medida que os casos são criados, novas filas são adicionadas a esta tabela. A chave de partição desta tabela é a chave de identificação ou entidade da instância de orquestração e a chave da linha é uma constante fixa. Há uma linha por orquestração ou instância de entidade.

Esta tabela é utilizada para satisfazer pedidos `GetStatusAsync` de consulta `getStatus` de instâncias das APIs (.NET) e (JavaScript), bem como a consulta de [estado HTTP API](durable-functions-http-api.md#get-instance-status). É eventualmente consistente com o conteúdo da tabela **História** mencionado anteriormente. A utilização de uma tabela de armazenamento azure separada para satisfazer eficazmente as operações de consulta de instância desta forma é influenciada pelo padrão de Segregação de Responsabilidade de [Comando e Consulta (CQRS).](https://docs.microsoft.com/azure/architecture/patterns/cqrs)

## <a name="internal-queue-triggers"></a>Gatilhos de fila interna

As funções de orquestrador e as funções de atividade são ambas desencadeadas por filas internas no centro de tarefas da aplicação de funções. A utilização de filas desta forma fornece garantias fiáveis de entrega de mensagens "pelo menos uma vez". Existem dois tipos de filas em Funções Duráveis: a fila de **controlo** e a **fila do artigo de trabalho.**

### <a name="the-work-item-queue"></a>A fila do artigo de trabalho

Há uma fila de trabalho por centro de tarefas em Funções Duráveis. É uma fila básica e comporta-se `queueTrigger` da mesma forma que qualquer outra fila em Funções Azure. Esta fila é usada para desencadear *funções* de atividade apátrida, desfilando uma única mensagem de cada vez. Cada uma destas mensagens contém inputs de função de atividade e metadados adicionais, tais como qual função executar. Quando uma aplicação de Funções Duráveis se dimensiona para vários VMs, todos estes VMs competem para adquirir trabalho a partir da fila de artigos de trabalho.

### <a name="control-queues"></a>Fila de controlo(s)

Existem *múltiplas filas* de controlo por centro de tarefas em Funções Duráveis. Uma fila de *controlo* é mais sofisticada do que a fila de artigos de trabalho mais simples. As filas de controlo são usadas para desencadear as funções de orquestrador e entidade. Como os casos de função orquestradora e entidade são singletons imponentes, não é possível usar um modelo de consumidor concorrente para distribuir carga através de VMs. Em vez disso, as mensagens orquestradoras e entidades são equilibradas em todas as filas de controlo. Mais detalhes sobre este comportamento podem ser encontrados em secções subsequentes.

As filas de controlo contêm uma variedade de tipos de mensagens de ciclo de vida de orquestração. Exemplos incluem mensagens de [controlo de orquestração,](durable-functions-instance-management.md)mensagens de *resposta* de função de atividade e mensagens temporizadoras. Cerca de 32 mensagens serão retiradas de uma fila de controlo numa única sondagem. Estas mensagens contêm dados de carga útil, bem como metadados, incluindo a instância de orquestração a que se destina. Se várias mensagens dequeudas se destinarem à mesma instância de orquestração, serão processadas como um lote.

### <a name="queue-polling"></a>Sondagem de fila

A extensão de tarefa duradoura implementa um algoritmo de back-off exponencial aleatório para reduzir o efeito da sondagem de fila inativa nos custos de transação de armazenamento. Quando uma mensagem é encontrada, o tempo de execução verifica imediatamente outra mensagem; quando nenhuma mensagem é encontrada, espera por um período de tempo antes de tentar novamente. Após tentativas falhadas subsequentes de obter uma mensagem de fila, o tempo de espera continua a aumentar até atingir o tempo máximo de espera, que se desfaz em 30 segundos.

O atraso máximo de votação é configurável através da `maxQueuePollingInterval` propriedade no ficheiro [host.json](../functions-host-json.md#durabletask). A definição desta propriedade para um valor mais elevado pode resultar em tardios de processamento de mensagens mais elevados. As tardios mais elevadas só seriam esperadas após períodos de inatividade. A fixação deste imóvel para um valor mais baixo poderia resultar em custos de armazenamento mais elevados devido ao aumento das transações de armazenamento.

> [!NOTE]
> Ao executar os planos de consumo e premium de funções Azure, o Controlador de Escala de [Funções Azure](../functions-scale.md#how-the-consumption-and-premium-plans-work) irá fazer uma sondagem a cada controlo e fila de artigos de trabalho uma vez a cada 10 segundos. Esta sondagem adicional é necessária para determinar quando ativar as instâncias da aplicação de funções e tomar decisões de escala. No momento da escrita, este intervalo de 10 segundos é constante e não pode ser configurado.

## <a name="storage-account-selection"></a>Seleção de conta de armazenamento

As filas, tabelas e bolhas utilizadas pelas Funções Duráveis são criadas numa conta de Armazenamento Azure configurada. A conta a utilizar pode `durableTask/storageProvider/connectionStringName` ser especificada utilizando a definição (ou `durableTask/azureStorageConnectionStringName` regulação nas Funções Duráveis 1.x) no ficheiro **host.json.**

### <a name="durable-functions-2x"></a>Funções Duráveis 2.x

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

### <a name="durable-functions-1x"></a>Funções Duráveis 1.x

```json
{
  "extensions": {
    "durableTask": {
      "azureStorageConnectionStringName": "MyStorageAccountAppSetting"
    }
  }
}
```

Se não especificada, `AzureWebJobsStorage` a conta de armazenamento por defeito é utilizada. No entanto, para cargas de trabalho sensíveis ao desempenho, recomenda-se a configuração de uma conta de armazenamento não predefinida. Funções Duráveis utiliza fortemente o Armazenamento Azure, e a utilização de uma conta de armazenamento dedicada isola o uso do armazenamento das Funções Duráveis a partir do uso interno pelo hospedeiro funções Azure.

## <a name="orchestrator-scale-out"></a>Orchestrator scale-out

As funções de atividade são apátridas e dimensionadas automaticamente adicionando VMs. As funções e entidades orquestradoras, por outro lado, são *divididas* através de uma ou mais filas de controlo. O número de filas de controlo é definido no ficheiro **host.json.** O seguinte exemplo host.json snippet define a `durableTask/storageProvider/partitionCount` propriedade (ou `durableTask/partitionCount` em `3`Funções Duráveis 1.x) para .

### <a name="durable-functions-2x"></a>Funções Duráveis 2.x

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

### <a name="durable-functions-1x"></a>Funções Duráveis 1.x

```json
{
  "extensions": {
    "durableTask": {
      "partitionCount": 3
    }
  }
}
```

Um centro de tarefas pode ser configurado com entre 1 e 16 divisórias. Se não especificada, a contagem de divisórias por defeito é de **4**.

Ao escalonar para várias instâncias de acolhimento de funções (normalmente em VMs diferentes), cada instância adquire um bloqueio numa das filas de controlo. Estes cadeados são implementados internamente como arrendamentos de armazenamento de blob e garantem que uma instância ou entidade de orquestração funciona apenas numa única instância de hospedeiro de cada vez. Se um centro de tarefas for configurado com três filas de controlo, instâncias de orquestração e entidades podem ser equilibradas em três VMs. VMs adicionais podem ser adicionados para aumentar a capacidade de execução da função de atividade.

O diagrama que se segue ilustra como o hospedeiro das Funções Azure interage com as entidades de armazenamento num ambiente escalado.

![Diagrama de escala](./media/durable-functions-perf-and-scale/scale-diagram.png)

Como mostrado no diagrama anterior, todos os VMs competem por mensagens na fila do item de trabalho. No entanto, apenas três VMs podem adquirir mensagens a partir de filas de controlo, e cada VM bloqueia uma única fila de controlo.

Casos de orquestração e entidades são distribuídos por todos os casos de fila de controlo. A distribuição é feita apresentando a instância id da orquestração ou o nome da entidade e par chave. Os IDs de instância de orquestração por padrão são GUIDs aleatórios, garantindo que as instâncias são distribuídas igualmente por todas as filas de controlo.

De um modo geral, as funções de orquestrador destinam-se a ser leves e não devem exigir grandes quantidades de poder de computação. Por conseguinte, não é necessário criar um grande número de divisórias de fila de controlo para obter um grande rendimento para orquestrações. A maior parte do trabalho pesado deve ser feito em funções de atividade apátrida, que podem ser dimensionadas infinitamente.

## <a name="auto-scale"></a>Escala automática

Tal como acontece com todas as funções Azure em execução nos planos De consumo e elástico premium, as Funções Duráveis suportam a escala automática através do controlador de [escala Funções Azure](../functions-scale.md#runtime-scaling). O Controlador de Escala monitoriza a latência de todas as filas através da emissão periódica de comandos _de espreitar._ Com base nas tardios das mensagens espreitadas, o Controlador de Escala decidirá se adiciona ou remove Os VMs.

Se o Controlador de Escala determinar que as latenciências de mensagens de controlo são demasiado elevadas, irá adicionar instâncias vM até que a latência da mensagem diminua para um nível aceitável ou atinge a contagem de divisórias de fila de controlo. Da mesma forma, o Controlador de Escala adicionará continuamente instâncias vM se as latenciências de fila de trabalho forem elevadas, independentemente da contagem de divisórias.

> [!NOTE]
> Começando com as Funções Duráveis 2.0, as aplicações de função podem ser configuradas para serem executadas dentro de pontos finais de serviço protegidos vNET no plano Elástico Premium. Nesta configuração, as Funções Duráveis acionam pedidos de escala em vez do Controlador de Escala.

## <a name="thread-usage"></a>Utilização de fios

As funções de orquestrador são executadas num único fio para garantir que a execução pode ser determinística em muitas repetições. Devido a esta execução de um fio único, é importante que os fios de função orquestrador não executem tarefas intensivas de CPU, não façam I/O, ou bloqueiem por qualquer motivo. Qualquer trabalho que possa exigir I/O, bloqueio ou vários fios deve ser movido para funções de atividade.

As funções de atividade têm todos os mesmos comportamentos que funções regulares desencadeadas pela fila. Podem fazer i/O com segurança, executar operações intensivas cpu, e usar vários fios. Como os gatilhos de atividade são apátridas, podem escalar livremente para um número ilimitado de VMs.

As funções da entidade também são executadas num único fio e as operações são processadas de uma vez por cada vez. No entanto, as funções da entidade não têm quaisquer restrições sobre o tipo de código que pode ser executado.

## <a name="concurrency-throttles"></a>Aceleradores de condivisões

A Azure Functions suporta a execução de múltiplas funções simultaneamente numa única instância de aplicação. Esta execução simultânea ajuda a aumentar o paralelismo e minimiza o número de "arranques a frio" que uma aplicação típica irá experimentar ao longo do tempo. No entanto, a alta conmoedação pode esgotar os recursos do sistema por VM tais ligações de rede ou memória disponível. Dependendo das necessidades da aplicação de função, pode ser necessário acelerar a conmoeda por exemplo para evitar a possibilidade de ficar sem memória em situações de alta carga.

Os limites de condivisa de função de atividade, orquestrador e entidade podem ser configurados no ficheiro **host.json.** As configurações `durableTask/maxConcurrentActivityFunctions` relevantes são `durableTask/maxConcurrentOrchestratorFunctions` para funções de atividade e para funções orquestradoras e entidades.

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

No exemplo anterior, um máximo de 10 funções de orquestrador ou entidade e 10 funções de atividade podem funcionar num único VM simultaneamente. Se não especificado, o número de execuções de funções de orquestrador ou de função de orquestrador ou entidade é limitado a 10X o número de núcleos no VM.

> [!NOTE]
> Estas definições são úteis para ajudar a gerir a memória e o uso do CPU num único VM. No entanto, quando dimensionados em vários VMs, cada VM tem o seu próprio conjunto de limites. Estas configurações não podem ser usadas para controlar a moeda a nível global.

## <a name="extended-sessions"></a>Sessões alargadas

Sessões estendidas é um cenário que mantém orquestrações e entidades na memória mesmo depois de terminarem o processamento de mensagens. O efeito típico de permitir sessões prolongadas é reduzido em I/S contra a conta de Armazenamento Azure e uma melhor contribuição global.

Pode ativar sessões `durableTask/extendedSessionsEnabled` `true` prolongadas, definindo-a no ficheiro **host.json.** A `durableTask/extendedSessionIdleTimeoutInSeconds` definição pode ser usada para controlar quanto tempo uma sessão inativa será realizada na memória:

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

Existem duas desvantagens potenciais deste cenário para estar atento:

1. Há um aumento geral no uso da memória da aplicação de funções.
2. Pode haver uma diminuição geral da entrada se houver muitas execuções de funções de orquestrador, de curta duração ou de função de entidade.

Como exemplo, `durableTask/extendedSessionIdleTimeoutInSeconds` se for definido para 30 segundos, então um episódio de função de orquestrador de curta duração ou entidade que executa em menos de 1 segundo ainda ocupa a memória por 30 segundos. Conta também com `durableTask/maxConcurrentOrchestratorFunctions` a quota mencionada anteriormente, potencialmente impedindo que outras funções de orquestrador ou entidade funcionem.

Os efeitos específicos de sessões prolongadas sobre as funções de orquestrador e entidade são descritos nas secções seguintes.

### <a name="orchestrator-function-replay"></a>Repetição da função do orquestrador

Como mencionado anteriormente, as funções de orquestrador são reproduzidas usando o conteúdo da tabela **História.** Por predefinição, o código de função do orquestrador é repetido sempre que um lote de mensagens é desnorteado de uma fila de controlo. Mesmo que esteja a utilizar o padrão de saída de ventilador, de fã-in `Task.WhenAll` e esteja `context.df.Task.all` à espera que todas as tarefas sejam concluídas (por exemplo, utilizando em .NET ou no JavaScript), haverá repetições que ocorrem à medida que lotes de respostas de tarefas são processados ao longo do tempo. Quando as sessões prolongadas são ativadas, as instâncias de função orquestradora são mantidas na memória por mais tempo e novas mensagens podem ser processadas sem uma repetição completa do histórico.

A melhoria do desempenho das sessões prolongadas é observada com maior frequência nas seguintes situações:

* Quando há um número limitado de casos de orquestração em simultâneo.
* Quando as orquestrações têm um grande número de ações sequenciais (por exemplo, centenas de chamadas de função de atividade) que completam rapidamente.
* Quando as orquestrações se abanam e se desalegem um grande número de ações que completam ao mesmo tempo.
* Quando as funções de orquestrador precisam de processar mensagens grandes ou fazer qualquer processamento intensivo de dados da CPU.

Em todas as outras situações, normalmente não há melhoria de desempenho observável para funções orquestradoras.

> [!NOTE]
> Estas definições só devem ser utilizadas depois de uma função de orquestrador ter sido totalmente desenvolvida e testada. O comportamento de repetição agressiva padrão pode ser útil para detetar restrições de código de [função orquestrador](durable-functions-code-constraints.md) a quando o tempo de desenvolvimento, e é, portanto, desativado por padrão.

### <a name="entity-function-unloading"></a>Descarregamento de função da entidade

As funções da entidade processam até 20 operações num único lote. Assim que uma entidade termina o processamento de um lote de operações, persiste o seu estado e descarrega da memória. Pode atrasar a descarga de entidades da memória utilizando a definição de sessões estendidas. As entidades continuam a persistir as suas alterações de estado como antes, mas permanecem na memória durante o período de tempo configurado para reduzir o número de cargas do Armazenamento Azure. Esta redução de cargas do Armazenamento Azure pode melhorar o contributo global de entidades frequentemente acedidas.

## <a name="performance-targets"></a>Metas de desempenho

Ao planear utilizar Funções Duráveis para uma aplicação de produção, é importante considerar os requisitos de desempenho no início do processo de planeamento. Esta secção abrange alguns cenários básicos de utilização e os números máximos de entrada esperados.

* **Execução sequencial**de atividade : Este cenário descreve uma função orquestradora que executa uma série de funções de atividade uma após a outra. Assemelha-se mais à amostra de corrente de [função.](durable-functions-sequence.md)
* **Execução paralela**da atividade : Este cenário descreve uma função orquestradora que executa muitas funções de atividade em paralelo usando o padrão [Fan-out, Fan-in.](durable-functions-cloud-backup.md)
* **Processamento de resposta paralela**: Este cenário é a segunda metade do padrão [Fan-out, Fan-in.](durable-functions-cloud-backup.md) Centra-se no desempenho do fã-in. É importante notar que, ao contrário do fan-out, o fan-in é feito por uma única instância de função orquestradora, e, portanto, só pode correr em um único VM.
* **Processamento externo**de eventos : Este cenário representa uma única instância de função orquestradora que aguarda [eventos externos,](durable-functions-external-events.md)um de cada vez.
* **Processamento**de operação da entidade : Este cenário testa a rapidez com que uma _única_ [entidade contra-contador](durable-functions-entities.md) pode processar um fluxo constante de operações.

> [!TIP]
> Ao contrário dos fãs, as operações de fan-in estão limitadas a um único VM. Se a sua aplicação utilizar o padrão de fan-out, fan-in e estiver preocupado com o desempenho do fã-in, considere subdividir a função de atividade em [várias suborquestrações](durable-functions-sub-orchestrations.md).

A tabela que se segue mostra os números *máximos* de entrada esperados para os cenários previamente descritos. "Instance" refere-se a uma única instância de uma função orquestrador a funcionar num único VM pequeno ([A1](../../virtual-machines/sizes-previous-gen.md)) no Azure App Service. Em todos os casos, presume-se que [as sessões alargadas](#orchestrator-function-replay) estão habilitadas. Os resultados reais podem variar dependendo do trabalho de CPU ou De/S realizado pelo código de função.

| Cenário | Débito máximo |
|-|-|
| Execução de atividade sequencial | 5 atividades por segundo, por exemplo |
| Execução paralela de atividade (fan-out) | 100 atividades por segundo, por exemplo |
| Processamento de resposta paralela (fan-in) | 150 respostas por segundo, por exemplo |
| Processamento externo de eventos | 50 eventos por segundo, por exemplo |
| Processamento de operação de entidade | 64 operações por segundo |

> [!NOTE]
> Estes números são atuais a partir da versão v1.4.0 (GA) da extensão funções duráveis. Estes números podem mudar ao longo do tempo à medida que a funcionalidade amadurece e à medida que as otimizações são feitas.

Se não estiver a ver os números de entrada que espera e o seu CPU e o uso da memória parecersaudável, verifique se a causa está relacionada com [a saúde da sua conta de armazenamento](../../storage/common/storage-monitoring-diagnosing-troubleshooting.md#troubleshooting-guidance). A extensão das Funções Duráveis pode colocar carga significativa numa conta de Armazenamento Azure e cargas suficientemente elevadas podem resultar em estrangulamento da conta de armazenamento.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Conheça a recuperação de desastres e a geodistribuição](durable-functions-disaster-recovery-geo-distribution.md)
