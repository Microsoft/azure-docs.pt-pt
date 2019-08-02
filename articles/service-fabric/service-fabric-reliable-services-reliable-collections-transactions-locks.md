---
title: Transações e modos de bloqueio no Azure Service Fabric coleções confiáveis | Microsoft Docs
description: O Azure Service Fabric o Gerenciador de estado confiável e as transações de coleções confiáveis e o bloqueio.
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
editor: masnider,rajak
ms.assetid: 62857523-604b-434e-bd1c-2141ea4b00d1
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 5/1/2017
ms.author: atsenthi
ms.openlocfilehash: 8e77e488a3c0a40a714a0e8efffba0a2947454bf
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/29/2019
ms.locfileid: "68599322"
---
# <a name="transactions-and-lock-modes-in-azure-service-fabric-reliable-collections"></a>Transações e modos de bloqueio no Azure Service Fabric coleções confiáveis

## <a name="transaction"></a>Transação
Uma transação é uma sequência de operações executadas como uma única unidade lógica de trabalho.
Uma transação deve exibir as seguintes propriedades ACID. Esse https://technet.microsoft.com/library/ms190612)
* **Atomicidade**: Uma transação deve ser uma unidade atômica de trabalho. Em outras palavras, todas as modificações de dados são executadas ou nenhuma delas é executada.
* **Consistência**: Quando concluída, uma transação deve deixar todos os dados em um estado consistente. Todas as estruturas de dados internas devem estar corretas no final da transação.
* **Isolamento**: As modificações feitas por transações simultâneas devem ser isoladas das modificações feitas por quaisquer outras transações simultâneas. O nível de isolamento usado para uma operação em um ITransaction é determinado pelo IReliableState que executa a operação.
* **Durabilidade**: Após a conclusão de uma transação, seus efeitos estão permanentemente em vigor no sistema. As modificações persistem mesmo no caso de uma falha do sistema.

### <a name="isolation-levels"></a>Níveis de isolamento
Nível de isolamento define o grau para o qual a transação deve ser isolada de modificações feitas por outras transações.
Há dois níveis de isolamento com suporte em coleções confiáveis:

* **Leitura repetida**: Especifica que as instruções não podem ler dados que foram modificados, mas que ainda não foram confirmados por outras transações e que nenhuma outra transação pode modificar dados que foram lidos pela transação atual até que a transação atual seja concluída. Para obter mais detalhes, [https://msdn.microsoft.com/library/ms173763.aspx](https://msdn.microsoft.com/library/ms173763.aspx)consulte.
* **Instantâneo**: Especifica que os dados lidos por qualquer instrução em uma transação são a versão transacionalmente consistente dos dados que existiam no início da transação.
  A transação pode reconhecer apenas as modificações de dados que foram confirmadas antes do início da transação.
  As modificações de dados feitas por outras transações após o início da transação atual não são visíveis para as instruções em execução na transação atual.
  O efeito é como se as instruções em uma transação obtiverem um instantâneo dos dados confirmados como existiam no início da transação.
  Os instantâneos são consistentes em coleções confiáveis.
  Para obter mais detalhes, [https://msdn.microsoft.com/library/ms173763.aspx](https://msdn.microsoft.com/library/ms173763.aspx)consulte.

As coleções confiáveis escolhem automaticamente o nível de isolamento a ser usado para uma determinada operação de leitura, dependendo da operação e da função da réplica no momento da criação da transação.
Veja a seguir a tabela que descreve os padrões de nível de isolamento para operações confiáveis de dicionário e fila.

| Operação \ função | Primário | Secundário |
| --- |:--- |:--- |
| Leitura de entidade única |Leitura repetida |Instantâneo |
| Enumeração, contagem |Instantâneo |Instantâneo |

> [!NOTE]
> Exemplos comuns para operações de entidade única `IReliableDictionary.TryGetValueAsync`são `IReliableQueue.TryPeekAsync`,.
> 

O dicionário confiável e a fila confiável dão suporte à leitura de suas gravações.
Em outras palavras, qualquer gravação dentro de uma transação ficará visível para uma leitura a seguir que pertença à mesma transação.

## <a name="locks"></a>Bloqueios
Em coleções confiáveis, todas as transações implementam um bloqueio rigoroso de duas fases: uma transação não libera os bloqueios adquiridos até que a transação seja encerrada com uma anulação ou uma confirmação.

O dicionário confiável usa o bloqueio em nível de linha para todas as operações de entidade única.
A fila confiável compensa a simultaneidade para a propriedade FIFO transacional estrita.
A fila confiável usa bloqueios no nível da operação `TryPeekAsync` , permitindo uma `TryDequeueAsync` transação com e/ `EnqueueAsync` ou e uma transação com por vez.
Observe que para preservar FIFO, se um `TryPeekAsync` ou `TryDequeueAsync` algum dia observar que a fila confiável está vazia, eles também serão bloqueados `EnqueueAsync`.

As operações de gravação sempre usam bloqueios exclusivos.
Para operações de leitura, o bloqueio depende de alguns fatores.
Qualquer operação de leitura feita usando o isolamento de instantâneo é livre de bloqueio.
Por padrão, qualquer operação de leitura repetida usa bloqueios compartilhados.
No entanto, para qualquer operação de leitura que dê suporte à leitura repetida, o usuário pode solicitar um bloqueio de atualização em vez do bloqueio compartilhado.
Um bloqueio de atualização é um bloqueio assimétrico usado para evitar uma forma comum de deadlock que ocorre quando várias transações bloqueiam recursos para atualizações potenciais em um momento posterior.

A matriz de compatibilidade de bloqueio pode ser encontrada na tabela a seguir:

| Solicitação \ concedida | Nenhum | Partilhado | Atualizar | Exclude |
| --- |:--- |:--- |:--- |:--- |
| Partilhado |Sem conflito |Sem conflito |Conflito |Conflito |
| Atualizar |Sem conflito |Sem conflito |Conflito |Conflito |
| Exclude |Sem conflito |Conflito |Conflito |Conflito |

O argumento de tempo limite nas APIs de coleções confiáveis é usado para detecção de deadlock.
Por exemplo, duas transações (T1 e T2) estão tentando ler e atualizar K1.
É possível que eles sejam bloqueados, pois ambos acabam tendo o bloqueio compartilhado.
Nesse caso, uma ou ambas as operações atingirão o tempo limite.

Esse cenário de deadlock é um ótimo exemplo de como um bloqueio de atualização pode impedir deadlocks.

## <a name="next-steps"></a>Passos seguintes
* [Trabalhar com as Reliable Collections](service-fabric-work-with-reliable-collections.md)
* [Notificações de Reliable Services](service-fabric-reliable-services-notifications.md)
* [Reliable Services backup e restauração (recuperação de desastre)](service-fabric-reliable-services-backup-restore.md)
* [Configuração do Gerenciador de estado confiável](service-fabric-reliable-services-configuration.md)
* [Referência do desenvolvedor para coleções confiáveis](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.data.collections.aspx)

