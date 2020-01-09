---
title: Notificações de Reliable Services
description: Documentação conceitual para Service Fabric Reliable Services notificações para o Gerenciador de estado confiável e o dicionário confiável
author: mcoskun
ms.topic: conceptual
ms.date: 6/29/2017
ms.author: mcoskun
ms.openlocfilehash: 1f3239ea1da252ccd84c6572b562756c8fd1677d
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/03/2020
ms.locfileid: "75639569"
---
# <a name="reliable-services-notifications"></a>Notificações de Reliable Services
As notificações permitem que os clientes acompanhem as alterações que estão sendo feitas em um objeto no qual estão interessados. Dois tipos de objetos dão suporte a notificações: *Gerenciador de estado confiável* e *dicionário confiável*.

Os motivos comuns para usar notificações são:

* Compilando exibições materializadas, como índices secundários ou exibições filtradas agregadas do estado da réplica. Um exemplo é um índice classificado de todas as chaves no dicionário confiável.
* Envio de dados de monitoramento, como o número de usuários adicionados na última hora.

As notificações são acionadas como parte da aplicação de operações. Por isso, as notificações devem ser tratadas o mais rápido possível, e os eventos síncronos não devem incluir nenhuma operação dispendiosa.

## <a name="reliable-state-manager-notifications"></a>Notificações do Gerenciador de estado confiável
O Gerenciador de estado confiável fornece notificações para os seguintes eventos:

* Transação
  * Consolidação
* Gerenciador de estado
  * Reconstruir
  * Adição de um estado confiável
  * Remoção de um estado confiável

O Gerenciador de estado confiável controla as transações em andamento atuais. A única alteração no estado de transação que faz com que uma notificação seja acionada é uma transação sendo confirmada.

O Gerenciador de estado confiável mantém uma coleção de Estados confiáveis, como um dicionário confiável e uma fila confiável. O Gerenciador de estado confiável aciona as notificações quando esta coleção é alterada: um estado confiável é adicionado ou removido, ou toda a coleção é recriada.
A coleção do Gerenciador de estado confiável é recriada em três casos:

* Recuperação: quando uma réplica é iniciada, ela recupera seu estado anterior do disco. No final da recuperação, ele usa **NotifyStateManagerChangedEventArgs** para acionar um evento que contém o conjunto de Estados confiáveis recuperados.
* Cópia completa: antes que uma réplica possa ingressar no conjunto de configuração, ela deve ser compilada. Às vezes, isso requer uma cópia completa do estado do Gerenciador de estado confiável da réplica primária a ser aplicada à réplica secundária ociosa. O Gerenciador de estado confiável na réplica secundária usa **NotifyStateManagerChangedEventArgs** para acionar um evento que contém o conjunto de Estados confiáveis que ele adquiriu da réplica primária.
* Restauração: em cenários de recuperação de desastre, o estado da réplica pode ser restaurado de um backup por meio de **RestoreAsync**. Nesses casos, o Gerenciador de estado confiável na réplica primária usa **NotifyStateManagerChangedEventArgs** para acionar um evento que contém o conjunto de Estados confiáveis que ele restaurou do backup.

Para se registrar para notificações de transação e/ou notificações do Gerenciador de estado, você precisa se registrar com os eventos **transactionchanged** ou **StateManagerChanged** no Gerenciador de estado confiável. Um local comum para se registrar com esses manipuladores de eventos é o construtor de seu serviço com estado. Ao se registrar no construtor, você não perderá nenhuma notificação causada por uma alteração durante o tempo de vida do **IReliableStateManager**.

```csharp
public MyService(StatefulServiceContext context)
    : base(MyService.EndpointName, context, CreateReliableStateManager(context))
{
    this.StateManager.TransactionChanged += this.OnTransactionChangedHandler;
    this.StateManager.StateManagerChanged += this.OnStateManagerChangedHandler;
}
```

O manipulador de eventos **transactionchanged** usa **NotifyTransactionChangedEventArgs** para fornecer detalhes sobre o evento. Ele contém a propriedade Action (por exemplo, **NotifyTransactionChangedAction. Commit**) que especifica o tipo de alteração. Ele também contém a propriedade Transaction que fornece uma referência à transação que foi alterada.

> [!NOTE]
> Hoje, os eventos **transactionchanged** são gerados somente se a transação é confirmada. A ação é, então, igual a **NotifyTransactionChangedAction. Commit**. Mas, no futuro, os eventos podem ser gerados para outros tipos de alterações de estado de transação. É recomendável verificar a ação e processar o evento somente se ele for o que você espera.
> 
> 

