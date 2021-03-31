---
title: ReliableConcurrentQueue em Tecido de Serviço Azure
description: ReliableConcurrentQueue é uma fila de alta produção que permite enquezas e deques paralelos.
ms.topic: conceptual
ms.date: 5/1/2017
ms.openlocfilehash: d6852982621d3efd3f4a8597a2959fceb13abd12
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98784296"
---
# <a name="introduction-to-reliableconcurrentqueue-in-azure-service-fabric"></a>Introdução à ReliableConcurrentQueue em Tecido de Serviço Azure
A Fila Simultânea fiável é uma fila assíncronea, transacional e replicada que apresenta alta concordância para operações de enquecidismo e deques. É projetado para fornecer alta produção e baixa latência, relaxando a rigorosa encomenda FIFO fornecida pela [Fila Fiável](/dotnet/api/microsoft.servicefabric.data.collections.ireliablequeue-1#microsoft_servicefabric_data_collections_ireliablequeue_1) e, em vez disso, proporciona uma encomenda de melhor esforço.

## <a name="apis"></a>APIs

|Fila simultânea                |Fila do Reliable Concurrent                                         |
|--------------------------------|------------------------------------------------------------------|
| vazio Enqueue(item T)           | Task EnqueueAsync (ITransaction tx, T item)                       |
| bool TryDequeue (resultado T out)  | < de tarefas Condicionais < T > > TryDequeueAsync (ITransaction tx)  |
| int Conde()                    | Contagem longa()                                                     |

## <a name="comparison-with-reliable-queue"></a>Comparação com [fila fiável](/dotnet/api/microsoft.servicefabric.data.collections.ireliablequeue-1#microsoft_servicefabric_data_collections_ireliablequeue_1)

A Fila Simultânea fiável é oferecida como uma alternativa à [Fila Fiável.](/dotnet/api/microsoft.servicefabric.data.collections.ireliablequeue-1#microsoft_servicefabric_data_collections_ireliablequeue_1) Deve ser utilizado nos casos em que não seja necessária uma encomenda rigorosa do FIFO, uma vez que a garantia da FIFO requer uma compensação com concordância.  [A Fila Fiável](/dotnet/api/microsoft.servicefabric.data.collections.ireliablequeue-1#microsoft_servicefabric_data_collections_ireliablequeue_1) utiliza fechaduras para impor a encomenda do FIFO, com, no máximo, uma transação permitida a encadear e, no máximo, uma transação permitida a dequear de cada vez. Em comparação, a Fila Simultânea fiável relaxa a restrição de encomenda e permite que qualquer número de transações simultâneas interligem as suas operações de enquecimento e deques. A encomenda de melhor esforço é fornecida, no entanto, a encomenda relativa de dois valores numa Fila Simultânea Fiável nunca pode ser garantida.

A Fila Simultânea fiável proporciona maior produção e menor latência do que [a Fila Fiável](/dotnet/api/microsoft.servicefabric.data.collections.ireliablequeue-1#microsoft_servicefabric_data_collections_ireliablequeue_1) sempre que existem várias transações simultâneas que realizam enques e/ou deques.

Um caso de utilização de amostras para o ReliableConcurrentQueue é o cenário [de Fila de Mensagens.](https://en.wikipedia.org/wiki/Message_queue) Neste cenário, um ou mais produtores de mensagens criam e adicionam itens à fila, e uma ou mais mensagens que os consumidores retiram mensagens da fila e processam-nas. Vários produtores e consumidores podem trabalhar de forma independente, utilizando transações simultâneas para processar a fila.

## <a name="usage-guidelines"></a>Diretrizes de utilização
* A fila espera que os itens na fila tenham um período de retenção baixo. Ou seja, os artigos não ficariam na fila por muito tempo.
* A fila não garante uma encomenda rigorosa do FIFO.
* A fila não lê os seus próprios escritos. Se um item for enfeitado dentro de uma transação, não será visível para um dequeuer dentro da mesma transação.
* Os deques não estão isolados um do outro. Se o item *A* for dequeu-se em *txnA* de transação, mesmo que a *txnA* não seja cometida, o ponto *A* não seria visível para uma transação simultânea *txnB*.  Se *o txnA* abortar, *A* tornar-se-á visível imediatamente para *txnB.*
* O comportamento *do TryPeekAsync* pode ser implementado utilizando um *TryDequeueAsync* e, em seguida, abortar a transação. Um exemplo deste comportamento pode ser encontrado na secção Padrões de Programação.
* O conde não é transacional. Pode ser usado para ter uma ideia do número de elementos na fila, mas representa um ponto no tempo e não pode ser confiado.
* O processamento dispendioso dos itens desossetados não deve ser realizado enquanto a transação estiver ativa, para evitar transações de longa duração que possam ter um impacto de desempenho no sistema.

## <a name="code-snippets"></a>Fragmentos de Código
Vejamos alguns cortes de código e as suas saídas esperadas. O tratamento de exceções é ignorado nesta secção.

### <a name="instantiation"></a>Instalação
Criar uma instância de uma fila concurrent fiável é semelhante a qualquer outra Coleção Fiável.

```csharp
IReliableConcurrentQueue<int> queue = await this.StateManager.GetOrAddAsync<IReliableConcurrentQueue<int>>("myQueue");
```

### <a name="enqueueasync"></a>EnqueueAsync
Aqui estão alguns cortes de código para usar o EnqueueAsync seguido pelas suas saídas esperadas.

- *Caso 1: Tarefa única de enquesto*

```
using (var txn = this.StateManager.CreateTransaction())
{
    await this.Queue.EnqueueAsync(txn, 10, cancellationToken);
    await this.Queue.EnqueueAsync(txn, 20, cancellationToken);

    await txn.CommitAsync();
}
```

Assuma que a tarefa foi concluída com sucesso e que não houve transações simultâneas que modificassem a fila. O utilizador pode esperar que a fila contenha os itens em qualquer uma das seguintes encomendas:

> 10, 20
> 
> 20, 10


- *Caso 2: Tarefa paralela de enquesta*

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

Assuma que as tarefas foram concluídas com sucesso, que as tarefas correram em paralelo, e que não havia outras transações simultâneas que alterassem a fila. Não se pode fazer nenhuma inferência sobre a ordem dos itens na fila. Para este corte de código, os itens podem aparecer em qualquer um dos 4! possíveis encomendas.  A fila tentará manter os itens na ordem original (enqueias), mas pode ser forçado a reordená-los devido a operações ou falhas simultâneas.


### <a name="dequeueasync"></a>DequeueAsync
Aqui estão alguns excertos de código para a utilização do TryDequeueAsync seguidos pelas saídas esperadas. Assuma que a fila já está povoada com os seguintes itens na fila:
> 10, 20, 30, 40, 50, 60

- *Caso 1: Tarefa de deques única*

```
using (var txn = this.StateManager.CreateTransaction())
{
    await this.Queue.TryDequeueAsync(txn, cancellationToken);
    await this.Queue.TryDequeueAsync(txn, cancellationToken);
    await this.Queue.TryDequeueAsync(txn, cancellationToken);

    await txn.CommitAsync();
}
```

Assuma que a tarefa foi concluída com sucesso e que não houve transações simultâneas que modificassem a fila. Uma vez que não pode ser feita nenhuma inferência sobre a ordem dos itens na fila, qualquer um dos três itens pode ser desresatado, em qualquer ordem. A fila tentará manter os itens na ordem original (enqueias), mas pode ser forçado a reordená-los devido a operações ou falhas simultâneas.  

- *Caso 2: Tarefa paralela de dequese*

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

Assuma que as tarefas foram concluídas com sucesso, que as tarefas correram em paralelo, e que não havia outras transações simultâneas que alterassem a fila. Uma vez que não pode ser feita nenhuma inferência sobre a ordem dos itens na fila, as listas *dequeue1* e *dequeue2* conterão cada um de dois itens, por qualquer ordem.

O mesmo item *não* aparecerá em ambas as listas. Assim, se a dequeue1 tiver *10,* *30,* então a dequeue2 teria *20,* *40*.

- *Caso 3: Dequeue encomendar com aborto de transação*

Abortar uma transação com deques a bordo coloca os itens de volta na cabeça da fila. A ordem pela qual os artigos são colocados de volta na cabeça da fila não está garantida. Vejamos o seguinte código:

```
using (var txn = this.StateManager.CreateTransaction())
{
    await this.Queue.TryDequeueAsync(txn, cancellationToken);
    await this.Queue.TryDequeueAsync(txn, cancellationToken);

    // Abort the transaction
    await txn.AbortAsync();
}
```
Suponha que os artigos foram desordenados na seguinte ordem:
> 10, 20

Quando abortarmos a transação, os itens seriam adicionados de volta à cabeça da fila em qualquer uma das seguintes encomendas:
> 10, 20
> 
> 20, 10

O mesmo acontece com todos os casos em que a transação não foi *comprometida* com sucesso.

## <a name="programming-patterns"></a>Padrões de programação
Nesta secção, vejamos alguns padrões de programação que podem ser úteis na utilização do ReliableConcurrentQueue.

### <a name="batch-dequeues"></a>LotEs Dequeues
Um padrão de programação recomendado é para o consumidor a tarefa de emaranar os seus deques em vez de realizar um dequeue de cada vez. O utilizador pode optar por acelerar os atrasos entre cada lote ou o tamanho do lote. O seguinte código snippet mostra este modelo de programação. Esteja ciente, neste exemplo, o processamento é feito após a transação ser cometida, por isso, se uma falha ocorrer durante o processamento, os itens não processados serão perdidos sem terem sido processados.  Em alternativa, o processamento pode ser feito dentro do âmbito da transação, no entanto pode ter um impacto negativo no desempenho e requer o manuseamento dos itens já processados.

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

### <a name="best-effort-notification-based-processing"></a>Processamento Best-Effort Notification-Based
Outro padrão de programação interessante usa a API conde. Aqui, podemos implementar o processamento baseado em notificação de melhor esforço para a fila. O Conde de fila pode ser usado para acelerar uma enqueue ou uma tarefa de dequeue.  Note que, tal como no exemplo anterior, uma vez que o processamento ocorre fora da transação, os itens não transformados podem ser perdidos se ocorrer uma falha durante o processamento.

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

### <a name="best-effort-drain"></a>Dreno Best-Effort
Um dreno da fila não pode ser garantido devido à natureza simultânea da estrutura de dados.  É possível que, mesmo que nenhuma operação de utilizador na fila esteja a bordo, uma chamada particular para TryDequeueAsync não pode devolver um item que foi previamente encadeado e comprometido.  O item enqueia o facto *de eventualmente* se tornar visível para dequear, no entanto, sem um mecanismo de comunicação fora de banda, um consumidor independente não pode saber que a fila atingiu um estado estável, mesmo que todos os produtores tenham sido parados e não sejam permitidas novas operações de enqueue. Assim, a operação de drenagem é o melhor esforço implementado abaixo.

O utilizador deve parar todas as outras tarefas de produtor e consumidor e aguardar que quaisquer transações a bordo cometam ou abortem, antes de tentar drenar a fila.  Se o utilizador souber o número esperado de itens na fila, pode configurar uma notificação que indique que todos os itens foram desoquedos.

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
ReliableConcurrentQueue não fornece a *api TryPeekAsync.* Os utilizadores podem obter o semântico espreitar usando um *TryDequeueAsync* e, em seguida, abortar a transação. Neste exemplo, os deques só são processados se o valor do artigo for superior a *10*.

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

## <a name="must-read"></a>Deve ler
* [Arranque rápido de serviços fiáveis](service-fabric-reliable-services-quick-start.md)
* [Trabalhar com as Reliable Collections](service-fabric-work-with-reliable-collections.md)
* [Notificações de Serviços Fiáveis](service-fabric-reliable-services-notifications.md)
* [Backup e Restauro de Serviços Fiáveis (Recuperação de Desastres)](service-fabric-reliable-services-backup-restore.md)
* [Configuração fiável do gestor de estado](service-fabric-reliable-services-configuration.md)
* [Começar com serviços de API Web fabric fabric](./service-fabric-reliable-services-communication-aspnetcore.md)
* [Utilização avançada do modelo de programação de serviços fiáveis](./service-fabric-reliable-services-lifecycle.md)
* [Referência do desenvolvedor para coleções fiáveis](/dotnet/api/microsoft.servicefabric.data.collections#microsoft_servicefabric_data_collections)
