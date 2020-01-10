---
title: ReliableConcurrentQueue no Azure Service Fabric
description: ReliableConcurrentQueue é uma fila de alta taxa de transferência que permite filas paralelas e remover filas.
ms.topic: conceptual
ms.date: 5/1/2017
ms.openlocfilehash: a7115db8259fde0e87e53557ecef730f8e82d2fd
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75462738"
---
# <a name="introduction-to-reliableconcurrentqueue-in-azure-service-fabric"></a>Introdução ao ReliableConcurrentQueue no Azure Service Fabric
A fila simultânea confiável é uma fila assíncrona, transacional e replicada que apresenta alta simultaneidade para operações enfileirar e remover da fila. Ele foi projetado para fornecer alta taxa de transferência e baixa latência, relaxando a ordem de FIFO estrita fornecida pela [fila confiável](https://msdn.microsoft.com/library/azure/dn971527.aspx) e, em vez disso, fornece uma ordem de melhor esforço.

## <a name="apis"></a>APIs

|Fila simultânea                |Fila do Reliable Concurrent                                         |
|--------------------------------|------------------------------------------------------------------|
| enfileirar void (T item)           | Tarefa EnqueueAsync (ITransaction TX, T item)                       |
| bool TryDequeue (resultado T de saída)  | Task < Condicionalvalue < T > > TryDequeueAsync (ITransaction TX)  |
| Contagem int ()                    | Contagem longa ()                                                     |

## <a name="comparison-with-reliable-queuehttpsmsdnmicrosoftcomlibraryazuredn971527aspx"></a>Comparação com [fila confiável](https://msdn.microsoft.com/library/azure/dn971527.aspx)

Uma fila simultânea confiável é oferecida como uma alternativa à [fila confiável](https://msdn.microsoft.com/library/azure/dn971527.aspx). Ele deve ser usado nos casos em que a ordem de FIFO estrita não é necessária, pois a garantia de FIFO requer uma compensação com simultaneidade.  A [fila confiável](https://msdn.microsoft.com/library/azure/dn971527.aspx) usa bloqueios para impor a ordenação de FIFO, com no máximo uma transação com permissão para enfileirar e no máximo uma transação permitida para remover a fila de cada vez. Em comparação, a fila simultânea confiável libera a restrição de ordenação e permite que quaisquer transações simultâneas de número intercalam suas operações de enfileiramento e remoção da fila. A ordem de melhor esforço é fornecida, no entanto, a ordenação relativa de dois valores em uma fila simultânea confiável nunca pode ser garantida.

A fila simultânea confiável fornece maior taxa de transferência e menor latência do que a [fila confiável](https://msdn.microsoft.com/library/azure/dn971527.aspx) sempre que houver várias transações simultâneas executando as filas e/ou as subfilas.

Um caso de uso de exemplo para o ReliableConcurrentQueue é o cenário de [fila de mensagens](https://en.wikipedia.org/wiki/Message_queue) . Nesse cenário, um ou mais produtores de mensagem criam e adicionam itens à fila, e um ou mais consumidores de mensagens efetuam pull de mensagens da fila e as processam. Vários produtores e consumidores podem trabalhar de forma independente, usando transações simultâneas para processar a fila.

## <a name="usage-guidelines"></a>Diretrizes de uso
* A fila espera que os itens na fila tenham um período de retenção baixo. Ou seja, os itens não permanecerão na fila por muito tempo.
* A fila não garante a ordenação de FIFO estrita.
* A fila não lê suas próprias gravações. Se um item for enfileirado em uma transação, ele não será visível para um desenfileirador dentro da mesma transação.
* As reas filas não são isoladas umas das outras. Se *o item A* for removido da fila na transação *txnA*, mesmo que *TxnA* não seja confirmado, *o item A* não estaria visível para uma transação simultânea *txnB*.  Se *txnA* abortar, *um* ficará visível para *txnB* imediatamente.
* O comportamento de *TryPeekAsync* pode ser implementado usando um *TryDequeueAsync* e, em seguida, anulando a transação. Um exemplo desse comportamento pode ser encontrado na seção padrões de programação.
* A contagem não é transacional. Ele pode ser usado para obter uma ideia do número de elementos na fila, mas representa um ponto no tempo e não pode ser confiável.
* O processamento dispendioso nos itens da fila não deve ser executado enquanto a transação estiver ativa, para evitar transações de longa execução que podem ter um impacto no desempenho do sistema.

## <a name="code-snippets"></a>Fragmentos de Código
Vamos examinar alguns trechos de código e suas saídas esperadas. O tratamento de exceção é ignorado nesta seção.

### <a name="instantiation"></a>Instalação
A criação de uma instância de uma fila simultânea confiável é semelhante a qualquer outra coleção confiável.

```csharp
IReliableConcurrentQueue<int> queue = await this.StateManager.GetOrAddAsync<IReliableConcurrentQueue<int>>("myQueue");
```

### <a name="enqueueasync"></a>EnqueueAsync
Aqui estão alguns trechos de código para usar EnqueueAsync seguidos por suas saídas esperadas.

- *Caso 1: tarefa de enfileiramento único*

```
using (var txn = this.StateManager.CreateTransaction())
{
    await this.Queue.EnqueueAsync(txn, 10, cancellationToken);
    await this.Queue.EnqueueAsync(txn, 20, cancellationToken);

    await txn.CommitAsync();
}
```

Suponha que a tarefa foi concluída com êxito e que não havia transações simultâneas modificando a fila. O usuário pode esperar que a fila contenha os itens em qualquer uma das seguintes ordens:

> 10, 20
> 
> 20, 10


- *Caso 2: tarefa de enfileiramento paralelo*

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

Suponha que as tarefas foram concluídas com êxito, que as tarefas foram executadas em paralelo e que não havia outras transações simultâneas modificando a fila. Nenhuma inferência pode ser feita sobre a ordem dos itens na fila. Para este trecho de código, os itens podem aparecer em qualquer um dos 4! ordenações possíveis.  A fila tentará manter os itens na ordem original (enfileirada), mas pode ser forçada a reordená-los devido a falhas ou operações simultâneas.


### <a name="dequeueasync"></a>DequeueAsync
Aqui estão alguns trechos de código para usar TryDequeueAsync seguidos pelas saídas esperadas. Suponha que a fila já esteja preenchida com os seguintes itens na fila:
> 10, 20, 30, 40, 50, 60

- *Caso 1: tarefa de remoção de uma única fila*

```
using (var txn = this.StateManager.CreateTransaction())
{
    await this.Queue.TryDequeueAsync(txn, cancellationToken);
    await this.Queue.TryDequeueAsync(txn, cancellationToken);
    await this.Queue.TryDequeueAsync(txn, cancellationToken);

    await txn.CommitAsync();
}
```

Suponha que a tarefa foi concluída com êxito e que não havia transações simultâneas modificando a fila. Como nenhuma inferência pode ser feita sobre a ordem dos itens na fila, qualquer um dos três itens pode ser removido da fila, em qualquer ordem. A fila tentará manter os itens na ordem original (enfileirada), mas pode ser forçada a reordená-los devido a falhas ou operações simultâneas.  

- *Caso 2: tarefa paralela de remoção da fila*

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

Suponha que as tarefas foram concluídas com êxito, que as tarefas foram executadas em paralelo e que não havia outras transações simultâneas modificando a fila. Como nenhuma inferência pode ser feita sobre a ordem dos itens na fila, as listas *dequeue1* e *dequeue2* terão cada um contendo dois itens, em qualquer ordem.

O mesmo item *não* será exibido em ambas as listas. Portanto, se dequeue1 tiver *10*, *30*, então dequeue2 teria *20*, *40*.

- *Caso 3: remover a ordem da fila com a anulação da transação*

A anulação de uma transação com as filas em andamento coloca os itens de volta no cabeçalho da fila. A ordem na qual os itens são colocados de volta no início da fila não é garantida. Vamos examinar o código a seguir:

```
using (var txn = this.StateManager.CreateTransaction())
{
    await this.Queue.TryDequeueAsync(txn, cancellationToken);
    await this.Queue.TryDequeueAsync(txn, cancellationToken);

    // Abort the transaction
    await txn.AbortAsync();
}
```
Suponha que os itens foram removidas da fila na seguinte ordem:
> 10, 20

Quando anulamos a transação, os itens seriam adicionados de volta ao cabeçalho da fila em qualquer um dos seguintes pedidos:
> 10, 20
> 
> 20, 10

O mesmo acontece com todos os casos em que a transação não foi *confirmada*com êxito.

## <a name="programming-patterns"></a>Padrões de programação
Nesta seção, vamos examinar alguns padrões de programação que podem ser úteis no uso do ReliableConcurrentQueue.

### <a name="batch-dequeues"></a>Remover filas do lote
Um padrão de programação recomendado é que a tarefa do consumidor execute em lote suas rerespectivas filas em vez de executar uma remoção da fila de cada vez. O usuário pode optar por limitar os atrasos entre cada lote ou o tamanho do lote. O trecho de código a seguir mostra esse modelo de programação. Lembre-se, neste exemplo, que o processamento é feito depois que a transação é confirmada, portanto, se ocorrer uma falha durante o processamento, os itens não processados serão perdidos sem terem sido processados.  Como alternativa, o processamento pode ser feito dentro do escopo da transação, no entanto, ele pode ter um impacto negativo no desempenho e requer o tratamento dos itens já processados.

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

### <a name="best-effort-notification-based-processing"></a>Processamento baseado em notificação de melhor esforço
Outro padrão de programação interessante usa a API de contagem. Aqui, podemos implementar o processamento com base em notificação de melhor esforço para a fila. A contagem de filas pode ser usada para limitar uma tarefa de enfileirar ou remover da fila.  Observe que, como no exemplo anterior, como o processamento ocorre fora da transação, os itens não processados poderão ser perdidos se ocorrer uma falha durante o processamento.

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

### <a name="best-effort-drain"></a>Dreno de melhor esforço
Uma descarga da fila não pode ser garantida devido à natureza simultânea da estrutura de dados.  É possível que, mesmo que nenhuma operação de usuário na fila esteja em andamento, uma chamada específica para TryDequeueAsync pode não retornar um item que foi anteriormente enfileirado e confirmado.  O item enfileirado tem garantia de *tornar-se visível para a* remoção da fila, no entanto, sem um mecanismo de comunicação fora de banda, um consumidor independente não pode saber que a fila atingiu um estado estável mesmo que todos os produtores tenham sido interrompidos e nenhuma nova operação de enfileiramento seja permitida. Portanto, a operação de descarga é o melhor esforço conforme implementado abaixo.

O usuário deve parar todas as tarefas de produtor e consumidor adicionais e aguardar que as transações em andamento sejam confirmadas ou anuladas antes de tentar drenar a fila.  Se o usuário souber o número esperado de itens na fila, ele poderá configurar uma notificação que sinalizará que todos os itens foram removidas da fila.

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
ReliableConcurrentQueue não fornece a API *TryPeekAsync* . Os usuários podem obter a semântica de Peek usando um *TryDequeueAsync* e, em seguida, anulando a transação. Neste exemplo, as reas filas são processadas somente se o valor do item for maior que *10*.

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
* [Guia de início rápido Reliable Services](service-fabric-reliable-services-quick-start.md)
* [Trabalhar com as Reliable Collections](service-fabric-work-with-reliable-collections.md)
* [Notificações de Reliable Services](service-fabric-reliable-services-notifications.md)
* [Reliable Services backup e restauração (recuperação de desastre)](service-fabric-reliable-services-backup-restore.md)
* [Configuração do Gerenciador de estado confiável](service-fabric-reliable-services-configuration.md)
* [Introdução com Service Fabric serviços de API Web](service-fabric-reliable-services-communication-webapi.md)
* [Uso avançado do modelo de programação de Reliable Services](service-fabric-reliable-services-advanced-usage.md)
* [Referência do desenvolvedor para coleções confiáveis](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.data.collections.aspx)