Veja a seguir um exemplo de manipulador de eventos **transactionchanged** .

```csharp
private void OnTransactionChangedHandler(object sender, NotifyTransactionChangedEventArgs e)
{
    if (e.Action == NotifyTransactionChangedAction.Commit)
    {
        this.lastCommitLsn = e.Transaction.CommitSequenceNumber;
        this.lastTransactionId = e.Transaction.TransactionId;

        this.lastCommittedTransactionList.Add(e.Transaction.TransactionId);
    }
}
```

O manipulador de eventos **StateManagerChanged** usa **NotifyStateManagerChangedEventArgs** para fornecer detalhes sobre o evento.
**NotifyStateManagerChangedEventArgs** tem duas subclasses: **NotifyStateManagerRebuildEventArgs** e **NotifyStateManagerSingleEntityChangedEventArgs**.
Você usa a propriedade Action em **NotifyStateManagerChangedEventArgs** para converter **NotifyStateManagerChangedEventArgs** para a subclasse correta:

* **NotifyStateManagerChangedAction.Rebuild**: **NotifyStateManagerRebuildEventArgs**
* **NotifyStateManagerChangedAction. Add** e **NotifyStateManagerChangedAction. Remove**: **NotifyStateManagerSingleEntityChangedEventArgs**

Veja a seguir um exemplo de manipulador de notificação **StateManagerChanged** .

```csharp
public void OnStateManagerChangedHandler(object sender, NotifyStateManagerChangedEventArgs e)
{
    if (e.Action == NotifyStateManagerChangedAction.Rebuild)
    {
        this.ProcessStateManagerRebuildNotification(e);

        return;
    }

    this.ProcessStateManagerSingleEntityNotification(e);
}
```

## <a name="reliable-dictionary-notifications"></a>Notificações de dicionário confiável
O dicionário confiável fornece notificações para os seguintes eventos:

* Rebuild: chamado quando **ReliableDictionary** recuperou seu estado de um backup ou estado local recuperado ou copiado.
* Clear: chamado quando o estado de **ReliableDictionary** foi limpo por meio do método **ClearAsync** .
* Adicionar: chamado quando um item foi adicionado a **ReliableDictionary**.
* Atualização: chamado quando um item em **IReliableDictionary** foi atualizado.
* Remove: chamado quando um item em **IReliableDictionary** foi excluído.

Para obter notificações de dicionário confiáveis, você precisa se registrar com o manipulador de eventos **dictionarychanged** em **IReliableDictionary**. Um local comum para se registrar com esses manipuladores de eventos está na **ReliableStateManager. StateManagerChanged** adicionar notificação.
O registro quando **IReliableDictionary** é adicionado ao **IReliableStateManager** garante que você não perderá nenhuma notificação.

```csharp
private void ProcessStateManagerSingleEntityNotification(NotifyStateManagerChangedEventArgs e)
{
    var operation = e as NotifyStateManagerSingleEntityChangedEventArgs;

    if (operation.Action == NotifyStateManagerChangedAction.Add)
    {
        if (operation.ReliableState is IReliableDictionary<TKey, TValue>)
        {
            var dictionary = (IReliableDictionary<TKey, TValue>)operation.ReliableState;
            dictionary.RebuildNotificationAsyncCallback = this.OnDictionaryRebuildNotificationHandlerAsync;
            dictionary.DictionaryChanged += this.OnDictionaryChangedHandler;
        }
    }
}
```

> [!NOTE]
> **ProcessStateManagerSingleEntityNotification** é o método de exemplo que o exemplo de **OnStateManagerChangedHandler** anterior chama.
> 
> 

O código anterior define a interface **IReliableNotificationAsyncCallback** , juntamente com **dictionarychanged**. Como **NotifyDictionaryRebuildEventArgs** contém uma interface **IAsyncEnumerable** , que precisa ser enumerada de forma assíncrona, as notificações de recriação são acionadas por meio de **RebuildNotificationAsyncCallback** em vez de **OnDictionaryChangedHandler**.

```csharp
public async Task OnDictionaryRebuildNotificationHandlerAsync(
    IReliableDictionary<TKey, TValue> origin,
    NotifyDictionaryRebuildEventArgs<TKey, TValue> rebuildNotification)
{
    this.secondaryIndex.Clear();

    var enumerator = e.State.GetAsyncEnumerator();
    while (await enumerator.MoveNextAsync(CancellationToken.None))
    {
        this.secondaryIndex.Add(enumerator.Current.Key, enumerator.Current.Value);
    }
}
```

