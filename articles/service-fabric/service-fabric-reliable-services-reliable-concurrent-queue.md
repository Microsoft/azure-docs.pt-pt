---
title: ReliableConcurrentQueue nos recursos de infraestrutura do serviço do Azure
description: ReliableConcurrentQueue é uma fila de alto débito, que permite que enfileira paralela e retira.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: timlt
editor: raja,tyadam,masnider,vturecek
ms.assetid: 62857523-604b-434e-bd1c-2141ea4b00d1
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 5/1/2017
ms.author: aljo
ms.openlocfilehash: d4d399258ac1bd83fe4cfb46344576ca74e66f1e
ms.sourcegitcommit: 7f7c2fe58c6cd3ba4fd2280e79dfa4f235c55ac8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/25/2019
ms.locfileid: "56805142"
---
# <a name="introduction-to-reliableconcurrentqueue-in-azure-service-fabric"></a>Introdução ao ReliableConcurrentQueue nos recursos de infraestrutura do serviço do Azure
Fila do Reliable Concurrent é uma fila assíncrona, transacional e replicada qual simultaneidade de alto de funcionalidades para colocar em fila e tirar da fila de operações. Ele foi projetado para fornecer alto débito e baixa latência ao simplificar a ordenação de FIFO strict fornecida pela [fila do Reliable](https://msdn.microsoft.com/library/azure/dn971527.aspx) e em vez disso, fornece uma ordem de melhor esforço.

## <a name="apis"></a>APIs

|Fila em simultâneo                |Fila do Reliable Concurrent                                         |
|--------------------------------|------------------------------------------------------------------|
| void Enqueue(T item)           | Tarefa EnqueueAsync (tx ITransaction, T item)                       |
| bool TryDequeue (o resultado de T)  | Tarefa < ConditionalValue < T >> TryDequeueAsync (ITransaction TX, e.u.a.)  |
| int Count()                    | long Count()                                                     |

## <a name="comparison-with-reliable-queuehttpsmsdnmicrosoftcomlibraryazuredn971527aspx"></a>Comparação com [fila fiável](https://msdn.microsoft.com/library/azure/dn971527.aspx)

Fila do Reliable Concurrent é oferecida como uma alternativa ao [fila do Reliable](https://msdn.microsoft.com/library/azure/dn971527.aspx). Deve ser utilizado em casos onde o rigoroso de ordenação FIFO não é necessária, como garantir FIFO requer um compromisso com a simultaneidade.  [Fila do Reliable](https://msdn.microsoft.com/library/azure/dn971527.aspx) usa bloqueios para impor a ordenação FIFO, com no máximo uma transação de permissão para colocar em fila e no máximo uma transação de permissão para remover da fila cada vez. Em comparação, a fila do Reliable Concurrent relaxes a restrição de ordenação e permite que qualquer número transações simultâneas para intercalação seus colocar e tirar da fila de operações. Ordenação de melhor esforço é fornecido, no entanto a ordenação relativa de dois valores numa fila do Reliable Concurrent nunca pode ser garantida.

Fila do Reliable Concurrent fornece débito mais elevado e uma latência inferior à [fila do Reliable](https://msdn.microsoft.com/library/azure/dn971527.aspx) sempre que há várias transações simultâneas, enfileira a efetuar e/ou retirar.

Um exemplo de caso de utilização para o ReliableConcurrentQueue é o [fila de mensagens](https://en.wikipedia.org/wiki/Message_queue) cenário. Neste cenário, os produtores de mensagem de um ou mais criar e adicionar os itens na fila, e um ou mais consumidores de mensagem extraem as mensagens da fila e processá-las. Vários produtores e consumidores podem trabalhar de forma independente, usando transações simultâneas para processar a fila.

## <a name="usage-guidelines"></a>Diretrizes de utilização
* A fila de espera que os itens na fila tenham um período de retenção baixa. Ou seja, os itens não seriam permanecer na fila por muito tempo.
* A fila não garante a ordenação de FIFO strict.
* A fila não ler o seus próprio escritas. Se um item é enfileirado dentro de uma transação, não será visível para um dequeuer na mesma transação.
* Retira não estão isoladas umas das outras. Se o item *uma* for retirada da fila na transação *txnA*, mesmo que já *txnA* não está consolidada, item *A* não ficavam visíveis para uma simultâneas transação *txnB*.  Se *txnA* for anulada, *uma* se tornará visível para *txnB* imediatamente.
* *TryPeekAsync* comportamento pode ser implementado com um *TryDequeueAsync* e, em seguida, a anulação da transação. Um exemplo disso pode ser encontrado na seção de padrões de programação.
* A contagem é não transacional. Ele pode ser utilizado para ter uma ideia do número de elementos na fila, mas representa um ponto anterior no tempo e não pode ser utilizado.
* Não deve ser efetuado processamento dispendioso nos itens retirada da fila enquanto a transação está ativa, para evitar as transações de longa execução que podem ter um impacto no desempenho do sistema.

## <a name="code-snippets"></a>Trechos de código
Daremos uma olhada em alguns trechos de código e suas saídas esperadas. Manipulação de exceção é ignorada nesta secção.

### <a name="enqueueasync"></a>EnqueueAsync
Seguem-se alguns fragmentos de código para usar EnqueueAsync seguido por suas saídas esperadas.

- *Caso 1: Tarefa de colocar em fila única*

```
using (var txn = this.StateManager.CreateTransaction())
{
    await this.Queue.EnqueueAsync(txn, 10, cancellationToken);
    await this.Queue.EnqueueAsync(txn, 20, cancellationToken);

    await txn.CommitAsync();
}
```

Partem do princípio de que a tarefa foi concluída com êxito e que existe eram sem modificar a fila de transações simultâneas. O utilizador pode esperar que a fila contenha os itens em qualquer uma das ordens seguintes:

> 10, 20

> 20, 10


- *Caso 2: Colocar em fila de tarefas em paralelo*

```
// Parallel Task 1
using (var txn = this.StateManager.CreateTransaction())
{
    await this.Queue.EnqueueAsync(txn, 10, cancellationToken);
    await this.Queue.EnqueueAsync(txn, 20, cancellationToken);

    await txn.CommitAsync();
}

// Parallel Task 2
using (var txn = this.StateManager.CreateTransaction())
{
    await this.Queue.EnqueueAsync(txn, 30, cancellationToken);
    await this.Queue.EnqueueAsync(txn, 40, cancellationToken);

    await txn.CommitAsync();
}
```

Partem do princípio de que as tarefas concluídas com êxito, que as tarefas foi executada em paralelo e que não havia nenhum outras transações simultâneas modificar a fila. Sem inferência de tipos pode ser feita sobre a ordem dos itens na fila. Para este fragmento de código, os itens podem aparecer em qualquer uma do 4! possíveis ordenações.  A fila tentará manter os itens na ordem original (colocados em fila), mas pode ser forçada para os reordenar devido a operações simultâneas ou falhas.


### <a name="dequeueasync"></a>DequeueAsync
Seguem-se alguns fragmentos de código para usar TryDequeueAsync seguido as saídas esperadas. Partem do princípio de que a fila já está a ser preenchida com os seguintes itens na fila:
> 10, 20, 30, 40, 50, 60

- *Caso 1: Tarefa de remoções da fila única*

```
using (var txn = this.StateManager.CreateTransaction())
{
    await this.Queue.TryDequeueAsync(txn, cancellationToken);
    await this.Queue.TryDequeueAsync(txn, cancellationToken);
    await this.Queue.TryDequeueAsync(txn, cancellationToken);

    await txn.CommitAsync();
}
```

Partem do princípio de que a tarefa foi concluída com êxito e que existe eram sem modificar a fila de transações simultâneas. Uma vez que não existem inferência de tipos pode ser feita sobre a ordem dos itens na fila, qualquer três dos itens podem ser removidos da fila, por qualquer ordem. A fila tentará manter os itens na ordem original (colocados em fila), mas pode ser forçada para os reordenar devido a operações simultâneas ou falhas.  

- *Caso 2: Parallel remover da fila tarefas*

```
// Parallel Task 1
List<int> dequeue1;
using (var txn = this.StateManager.CreateTransaction())
{
    dequeue1.Add(await this.Queue.TryDequeueAsync(txn, cancellationToken)).val;
    dequeue1.Add(await this.Queue.TryDequeueAsync(txn, cancellationToken)).val;

    await txn.CommitAsync();
}

// Parallel Task 2
List<int> dequeue2;
using (var txn = this.StateManager.CreateTransaction())
{
    dequeue2.Add(await this.Queue.TryDequeueAsync(txn, cancellationToken)).val;
    dequeue2.Add(await this.Queue.TryDequeueAsync(txn, cancellationToken)).val;

    await txn.CommitAsync();
}
```

Partem do princípio de que as tarefas concluídas com êxito, que as tarefas foi executada em paralelo e que não havia nenhum outras transações simultâneas modificar a fila. Uma vez que não inferência de tipos que pode ser feita sobre a ordem dos itens na fila, as listas *dequeue1* e *dequeue2* cada conterá dois itens, por qualquer ordem.

O mesmo item será *não* aparecer nas duas listas. Por conseguinte, se tiver de dequeue1 *10*, *30*, teria dequeue2 *20*, *40*.

- *Caso 3: Remover da fila de ordenação com anulação de transação*

Abortar uma transação em trânsito com retira coloca que os itens de volta no cabeçalho da fila. A ordem na qual os itens são colocados novamente no cabeçalho da fila não é garantida. Vamos examinar o código a seguir:

```
using (var txn = this.StateManager.CreateTransaction())
{
    await this.Queue.TryDequeueAsync(txn, cancellationToken);
    await this.Queue.TryDequeueAsync(txn, cancellationToken);

    // Abort the transaction
    await txn.AbortAsync();
}
```
Partem do princípio de que os itens foram removidos da fila na seguinte ordem:
> 10, 20

Quando estamos anular a transação, os itens seriam adicionados novamente ao cabeçalho da fila em qualquer uma das ordens seguintes:
> 10, 20

> 20, 10

O mesmo acontece com todos os casos em que a transação não foi com êxito *consolidado*.

## <a name="programming-patterns"></a>Padrões de programação
Nesta secção, vamos dar uma examinada em programação alguns padrões que podem ser úteis com ReliableConcurrentQueue.

### <a name="batch-dequeues"></a>Retira do batch
A recomendado é de padrão de programação para a tarefa de consumidor para o batch seu retira em vez de efetuar um remover da fila cada vez. O utilizador pode optar por limitar atrasos entre todos os lotes ou o tamanho do lote. O fragmento de código seguinte mostra este modelo de programação.  Observe que, neste exemplo, o processamento é concluído após a transação for confirmada, portanto, se uma falha ocorrer durante o processamento, os itens não processados serão perdidos sem ter sido processada.  Em alternativa, o processamento pode ser feito no âmbito da transação, no entanto, isso pode ter um impacto negativo no desempenho e requer tratamento dos itens já processado.

```
int batchSize = 5;
long delayMs = 100;

while(!cancellationToken.IsCancellationRequested)
{
    // Buffer for dequeued items
    List<int> processItems = new List<int>();

    using (var txn = this.StateManager.CreateTransaction())
    {
        ConditionalValue<int> ret;

        for(int i = 0; i < batchSize; ++i)
        {
            ret = await this.Queue.TryDequeueAsync(txn, cancellationToken);

            if (ret.HasValue)
            {
                // If an item was dequeued, add to the buffer for processing
                processItems.Add(ret.Value);
            }
            else
            {
                // else break the for loop
                break;
            }
        }

        await txn.CommitAsync();
    }

    // Process the dequeues
    for (int i = 0; i < processItems.Count; ++i)
    {
        Console.WriteLine("Value : " + processItems[i]);
    }

    int delayFactor = batchSize - processItems.Count;
    await Task.Delay(TimeSpan.FromMilliseconds(delayMs * delayFactor), cancellationToken);
}
```

### <a name="best-effort-notification-based-processing"></a>Processamento baseado em notificações de melhor esforço
Outro padrão de programação interessante usa a API de contagem. Aqui, podemos implementar melhor esforço de processamento para a fila com base na notificação. A contagem de fila pode ser utilizada para limitar uma colocar em fila ou uma tarefa de dequeue.  Observe que como no exemplo anterior, uma vez que o processamento ocorre fora da transação, itens não processados podem ser perdidas se uma falha ocorre durante o processamento.

```
int threshold = 5;
long delayMs = 1000;

while(!cancellationToken.IsCancellationRequested)
{
    while (this.Queue.Count < threshold)
    {
        cancellationToken.ThrowIfCancellationRequested();

        // If the queue does not have the threshold number of items, delay the task and check again
        await Task.Delay(TimeSpan.FromMilliseconds(delayMs), cancellationToken);
    }

    // If there are approximately threshold number of items, try and process the queue

    // Buffer for dequeued items
    List<int> processItems = new List<int>();

    using (var txn = this.StateManager.CreateTransaction())
    {
        ConditionalValue<int> ret;

        do
        {
            ret = await this.Queue.TryDequeueAsync(txn, cancellationToken);

            if (ret.HasValue)
            {
                // If an item was dequeued, add to the buffer for processing
                processItems.Add(ret.Value);
            }
        } while (processItems.Count < threshold && ret.HasValue);

        await txn.CommitAsync();
    }

    // Process the dequeues
    for (int i = 0; i < processItems.Count; ++i)
    {
        Console.WriteLine("Value : " + processItems[i]);
    }
}
```

### <a name="best-effort-drain"></a>Drenagem de melhor esforço
Uma drenagem da fila não pode ser garantida devido à natureza da estrutura de dados em simultâneo.  É possível que, mesmo que não existem operações de utilizador na fila estiverem em trânsito, uma chamada em particular para TryDequeueAsync pode não devolver um item que foi anteriormente colocado em fila e confirmada.  É garantido que o item enfileirado *eventualmente* ficam visíveis para o remover da fila, no entanto sem um mecanismo de comunicação fora de banda, um consumidor independente não é possível saber que a fila atingiu um estado estável, mesmo se todos os produtores ter sido parado e novo não colocar em fila são permitidas operações. Portanto, a operação de drenagem é melhor esforço conforme implementado abaixo.

O utilizador deve parar todas as outra produtor e as tarefas de consumidor e para quaisquer transações em trânsito consolidar ou abortar, antes de tentar drenar a fila de espera.  Se o usuário souber o número esperado de itens na fila, eles podem configurar uma notificação que sinaliza que todos os itens têm foram removidos da fila.

```
int numItemsDequeued;
int batchSize = 5;

ConditionalValue ret;

do
{
    List<int> processItems = new List<int>();

    using (var txn = this.StateManager.CreateTransaction())
    {
        do
        {
            ret = await this.Queue.TryDequeueAsync(txn, cancellationToken);

            if(ret.HasValue)
            {
                // Buffer the dequeues
                processItems.Add(ret.Value);
            }
        } while (ret.HasValue && processItems.Count < batchSize);

        await txn.CommitAsync();
    }

    // Process the dequeues
    for (int i = 0; i < processItems.Count; ++i)
    {
        Console.WriteLine("Value : " + processItems[i]);
    }
} while (ret.HasValue);
```

### <a name="peek"></a>Pré-visualizar
ReliableConcurrentQueue não fornece o *TryPeekAsync* api. Os utilizadores podem obter a pré-visualização semântica com um *TryDequeueAsync* e, em seguida, a anulação da transação. Neste exemplo, retira são processados apenas se o valor do item for superior a *10*.

```
using (var txn = this.StateManager.CreateTransaction())
{
    ConditionalValue ret = await this.Queue.TryDequeueAsync(txn, cancellationToken);
    bool valueProcessed = false;

    if (ret.HasValue)
    {
        if (ret.Value > 10)
        {
            // Process the item
            Console.WriteLine("Value : " + ret.Value);
            valueProcessed = true;
        }
    }

    if (valueProcessed)
    {
        await txn.CommitAsync();    
    }
    else
    {
        await txn.AbortAsync();
    }
}
```

## <a name="must-read"></a>Tem de leitura
* [Início rápido do Reliable Services](service-fabric-reliable-services-quick-start.md)
* [Trabalhar com as Reliable Collections](service-fabric-work-with-reliable-collections.md)
* [Notificações do Reliable Services](service-fabric-reliable-services-notifications.md)
* [Cópia de segurança de serviços fiáveis e de restauro (recuperação após desastre)](service-fabric-reliable-services-backup-restore.md)
* [Configuração do Gestor de estado fiável](service-fabric-reliable-services-configuration.md)
* [Introdução ao serviços de API Web do Service Fabric](service-fabric-reliable-services-communication-webapi.md)
* [Utilização avançada dos Reliable Services modelo de programação](service-fabric-reliable-services-advanced-usage.md)
* [Referência para programadores para coleções fiáveis](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.data.collections.aspx)
