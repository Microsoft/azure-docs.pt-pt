---
title: Notificações de serviços fiáveis | Documentos da Microsoft
description: Documentação conceptual para notificações do Service Fabric Reliable Services
services: service-fabric
documentationcenter: .net
author: mcoskun
manager: chackdan
editor: masnider,vturecek
ms.assetid: cdc918dd-5e81-49c8-a03d-7ddcd12a9a76
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 6/29/2017
ms.author: mcoskun
ms.openlocfilehash: a3df5f28475b03f1799dc1e245c3a7e904b49cb3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60772929"
---
# <a name="reliable-services-notifications"></a>Notificações do Reliable Services
Notificações de permitir que os clientes controlar as alterações que estão a ser efetuadas a um objeto que elas têm interesse em. Notificações de suporte a dois tipos de objetos: *Gestor de estado de Reliable* e *dicionário fiável*.

Motivos comuns para usar notificações são:

* Criar vistas, tais como índices secundários materializadas ou agregados exibições filtradas do Estado da réplica. Um exemplo é um índice classificado de todas as chaves no dicionário fiável.
* Enviar dados de monitorização, como o número de utilizadores adicionados na última hora.

Notificações são acionadas como parte da aplicação de operações. Por isso, notificações devem ser manipuladas o mais rápido que eventos possíveis e síncronos não devem incluir quaisquer operações dispendiosas.

## <a name="reliable-state-manager-notifications"></a>Notificações do Reliable State Manager
Reliable State Manager fornece notificações para os seguintes eventos:

* Transação
  * Consolidação
* Gestor de estado
  * Reconstruir
  * Adição de um Estado fiável
  * Remoção de um Estado fiável

Reliable State Manager controla as transações em utilização atual. A única alteração no estado de transação que faz com que uma notificação para ser acionado é uma transação ser consolidada.

Reliable State Manager mantém uma coleção de Estados confiáveis, como o dicionário fiável e fila fiável. Reliable State Manager dispara notificações quando esta coleção é alterado: um Estado confiável é adicionado ou removido ou toda a coleção é reconstruída.
A coleção do Reliable State Manager for reconstruída em três casos:

* Recuperação de: Quando uma réplica é iniciado, ele recupera o estado anterior do disco. No final de recuperação, ele usa **NotifyStateManagerChangedEventArgs** disparar um evento que contém o conjunto de Estados fiáveis recuperados.
* Cópia completa: Antes de uma réplica pode associar o conjunto de configuração, tem de ser criada. Às vezes, isso requer uma cópia completa de estado de Reliable Gerenciador de estado a partir da réplica primária para ser aplicadas à réplica secundária inativa. Utiliza do Reliable State Manager na réplica secundária **NotifyStateManagerChangedEventArgs** disparar um evento que contém o conjunto de Estados confiáveis que adquiriu a partir da réplica primária.
* Restaure: Em cenários de recuperação após desastre, estado da réplica pode ser restaurado a partir de uma cópia de segurança por meio **RestoreAsync**. Nesses casos, Reliable State Manager, na réplica primária usa **NotifyStateManagerChangedEventArgs** disparar um evento que contém o conjunto de Estados confiáveis que restaurá-lo da cópia de segurança.

Para se registar notificações de transação e/ou notificações do Gestor de estado, precisa registrar com o **TransactionChanged** ou **StateManagerChanged** eventos no Reliable State Manager. Um lugar comum para registar com esses manipuladores de eventos é o construtor do seu serviço com estado. Quando se registra no construtor, não perder qualquer notificação que seja causada por uma alteração durante a duração de **IReliableStateManager**.

```csharp
public MyService(StatefulServiceContext context)
    : base(MyService.EndpointName, context, CreateReliableStateManager(context))
{
    this.StateManager.TransactionChanged += this.OnTransactionChangedHandler;
    this.StateManager.StateManagerChanged += this.OnStateManagerChangedHandler;
}
```

O **TransactionChanged** utiliza o manipulador de eventos **NotifyTransactionChangedEventArgs** para fornecer detalhes sobre o evento. Contém a propriedade action (por exemplo, **NotifyTransactionChangedAction.Commit**) que especifica o tipo de alteração. Também contém a propriedade de transação que fornece uma referência para a transação que foi alterada.

> [!NOTE]
> Hoje em dia, **TransactionChanged** eventos são gerados apenas se a transação será confirmada. A ação, em seguida, é igual a **NotifyTransactionChangedAction.Commit**. Mas, no futuro, os eventos podem ser gerados para outros tipos de alterações de estado de transação. Recomendamos a verificar a ação e processar o evento apenas se for um que espera.
> 
> 

Segue-se um exemplo **TransactionChanged** manipulador de eventos.

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

O **StateManagerChanged** utiliza o manipulador de eventos **NotifyStateManagerChangedEventArgs** para fornecer detalhes sobre o evento.
**NotifyStateManagerChangedEventArgs** tem duas subclasses: **NotifyStateManagerRebuildEventArgs** e **NotifyStateManagerSingleEntityChangedEventArgs**.
A propriedade em ação **NotifyStateManagerChangedEventArgs** converter **NotifyStateManagerChangedEventArgs** para a subclasse correta:

