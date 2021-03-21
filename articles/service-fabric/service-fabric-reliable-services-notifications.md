---
title: Notificações de Serviços Fiáveis
description: Documentação conceptual para notificações de serviços fiáveis de tecido de serviços para gestor estatal fiável e dicionário fiável
author: mcoskun
ms.topic: conceptual
ms.date: 6/29/2017
ms.author: mcoskun
ms.custom: devx-track-csharp
ms.openlocfilehash: f5b48cc6cca2e143c48ed7bdfc99de936be2a227
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98784584"
---
# <a name="reliable-services-notifications"></a>Notificações de Serviços Fiáveis
As notificações permitem que os clientes rastreiem as alterações que estão a ser feitas a um objeto em que estão interessados. Dois tipos de objetos suportam notificações: *Gestor de Estado fiável* e Dicionário *Fiável*.

As razões comuns para a utilização das notificações são:

* Construção de vistas materializadas, tais como índices secundários ou vistas filtradas agregadas do estado da réplica. Um exemplo é um índice classificado de todas as chaves no Dicionário Fiável.
* Envio de dados de monitorização, como o número de utilizadores adicionados na última hora.

As notificações são disparadas como parte da aplicação de operações. Por isso, as notificações devem ser tratadas o mais rapidamente possível, e eventos sincronizados não devem incluir operações caras.

## <a name="reliable-state-manager-notifications"></a>Notificações confiáveis do Gestor do Estado
O Gestor estatal fiável fornece notificações para os seguintes eventos:

* Transação
  * Consolidação
* Gestor estatal
  * Reconstruir
  * Adição de um estado fiável
  * Remoção de um estado fiável

O Gestor estatal fiável rastreia as transações atuais de voo. A única alteração no estado de transação que faz com que uma notificação seja disparada é uma transação a ser cometida.

O Gestor de Estado fiável mantém uma coleção de estados fiáveis como Dicionário Fiável e Fila Fiável. O Gestor de Estado fiável dispara notificações quando esta recolha muda: um estado fiável é adicionado ou removido, ou toda a coleção é reconstruída.
A coleção Reliable State Manager é reconstruída em três casos:

* Recuperação: Quando uma réplica começa, recupera o estado anterior do disco. No final da recuperação, utiliza **o NotifyStateManagerChangedEventArgs** para despedir um evento que contém o conjunto de estados de confiança recuperados.
* Cópia completa: Antes de uma réplica poder juntar-se ao conjunto de configuração, tem de ser construída. Por vezes, isto requer uma cópia completa do estado do Gestor de Estado fiável da réplica primária para ser aplicada à réplica secundária ociosa. O Gestor de Estado fiável na réplica secundária usa **NotifyStateManagerChangedEventArgs** para disparar um evento que contém o conjunto de estados fiáveis que adquiriu a partir da réplica primária.
* Restaurar: Em cenários de recuperação de desastres, o estado da réplica pode ser restaurado a partir de uma cópia de segurança via **RestoreAsync**. Nesses casos, o Reliable State Manager na réplica primária utiliza **o NotifyStateManagerChangedEventArgs** para disparar um evento que contém o conjunto de estados fiáveis que restaurou a partir da cópia de segurança.

Para se registar para notificações de transações e/ou notificações de gestor estatal, tem de se registar com os eventos **TransactionChanged** ou **StateManagerChanged** em Reliable State Manager. Um local comum para se registar com estes manipuladores de eventos é o construtor do seu serviço estatal. Quando se registar no construtor, não perderá nenhuma notificação causada por uma alteração durante a vida útil do **IReliableStateManager**.

```csharp
public MyService(StatefulServiceContext context)
    : base(MyService.EndpointName, context, CreateReliableStateManager(context))
{
    this.StateManager.TransactionChanged += this.OnTransactionChangedHandler;
    this.StateManager.StateManagerChanged += this.OnStateManagerChangedHandler;
}
```

O manipulador **de eventos TransactionChanged** usa **NotifyTransactionChangedEventArgs** para fornecer detalhes sobre o evento. Contém a propriedade de ação (por exemplo, **NotificaçãoTranstionChangedAction.Commit)** que especifica o tipo de alteração. Contém também a propriedade de transação que fornece uma referência à transação que mudou.

> [!NOTE]
> Hoje em dia, os eventos **TransactionChanged** só são levantados se a transação for comprometida. A ação é então igual a **NotIficaçãoTranstionChangedAction.Commit.** Mas no futuro, os eventos poderão ser levantados para outros tipos de alterações do estado de transações. Recomendamos verificar a ação e processar o evento apenas se for um evento que você espera.
> 
> 

Segue-se um exemplo **TransactionChanged** event handler.

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

O **manipulador de eventos StateManagerChanged** usa **NotifyStateManagerChangedEventArgs** para fornecer detalhes sobre o evento.
**NotifyStateManagerChangedEventArgs** tem duas subclasses: **NotifyStateManagerRebuildEventArgs** e **NotifyStateManagerSingleEntityChangedEventArgs**.
Utiliza a propriedade de ação em **NotifyStateManagerChangedEventArgs** para lançar **NotifyStateManagerChangedEventArgs** para a subclasse correta:

* **NotificaçãoStateManagerChangedAction.Rebuild:** **NotientManagerRebuildEventArgs**
* **NotificaçãoStateManagerChangedAction.Add** and **NotitionStateManagerChangedAction.Remove:** **NotifiqueStateManagerSingleEntityChangedEventArgs**

Segue-se um exemplo **de StateManagerChanged** notification handler.

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

## <a name="reliable-dictionary-notifications"></a>Notificações fiáveis do Dicionário
O Dicionário Fiável fornece notificações para os seguintes eventos:

* Reconstruir: Chamado quando **o ReliableDictionary** recuperou o seu estado de um estado local recuperado ou copiado ou de reserva.
* Claro: Chamado quando o estado de **ReliableDictionary** foi limpo através do método **ClearAsync.**
* Adicione: Chamado quando um item foi adicionado ao **ReliableDictionary**.
* Atualização: Chamada quando um item no **IReliableDictionary** foi atualizado.
* Remover: Chamado quando um item no **IReliableDictionary** foi eliminado.

Para obter notificações fiáveis do Dicionário, tem de se registar com o responsável pelo evento **Do Dicionário Em** **IReliableDictionary**. Um local comum para se registar com estes manipuladores de eventos está na notificação de adição **ReliableStateManager.StateManagerChanged.**
Registar-se quando **o IReliableDictionary** for adicionado ao **IReliableStateManager** garante que não perderá nenhuma notificação.

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
> **ProcessStateManagerSingleEntityNotification** é o método de amostra que o exemplo **onStateManagerChangedHandler** anterior chama.
> 
> 

O código anterior define a interface **IReliableNotificationAsyncCallback,** juntamente com **o DictionaryChanged**. Porque notificou as notificações **DesincitarárioRebuildEventArgs** contém uma interface **IAsyncEnumerable** -- que precisa de ser enumerada assíncroneamente -- as notificações de reconstrução são disparadas através da **RebuildNotificationAsyncCallback** em vez de **OnDictionaryChangedHandler**.

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
> No código anterior, como parte do processamento da notificação de reconstrução, primeiro o estado agregado mantido é apurado. Como a coleção fiável está a ser reconstruída com um estado novo, todas as notificações anteriores são irrelevantes.
> 
> 

O manipulador **de eventos DictionaryChanged** usa **NotifyDictionaryChangedEventArgs** para fornecer detalhes sobre o evento.
**NotificaçãoDaryChangedEventArgs** tem cinco subclasses. Utilize a propriedade de ação em **NotifyDictionaryChangedEventArgs** para lançar **NotidictionaryChangedEventArgs** para a subclasse correta:

* **NotificávelChangedAction.Rebuild:** **NotifyDictionaryRebuildEventArgs**
* **NotificávelChangedAction.Clear**: **NotitionaryClearEventArgs**
* **NotificávelChangedAction.Add:** **NotifyDictionaryItemAddedEventArgs**
* **NotificaçãoDictionaryChangedAction.Update**: **NotifyDictionaryItemUpdatedEventArgs**
* **NotificávelChangedAction.Remove:** **NotifyDictionaryItemRemovedEventArgs**

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
* *Complete* os eventos de notificação o mais rápido possível.
* *Não* execute operações dispendiosas (por exemplo, operações de E/S) como parte de eventos sincronizados.
* *Verifique* o tipo de ação antes de processar o evento. No futuro, poderão ser acrescentados novos tipos de ação.

Eis algumas coisas a ter em mente:

* As notificações são disparadas como parte da execução de uma operação. Por exemplo, uma notificação de restauro é disparada como o último passo de uma operação de restauro. Um restauro não terminará até que o evento de notificação seja processado.
* Como as notificações são disparadas como parte das operações de aplicação, os clientes vêem apenas notificações para operações comprometidas localmente. E porque as operações só são garantidas localmente (ou seja, registadas), podem ou não ser desfeitas no futuro.
* No caminho do redo, uma única notificação é disparada para cada operação aplicada. Isto significa que se a transação T1 incluir Create(X, Delete(X) e Create(X), receberá uma notificação para a criação de X, uma para a eliminação, e outra para a criação novamente, por essa ordem.
* Para transações que contenham múltiplas operações, as operações são aplicadas na ordem em que foram recebidas na réplica primária do utilizador.
* Como parte do processamento de falsos progressos, algumas operações podem ser desfeitas. As notificações são levantadas para tais operações de desfazer, revirando o estado da réplica para um ponto estável. Uma diferença importante das notificações de desfazer é que os eventos que têm chaves duplicadas são agregados. Por exemplo, se a transação T1 estiver a ser desfeita, verá uma única notificação para Apagar(X).

## <a name="next-steps"></a>Passos seguintes
* [Reliable Collections](service-fabric-work-with-reliable-collections.md)
* [Início rápido dos Serviços Fiáveis](service-fabric-reliable-services-quick-start.md)
* [Backup e restauro dos Serviços Fiáveis (recuperação de desastres)](service-fabric-reliable-services-backup-restore.md)
* [Referência do programador para Coleções Fiáveis](/dotnet/api/microsoft.servicefabric.data.collections#microsoft_servicefabric_data_collections)