> [!NOTE]
> No código anterior, como parte do processamento da notificação de recompilação, primeiro o estado agregado mantido é limpo. Como a coleção confiável está sendo recriada com um novo estado, todas as notificações anteriores são irrelevantes.
> 
> 

O manipulador de eventos **dictionarychanged** usa **NotifyDictionaryChangedEventArgs** para fornecer detalhes sobre o evento.
**NotifyDictionaryChangedEventArgs** tem cinco subclasses. Use a propriedade Action em **NotifyDictionaryChangedEventArgs** para converter **NotifyDictionaryChangedEventArgs** para a subclasse correta:

* **NotifyDictionaryChangedAction.Rebuild**: **NotifyDictionaryRebuildEventArgs**
* **NotifyDictionaryChangedAction.Clear**: **NotifyDictionaryClearEventArgs**
* **NotifyDictionaryChangedAction. Add**: **NotifyDictionaryItemAddedEventArgs**
* **NotifyDictionaryChangedAction.Update**: **NotifyDictionaryItemUpdatedEventArgs**
* **NotifyDictionaryChangedAction.Remove**: **NotifyDictionaryItemRemovedEventArgs**

```csharp
public void OnDictionaryChangedHandler(object sender, NotifyDictionaryChangedEventArgs<TKey, TValue> e)
{
    switch (e.Action)
    {
        case NotifyDictionaryChangedAction.Clear:
            var clearEvent = e as NotifyDictionaryClearEventArgs<TKey, TValue>;
            this.ProcessClearNotification(clearEvent);
            return;

        case NotifyDictionaryChangedAction.Add:
            var addEvent = e as NotifyDictionaryItemAddedEventArgs<TKey, TValue>;
            this.ProcessAddNotification(addEvent);
            return;

        case NotifyDictionaryChangedAction.Update:
            var updateEvent = e as NotifyDictionaryItemUpdatedEventArgs<TKey, TValue>;
            this.ProcessUpdateNotification(updateEvent);
            return;

        case NotifyDictionaryChangedAction.Remove:
            var deleteEvent = e as NotifyDictionaryItemRemovedEventArgs<TKey, TValue>;
            this.ProcessRemoveNotification(deleteEvent);
            return;

        default:
            break;
    }
}
```

## <a name="recommendations"></a>Recomendações
* *Conclua* eventos de notificação o mais rápido possível.
* *Não* execute nenhuma operação cara (por exemplo, operações de e/s) como parte dos eventos síncronos.
* *Verifique o* tipo de ação antes de processar o evento. Novos tipos de ação podem ser adicionados no futuro.

Aqui estão algumas coisas para ter em mente:

* As notificações são acionadas como parte da execução de uma operação. Por exemplo, uma notificação de restauração é acionada como a última etapa de uma operação de restauração. Uma restauração não será concluída até que o evento de notificação seja processado.
* Como as notificações são acionadas como parte das operações de aplicação, os clientes veem apenas as notificações para operações confirmadas localmente. E como as operações são garantidas apenas para serem confirmadas localmente (em outras palavras, registradas), elas podem ou não ser desfeitas no futuro.
* No caminho de restauração, uma única notificação é acionada para cada operação aplicada. Isso significa que, se a transação T1 incluir Create (x), Delete (X) e Create (X), você receberá uma notificação para a criação de X, uma para a exclusão e outra para a criação novamente, nessa ordem.
* Para transações que contêm várias operações, as operações são aplicadas na ordem em que foram recebidas na réplica primária do usuário.
* Como parte do andamento do processamento falso, algumas operações podem ser desfeitas. As notificações são geradas para essas operações de desfazer, revertendo o estado da réplica de volta para um ponto estável. Uma diferença importante das notificações de desfazer é que os eventos que têm chaves duplicadas são agregados. Por exemplo, se a transação T1 estiver sendo desfeita, você verá uma única notificação a ser excluída (X).

## <a name="next-steps"></a>Passos seguintes
* [Reliable Collections](service-fabric-work-with-reliable-collections.md)
* [Início rápido de Reliable Services](service-fabric-reliable-services-quick-start.md)
* [Reliable Services backup e restauração (recuperação de desastre)](service-fabric-reliable-services-backup-restore.md)
* [Referência do desenvolvedor para coleções confiáveis](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.data.collections.aspx)