* **NotifyStateManagerChangedAction.Rebuild**: **NotifyStateManagerRebuildEventArgs**
* **NotifyStateManagerChangedAction.Add** e **NotifyStateManagerChangedAction.Remove**: **NotifyStateManagerSingleEntityChangedEventArgs**

Segue-se um exemplo **StateManagerChanged** controlador de notificação.

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

## <a name="reliable-dictionary-notifications"></a>Notificações de dicionário fiáveis
Dicionário fiável fornece notificações para os seguintes eventos:

* Recrie: Chamado quando **ReliableDictionary** recuperou seu estado de uma cópia de segurança ou de estado local recuperado ou copiado.
* Clara: Chamado quando o estado da **ReliableDictionary** foi limpo por meio do **ClearAsync** método.
* Adicionar: Chamado quando um item foi adicionado ao **ReliableDictionary**.
* Actualização: Chamado quando um item numa **IReliableDictionary** foi atualizado.
* Remova: Chamado quando um item numa **IReliableDictionary** foi eliminado.

Para obter notificações de dicionário fiável, precisa registrar com o **DictionaryChanged** manipulador de eventos no **IReliableDictionary**. Um lugar comum para registar com esses manipuladores de eventos está no **ReliableStateManager.StateManagerChanged** adicionar notificação.
Quando a registar **IReliableDictionary** é adicionado ao **IReliableStateManager** garante que não perder quaisquer notificações.

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
> **ProcessStateManagerSingleEntityNotification** é o método de exemplo que precedente **OnStateManagerChangedHandler** chamadas de exemplo.
> 
> 

Os conjuntos de código anterior a **IReliableNotificationAsyncCallback** interface, juntamente com **DictionaryChanged**. Uma vez **NotifyDictionaryRebuildEventArgs** contém uma **IAsyncEnumerable** interface – que tem de ser enumerado de forma assíncrona, notificações de reconstrução são disparadas por meio de  **RebuildNotificationAsyncCallback** em vez de **OnDictionaryChangedHandler**.

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
> Em primeiro lugar no código anterior, como parte de processar a notificação de reconstrução, é limpa o estado agregado de manter. Uma vez que a coleção fiável está a ser reconstruída com um novo Estado, todas as notificações anteriores são irrelevantes.
> 
> 

O **DictionaryChanged** utiliza o manipulador de eventos **NotifyDictionaryChangedEventArgs** para fornecer detalhes sobre o evento.
**NotifyDictionaryChangedEventArgs** tem cinco subclasses. Utilize a propriedade de ação na **NotifyDictionaryChangedEventArgs** converter **NotifyDictionaryChangedEventArgs** para a subclasse correta:

* **NotifyDictionaryChangedAction.Rebuild**: **NotifyDictionaryRebuildEventArgs**
* **NotifyDictionaryChangedAction.Clear**: **NotifyDictionaryClearEventArgs**
* **NotifyDictionaryChangedAction.Add** e **NotifyDictionaryChangedAction.Remove**: **NotifyDictionaryItemAddedEventArgs**
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
* *Fazer* concluir os eventos de notificação mais rápido possível.
* *Isso não é possível* executar quaisquer operações caras (por exemplo, operações de e/s) como parte de eventos síncronos.
* *Fazer* verificar o tipo de ação antes de processar o evento. Novos tipos de ação podem ser adicionados no futuro.

Seguem-se alguns aspetos a ter em mente:

* Notificações são acionadas como parte da execução de uma operação. Por exemplo, uma notificação de restauro é disparada como última etapa de uma operação de restauro. Um restauro não será concluída até que o evento de notificação é processado.
* Porque as notificações são disparadas como parte das operações a aplicar, os clientes ver apenas notificações para operações confirmadas localmente. E, como operações são garantidas apenas localmente consolidada (em outras palavras, com sessão iniciada), pode ou não ser anuladas no futuro.
* O caminho de Refazer, uma única notificação é acionada para cada operação aplicada. Isso significa que se a transação T1 inclui Create(X) Delete(X) e Create(X), receberá uma notificação para a criação de X, um para a eliminação e outro para a criação novamente, nessa ordem.
* Para as transações que contêm várias operações, operações são aplicadas pela ordem em que foram recebidos na réplica primária do usuário.
* Como parte do processamento de progresso FALSO, algumas operações podem ser anuladas. Notificações são geradas para essas operações de desfazer, reverter o estado da réplica para um ponto estável. Uma diferença importante de notificações de desfazer é que os eventos que têm chaves duplicadas são agregados. Por exemplo, se está a ser anulada a transação T1, verá uma notificação única para Delete(X).

## <a name="next-steps"></a>Passos Seguintes
* [Reliable Collections](service-fabric-work-with-reliable-collections.md)
* [Início rápido de serviços fiável](service-fabric-reliable-services-quick-start.md)
* [Cópia de segurança de serviços fiável e de restauro (recuperação após desastre)](service-fabric-reliable-services-backup-restore.md)
* [Referência do desenvolvedor do Reliable Collections](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.data.collections.aspx)

