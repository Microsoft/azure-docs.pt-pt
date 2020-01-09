---
title: Transações e modos de bloqueio em coleções confiáveis
description: O Azure Service Fabric o Gerenciador de estado confiável e as transações de coleções confiáveis e o bloqueio.
ms.topic: conceptual
ms.date: 5/1/2017
ms.openlocfilehash: f27381aa0979b37c759f66d0e873126edc006d6d
ms.sourcegitcommit: 003e73f8eea1e3e9df248d55c65348779c79b1d6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/02/2020
ms.locfileid: "75614184"
---
# <a name="transactions-and-lock-modes-in-azure-service-fabric-reliable-collections"></a>Transações e modos de bloqueio no Azure Service Fabric coleções confiáveis

## <a name="transaction"></a>Transação
Uma transação é uma sequência de operações executadas como uma única unidade lógica de trabalho.
Uma transação deve exibir as seguintes propriedades ACID. (consulte: https://technet.microsoft.com/library/ms190612)
* **Atomicidade**: uma transação deve ser uma unidade atômica de trabalho. Em outras palavras, todas as modificações de dados são executadas ou nenhuma delas é executada.
* **Consistência**: quando concluída, uma transação deve deixar todos os dados em um estado consistente. Todas as estruturas de dados internas devem estar corretas no final da transação.
* **Isolamento**: as modificações feitas por transações simultâneas devem ser isoladas das modificações feitas por quaisquer outras transações simultâneas. O nível de isolamento usado para uma operação em um ITransaction é determinado pelo IReliableState que executa a operação.
* **Durabilidade**: após a conclusão de uma transação, seus efeitos estão permanentemente em vigor no sistema. As modificações persistem mesmo no caso de uma falha do sistema.

### <a name="isolation-levels"></a>Níveis de isolamento
Nível de isolamento define o grau para o qual a transação deve ser isolada de modificações feitas por outras transações.
Há dois níveis de isolamento com suporte em coleções confiáveis:

* **Leitura repetível**: Especifica que as instruções não podem ler dados que foram modificados, mas que ainda não foram confirmados por outras transações e que nenhuma outra transação pode modificar dados que foram lidos pela transação atual até que a transação atual seja concluída. Para obter mais detalhes, consulte [https://msdn.microsoft.com/library/ms173763.aspx](https://msdn.microsoft.com/library/ms173763.aspx).
* **Instantâneo**: Especifica que os dados lidos por qualquer instrução em uma transação são a versão transacionalmente consistente dos dados que existiam no início da transação.
  A transação pode reconhecer apenas as modificações de dados que foram confirmadas antes do início da transação.
  As modificações de dados feitas por outras transações após o início da transação atual não são visíveis para as instruções em execução na transação atual.
  O efeito é como se as instruções em uma transação obtiverem um instantâneo dos dados confirmados como existiam no início da transação.
  Os instantâneos são consistentes em coleções confiáveis.
  Para obter mais detalhes, consulte [https://msdn.microsoft.com/library/ms173763.aspx](https://msdn.microsoft.com/library/ms173763.aspx).

As coleções confiáveis escolhem automaticamente o nível de isolamento a ser usado para uma determinada operação de leitura, dependendo da operação e da função da réplica no momento da criação da transação.
Veja a seguir a tabela que descreve os padrões de nível de isolamento para operações confiáveis de dicionário e fila.

| Operação \ função | Primária | Secundária |
| --- |:--- |:--- |
| Leitura de entidade única |Leitura repetida |Instantâneo |
| Enumeração, contagem |Instantâneo |Instantâneo |

> [!NOTE]
> Exemplos comuns para operações de entidade única são `IReliableDictionary.TryGetValueAsync`, `IReliableQueue.TryPeekAsync`.
> 

O dicionário confiável e a fila confiável dão suporte à leitura de suas gravações.
Em outras palavras, qualquer gravação dentro de uma transação ficará visível para uma leitura a seguir que pertença à mesma transação.

## <a name="locks"></a>Bloqueios
Em coleções confiáveis, todas as transações implementam um bloqueio rigoroso de duas fases: uma transação não libera os bloqueios adquiridos até que a transação seja encerrada com uma anulação ou uma confirmação.

O dicionário confiável usa o bloqueio em nível de linha para todas as operações de entidade única.
A fila confiável compensa a simultaneidade para a propriedade FIFO transacional estrita.
A fila confiável usa bloqueios no nível da operação, permitindo que uma transação com `TryPeekAsync` e/ou `TryDequeueAsync` e uma transação com `EnqueueAsync` de cada vez.
Observe que, para preservar FIFO, se uma `TryPeekAsync` ou `TryDequeueAsync` observar que a fila confiável está vazia, elas também bloquearão `EnqueueAsync`.

As operações de gravação sempre usam bloqueios exclusivos.
Para operações de leitura, o bloqueio depende de alguns fatores.
Qualquer operação de leitura feita usando o isolamento de instantâneo é livre de bloqueio.
Por padrão, qualquer operação de leitura repetida usa bloqueios compartilhados.
No entanto, para qualquer operação de leitura que dê suporte à leitura repetida, o usuário pode solicitar um bloqueio de atualização em vez do bloqueio compartilhado.
Um bloqueio de atualização é um bloqueio assimétrico usado para evitar uma forma comum de deadlock que ocorre quando várias transações bloqueiam recursos para atualizações potenciais em um momento posterior.

A matriz de compatibilidade de bloqueio pode ser encontrada na tabela a seguir:

| Solicitação \ concedida | Nenhuma | Partilhado | Atualizar | Exclude |
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

