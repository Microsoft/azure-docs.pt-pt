---
title: Fila de Concurrent fiável em tecido de serviço Azure
description: ReliableConcurrentQueue é uma fila de alta persponagem que permite filas paralelas e filas.
ms.topic: conceptual
ms.date: 5/1/2017
ms.openlocfilehash: a7115db8259fde0e87e53557ecef730f8e82d2fd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75462738"
---
# <a name="introduction-to-reliableconcurrentqueue-in-azure-service-fabric"></a>Introdução à FiávelFila Concurrentem o Tecido de Serviço Azure
A Fila Consiária Fiável é uma fila assíncrona, transacional e replicada que apresenta alta conmoedação para operações de fila e de fila. Foi projetado para fornecer alta entrada e baixa latência, relaxando a rígida encomenda fIFO fornecida pela [Fila Fiável](https://msdn.microsoft.com/library/azure/dn971527.aspx) e, em vez disso, fornece uma ordem de melhor esforço.

## <a name="apis"></a>APIs

|Fila Simultânea                |Fila do Reliable Concurrent                                         |
|--------------------------------|------------------------------------------------------------------|
| vazio Enqueue (artigo T)           | Tarefa EnqueueAsync (ITransac tx, item T)                       |
| bool TryDequeue (resultado t)  | Task< ConditionalValue < T > > TryDequeueAsync (ITransact tx)  |
| int Conde()                    | contagem longa()                                                     |

## <a name="comparison-with-reliable-queue"></a>Comparação com [fila fiável](https://msdn.microsoft.com/library/azure/dn971527.aspx)

A fila simultânea fiável é oferecida como uma alternativa à [fila fiável.](https://msdn.microsoft.com/library/azure/dn971527.aspx) Deve ser utilizado nos casos em que não seja necessária uma encomenda rigorosa da FIFO, uma vez que garantir a FIFO requer uma compensação com a moeda.  [A Fila Fiável](https://msdn.microsoft.com/library/azure/dn971527.aspx) utiliza fechaduras para impor a encomenda da FIFO, com no máximo uma transação permitida a enveredar e, no máximo, uma transação permitida a desfilar de cada vez. Em comparação, a Fila Consisenta Fiável relaxa a restrição de encomenda e permite que quaisquer transações simultâneas de número interleavem as suas operações de fila e de fila. A encomenda de melhor esforço é fornecida, no entanto, a ordem relativa de dois valores numa fila considua fiável nunca pode ser garantida.

A Fila Consiária Fiável proporciona uma maior frequência e latência inferior à [fila fiável](https://msdn.microsoft.com/library/azure/dn971527.aspx) sempre que existem múltiplas transações simultâneas que realizam filas e/ou filas.

Um caso de utilização de amostrapara a Fila fiável é o cenário de fila de [mensagens.](https://en.wikipedia.org/wiki/Message_queue) Neste cenário, um ou mais produtores de mensagens criam e adicionam itens à fila, e uma ou mais mensagens os consumidores retiram mensagens da fila e processam-nas. Vários produtores e consumidores podem trabalhar de forma independente, utilizando transações simultâneas para processar a fila.

## <a name="usage-guidelines"></a>Diretrizes de Utilização
* A fila espera que os itens na fila tenham um período de retenção baixo. Ou seja, os itens não ficariam na fila por muito tempo.
* A fila não garante a estrita encomenda de FIFO.
* A fila não lê os seus próprios escritos. Se um item for enquecido dentro de uma transação, não será visível para um dequeuer dentro da mesma transação.
* As filas não estão isoladas umas das outras. Se o ponto *A* for desnorteado na transação *txnA*, mesmo que *o txnA* não esteja comprometido, o ponto *A* não seria visível para uma transação simultânea *txnB*.  Se *o txnA* abortar, *A* tornar-se-á visível para *txnB* imediatamente.
* O comportamento *tryPeekAsync* pode ser implementado usando um *TryDequeueAsync* e, em seguida, abortando a transação. Um exemplo deste comportamento pode ser encontrado na secção Padrões de Programação.
* O conde não é transacional. Pode ser usado para ter uma ideia do número de elementos na fila, mas representa um ponto no tempo e não pode ser invocado.
* O processamento dispendioso dos itens despachantes não deve ser realizado enquanto a transação estiver ativa, para evitar transações de longo prazo que possam ter um impacto no desempenho no sistema.

## <a name="code-snippets"></a>Fragmentos de Código
Vejamos alguns códigos e as suas saídas esperadas. O manuseamento de exceções é ignorado nesta secção.

### <a name="instantiation"></a>Instalação
Criar uma instância de uma fila consiã fiável é semelhante a qualquer outra Coleção Fiável.

```csharp
IReliableConcurrentQueue<int> queue = await this.StateManager.GetOrAddAsync<IReliableConcurrentQueue<int>>("myQueue");
```

### <a name="enqueueasync"></a>EnqueueAsync
Aqui estão alguns códigos para a utilização do EnqueueAsync seguidos das suas saídas esperadas.

- *Caso 1: Tarefa única enfila*

```
using (var txn = this.StateManager.CreateTransaction())
{
    await this.Queue.EnqueueAsync(txn, 10, cancellationToken);
    await this.Queue.EnqueueAsync(txn, 20, cancellationToken);

    await txn.CommitAsync();
}
```

Assuma que a tarefa foi concluída com sucesso e que não houve transações simultâneas que alterassem a fila. O utilizador pode esperar que a fila contenha os itens em qualquer uma das seguintes encomendas:

> 10, 20
> 
> 20, 10


- *Caso 2: Tarefa paralela de enfila*

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

Assuma que as tarefas concluídas com sucesso, que as tarefas foram realizadas paralelamente, e que não houve outras transações simultâneas que modificassem a fila. Não se pode fazer qualquer inferência sobre a ordem dos itens na fila. Para este corte de código, os itens podem aparecer em qualquer um dos 4! possíveis encomendas.  A fila tentará manter os itens na ordem original (enqueued), mas pode ser forçado a reencomendá-los devido a operações ou falhas simultâneas.


### <a name="dequeueasync"></a>DequeueAsync
Aqui estão alguns códigos para usar TryDequeueAsync seguido sinuoso seguido das saídas esperadas. Assuma que a fila já está povoada com os seguintes itens na fila:
> 10, 20, 30, 40, 50, 60

- *Caso 1: Tarefa única de defila*

```
using (var txn = this.StateManager.CreateTransaction())
{
    await this.Queue.TryDequeueAsync(txn, cancellationToken);
    await this.Queue.TryDequeueAsync(txn, cancellationToken);
    await this.Queue.TryDequeueAsync(txn, cancellationToken);

    await txn.CommitAsync();
}
```

Assuma que a tarefa foi concluída com sucesso e que não houve transações simultâneas que alterassem a fila. Uma vez que não pode ser feita qualquer inferência sobre a ordem dos itens na fila, qualquer um dos três itens pode ser desordenado, em qualquer ordem. A fila tentará manter os itens na ordem original (enqueued), mas pode ser forçado a reencomendá-los devido a operações ou falhas simultâneas.  

- *Caso 2: Tarefa paralela de defila*

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

Assuma que as tarefas concluídas com sucesso, que as tarefas foram realizadas paralelamente, e que não houve outras transações simultâneas que modificassem a fila. Uma vez que não pode ser feita qualquer inferência sobre a ordem dos itens na fila, as listas *defila1* e *defila2* contêm cada um dos dois itens, por qualquer ordem.

O mesmo item *não* aparecerá em ambas as listas. Assim, se o dequeue1 tiver *10,* *30,* então o defilar2 teria *20,* *40*.

- *Caso 3: Defilar com Transação Abortada*

Abortar uma transação com filas de voo coloca os itens de volta na cabeça da fila. A ordem em que os itens são colocados de volta na cabeça da fila não está garantida. Vejamos o seguinte código:

```
using (var txn = this.StateManager.CreateTransaction())
{
    await this.Queue.TryDequeueAsync(txn, cancellationToken);
    await this.Queue.TryDequeueAsync(txn, cancellationToken);

    // Abort the transaction
    await txn.AbortAsync();
}
```
Assuma que os artigos foram desordenados na seguinte ordem:
> 10, 20

Quando abortamos a transação, os itens seriam adicionados de volta à cabeça da fila em qualquer uma das seguintes ordens:
> 10, 20
> 
> 20, 10

O mesmo acontece em todos os casos em que a transação não foi *comprometida*com sucesso.

## <a name="programming-patterns"></a>Padrões de Programação
Nesta secção, vamos olhar para alguns padrões de programação que podem ser úteis na utilização da ReliableConcurrentQueue.

### <a name="batch-dequeues"></a>Defilas de lote
Um padrão de programação recomendado é para a tarefa do consumidor emlotar as suas defilas em vez de realizar uma defila de cada vez. O utilizador pode optar por acelerar os atrasos entre cada lote ou o tamanho do lote. O seguinte código de corte mostra este modelo de programação. Esteja ciente, neste exemplo, que o processamento é feito após a transação ser cometida, pelo que se ocorrer uma falha durante o processamento, os itens não processados serão perdidos sem terem sido processados.  Em alternativa, o processamento pode ser feito dentro do âmbito da transação, no entanto pode ter um impacto negativo no desempenho e requer o manuseamento dos itens já processados.

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
Outro padrão de programação interessante usa a Contagem API. Aqui, podemos implementar o melhor tratamento baseado em notificação para a fila. A fila Count pode ser usada para estrangular uma fila ou uma tarefa de defila.  Note que, tal como no exemplo anterior, uma vez que o processamento ocorre fora da transação, podem perder-se itens não processados se ocorrer uma falha durante o processamento.

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

### <a name="best-effort-drain"></a>Drenagem de Melhor Esforço
Uma drenagem da fila não pode ser garantida devido à natureza simultânea da estrutura de dados.  É possível que, mesmo que nenhuma operação de utilizador na fila esteja a bordo, uma chamada específica para TryDequeueAsync pode não devolver um item que foi previamente enqueced e comprometido.  O item enqueued é garantido que *eventualmente* se tornará visível para desfilar, no entanto, sem um mecanismo de comunicação fora da banda, um consumidor independente não pode saber que a fila atingiu um estado estável, mesmo que todos os produtores tenham sido parados e não sejam permitidas novas operações de fila. Assim, a operação de drenagem é o melhor esforço, tal como implementado abaixo.

O utilizador deve parar todas as tarefas adicionais de produtor e consumidor e esperar que quaisquer transações a bordo cometam ou abortem, antes de tentar drenar a fila.  Se o utilizador souber o número esperado de itens na fila, pode configurar uma notificação que indique que todos os itens foram despacados.

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
A ReliableConcurrentQueue não fornece o *api TryPeekAsync.* Os utilizadores podem obter a esminásica espreitando usando um *TryDequeueAsync* e, em seguida, abortando a transação. Neste exemplo, as filas só são processadas se o valor do item for superior a *10*.

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
* [Serviços fiáveis arranque rápido](service-fabric-reliable-services-quick-start.md)
* [Trabalhar com as Reliable Collections](service-fabric-work-with-reliable-collections.md)
* [Notificações de Serviços Fiáveis](service-fabric-reliable-services-notifications.md)
* [Backup e Restauro de Serviços Fiáveis (Recuperação de Desastres)](service-fabric-reliable-services-backup-restore.md)
* [Configuração fiável do gestor do estado](service-fabric-reliable-services-configuration.md)
* [Começar com serviços de API de tecido de serviço](service-fabric-reliable-services-communication-webapi.md)
* [Utilização Avançada do Modelo de Programação de Serviços Fiáveis](service-fabric-reliable-services-advanced-usage.md)
* [Referência do desenvolvedor para coleções fiáveis](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.data.collections.aspx)
