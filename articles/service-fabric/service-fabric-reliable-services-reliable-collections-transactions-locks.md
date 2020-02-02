---
title: Transações e modos de bloqueio em coleções confiáveis
description: O Azure Service Fabric o Gerenciador de estado confiável e as transações de coleções confiáveis e o bloqueio.
ms.topic: conceptual
ms.date: 5/1/2017
ms.custom: sfrev
ms.openlocfilehash: 5f7b3a4d43d35f0d2965dd33c8f69143f4b3a8f7
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/01/2020
ms.locfileid: "76938916"
---
# <a name="transactions-and-lock-modes-in-azure-service-fabric-reliable-collections"></a>Transações e modos de bloqueio no Azure Service Fabric coleções confiáveis

## <a name="transaction"></a>Transação

Uma transação é uma sequência de operações executadas como uma única unidade lógica de trabalho. Exibe as propriedades comuns de [ACID](https://en.wikipedia.org/wiki/ACID) *(atómico,* *consistência,* *isolamento,* *durabilidade)* das transações de base de dados:

* **Atomicidade**: uma transação deve ser uma unidade atômica de trabalho. Em outras palavras, todas as modificações de dados são executadas ou nenhuma delas é executada.
* **Consistência**: quando concluída, uma transação deve deixar todos os dados em um estado consistente. Todas as estruturas de dados internas devem estar corretas no final da transação.
* **Isolamento**: as modificações feitas por transações simultâneas devem ser isoladas das modificações feitas por quaisquer outras transações simultâneas. O nível de isolamento utilizado para uma operação dentro de um [ITransac é](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.data.itransaction?view=azure-dotnet) determinado pelo [IReliableState](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.data.ireliablestate?view=azure-dotnet) que executa a operação.
* **Durabilidade**: após a conclusão de uma transação, seus efeitos estão permanentemente em vigor no sistema. As modificações persistem mesmo no caso de uma falha do sistema.

### <a name="isolation-levels"></a>Níveis de isolamento

Nível de isolamento define o grau para o qual a transação deve ser isolada de modificações feitas por outras transações.
Há dois níveis de isolamento com suporte em coleções confiáveis:

* **Leitura repetível**: Especifica que as instruções não podem ler dados que foram modificados, mas que ainda não foram confirmados por outras transações e que nenhuma outra transação pode modificar dados que foram lidos pela transação atual até que a transação atual seja concluída.
* **Instantâneo**: Especifica que os dados lidos por qualquer instrução em uma transação são a versão transacionalmente consistente dos dados que existiam no início da transação.
  A transação pode reconhecer apenas as modificações de dados que foram confirmadas antes do início da transação.
  As modificações de dados feitas por outras transações após o início da transação atual não são visíveis para as instruções em execução na transação atual.
  O efeito é como se as instruções em uma transação obtiverem um instantâneo dos dados confirmados como existiam no início da transação.
  Os instantâneos são consistentes em coleções confiáveis.

As coleções confiáveis escolhem automaticamente o nível de isolamento a ser usado para uma determinada operação de leitura, dependendo da operação e da função da réplica no momento da criação da transação.
Veja a seguir a tabela que descreve os padrões de nível de isolamento para operações confiáveis de dicionário e fila.

| Operação \ função | Primária | Secundária |
| --- |:--- |:--- |
| Leitura de entidade única |Leitura repetida |Instantâneo |
| Enumeração, contagem |Instantâneo |Instantâneo |

> [!NOTE]
> Exemplos comuns para operações de entidade única são `IReliableDictionary.TryGetValueAsync`, `IReliableQueue.TryPeekAsync`.
> 

Tanto o Dicionário Fiável como o suporte fiável da fila *leia as suas escritas.*
Em outras palavras, qualquer gravação dentro de uma transação ficará visível para uma leitura a seguir que pertença à mesma transação.

## <a name="locks"></a>Bloqueios

Em coleções confiáveis, todas as transações implementam um bloqueio rigoroso de duas fases: uma transação não libera os bloqueios adquiridos até que a transação seja encerrada com uma anulação ou uma confirmação.

O Dicionário Fiável utiliza o bloqueio de nível de linha para todas as operações de entidade única.
A fila confiável compensa a simultaneidade para a propriedade FIFO transacional estrita.
A Fila Fiável utiliza fechaduras de nível de funcionamento que permitem uma transação com `TryPeekAsync` e/ou `TryDequeueAsync` e uma transação com `EnqueueAsync` de cada vez.
Observe que, para preservar FIFO, se uma `TryPeekAsync` ou `TryDequeueAsync` observar que a fila confiável está vazia, elas também bloquearão `EnqueueAsync`.

As operações de gravação sempre usam bloqueios exclusivos.
Para as operações de leitura, o bloqueio depende de alguns fatores:

- Qualquer operação de leitura feita com o isolamento snapshot está livre de bloqueios.
- Por padrão, qualquer operação de leitura repetida usa bloqueios compartilhados.
- No entanto, para qualquer operação de leitura que dê suporte à leitura repetida, o usuário pode solicitar um bloqueio de atualização em vez do bloqueio compartilhado.
Um bloqueio de atualização é um bloqueio assimétrico usado para evitar uma forma comum de deadlock que ocorre quando várias transações bloqueiam recursos para atualizações potenciais em um momento posterior.

A matriz de compatibilidade de bloqueio pode ser encontrada na tabela a seguir:

| Solicitação \ concedida | Nenhuma | Partilhado | Atualizar | Exclude |
| --- |:--- |:--- |:--- |:--- |
| Partilhado |Sem conflito |Sem conflito |Conflito |Conflito |
| Atualizar |Sem conflito |Sem conflito |Conflito |Conflito |
| Exclude |Sem conflito |Conflito |Conflito |Conflito |

O argumento do timeout em Reliable Collections APIs é usado para deteção de impasse.
Por exemplo, duas transações (T1 e T2) estão tentando ler e atualizar K1.
É possível que eles sejam bloqueados, pois ambos acabam tendo o bloqueio compartilhado.
Neste caso, uma ou ambas as operações vão esgotar-se. Este cenário, um bloqueio de atualização poderia evitar tal impasse.

## <a name="next-steps"></a>Passos seguintes

* [Trabalhar com as Reliable Collections](service-fabric-work-with-reliable-collections.md)
* [Notificações de Reliable Services](service-fabric-reliable-services-notifications.md)
* [Reliable Services backup e restauração (recuperação de desastre)](service-fabric-reliable-services-backup-restore.md)
* [Configuração do Gerenciador de estado confiável](service-fabric-reliable-services-configuration.md)
* [Referência do desenvolvedor para coleções confiáveis](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.data.collections.aspx)
