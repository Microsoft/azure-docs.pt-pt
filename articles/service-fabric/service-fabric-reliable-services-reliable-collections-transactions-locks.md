---
title: Transações e modos de bloqueio em coleções fiáveis
description: Azure Service Fabric Reliable State Manager and Reliable Collections Transactions and Locking.
ms.topic: conceptual
ms.date: 5/1/2017
ms.custom: sfrev
ms.openlocfilehash: 5f7b3a4d43d35f0d2965dd33c8f69143f4b3a8f7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76938916"
---
# <a name="transactions-and-lock-modes-in-azure-service-fabric-reliable-collections"></a>Transações e modos de bloqueio em Coleções Fiáveis de Tecido de Serviço Azure

## <a name="transaction"></a>Transação

Uma transação é uma sequência de operações realizadas como uma única unidade lógica de trabalho. Exibe as propriedades comuns de [ACID](https://en.wikipedia.org/wiki/ACID) *(atómico,* *consistência,* *isolamento,* *durabilidade)* das transações de base de dados:

* **Atomicidade**: Uma transação deve ser uma unidade atómica de trabalho. Por outras palavras, ou todas as suas modificações de dados são realizadas, ou nenhuma delas é realizada.
* **Consistência**: Quando concluída, uma transação deve deixar todos os dados num estado consistente. Todas as estruturas internas de dados devem estar corretas no final da transação.
* **Isolamento**: As modificações efetuadas por transações simultâneas devem ser isoladas das modificações efetuadas por quaisquer outras transações simultâneas. O nível de isolamento utilizado para uma operação dentro de um [ITransac é](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.data.itransaction?view=azure-dotnet) determinado pelo [IReliableState](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.data.ireliablestate?view=azure-dotnet) que executa a operação.
* **Durabilidade**: Após a conclusão de uma transação, os seus efeitos encontram-se permanentemente em vigor no sistema. As modificações persistem mesmo em caso de falha do sistema.

### <a name="isolation-levels"></a>Níveis de isolamento

O nível de isolamento define o grau em que a transação deve ser isolada das modificações efetuadas por outras transações.
Existem dois níveis de isolamento que são suportados em Coleções Fiáveis:

* **Leitura repetível**: Especifica que as declarações não podem ler dados que foram modificados mas ainda não cometidos por outras transações e que nenhuma outra transação pode modificar dados que foram lidos pela transação atual até que a transação atual termine.
* **Instantâneo**: Especifica que os dados lidos por qualquer declaração de uma transação são a versão transaccionalmente consistente dos dados que existiam no início da operação.
  A transação só pode reconhecer modificações de dados que foram cometidas antes do início da transação.
  As modificações de dados efetuadas por outras transações após o início da transação atual não são visíveis em declarações executadas na transação atual.
  O efeito é como se as declarações de uma transação obtivessem uma imagem instantânea dos dados comprometidos tal como existiam no início da operação.
  As fotos são consistentes em coleções fiáveis.

As Coleções Fiáveis escolhem automaticamente o nível de isolamento a utilizar para uma determinada operação de leitura, dependendo da operação e do papel da réplica no momento da criação da transação.
Segue-se a tabela que retrata os padrãos de nível de isolamento para operações fiáveis de Dicionário e Fila.

| Operação \ Papel | Primária | Secundária |
| --- |:--- |:--- |
| Leitura de entidade única |Leitura repetível |Instantâneo |
| Enumeração, Conde |Instantâneo |Instantâneo |

> [!NOTE]
> Exemplos comuns para `IReliableDictionary.TryGetValueAsync`operações de entidade única são , `IReliableQueue.TryPeekAsync`.
> 

Tanto o Dicionário Fiável como o suporte fiável da fila *leia as suas escritas.*
Por outras palavras, qualquer escrita dentro de uma transação será visível para uma seguinte leitura que pertence à mesma transação.

## <a name="locks"></a>Bloqueios

Em Coleções Fiáveis, todas as transações implementam rigorosos bloqueios de duas fases: uma transação não liberta os cadeados que adquiriu até que a transação termine com um aborto ou um compromisso.

O Dicionário Fiável utiliza o bloqueio de nível de linha para todas as operações de entidade única.
A fila fiável troca a moeda por propriedade rígida de Operações FIFO.
A Fila Fiável utiliza fechaduras de `TryPeekAsync` nível `TryDequeueAsync` de funcionamento que permitem uma transação com e/ou e uma transação com `EnqueueAsync` de cada vez.
Note que para preservar fIFO, se a `TryPeekAsync` ou `TryDequeueAsync` alguma vez observar `EnqueueAsync`que a fila fiável está vazia, também bloqueará .

As operações de escrita levam sempre fechaduras exclusivas.
Para as operações de leitura, o bloqueio depende de alguns fatores:

- Qualquer operação de leitura feita com o isolamento snapshot está livre de bloqueios.
- Qualquer operação de Leitura Repetível por padrão leva fechaduras partilhadas.
- No entanto, para qualquer operação de leitura que suporte a Leitura Repetível, o utilizador pode solicitar um bloqueio de atualização em vez do bloqueio partilhado.
Um bloqueio de atualização é um bloqueio assimétrico usado para evitar uma forma comum de impasse que ocorre quando várias transações bloqueiam recursos para potenciais atualizações mais tarde.

A matriz de compatibilidade do bloqueio pode ser encontrada na tabela seguinte:

| Pedido \ Concedido | Nenhuma | Partilhado | Atualizar | Exclusivo |
| --- |:--- |:--- |:--- |:--- |
| Partilhado |Sem conflitos |Sem conflitos |Conflito |Conflito |
| Atualizar |Sem conflitos |Sem conflitos |Conflito |Conflito |
| Exclusivo |Sem conflitos |Conflito |Conflito |Conflito |

O argumento do timeout em Reliable Collections APIs é usado para deteção de impasse.
Por exemplo, duas transações (T1 e T2) estão a tentar ler e atualizar o K1.
É possível que eles impossibilitem, porque ambos acabam por ter a fechadura partilhada.
Neste caso, uma ou ambas as operações vão esgotar-se. Este cenário, um bloqueio de atualização poderia evitar tal impasse.

## <a name="next-steps"></a>Passos seguintes

* [Trabalhar com as Reliable Collections](service-fabric-work-with-reliable-collections.md)
* [Notificações de Serviços Fiáveis](service-fabric-reliable-services-notifications.md)
* [Backup e restauro de Serviços Fiáveis (recuperação de desastres)](service-fabric-reliable-services-backup-restore.md)
* [Configuração fiável do Gestor do Estado](service-fabric-reliable-services-configuration.md)
* [Referência do desenvolvedor para Coleções Fiáveis](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.data.collections.aspx)
