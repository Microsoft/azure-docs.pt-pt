---
title: Transações e Modos de Bloqueio em Coleções Fiáveis
description: Azure Service Fabric Reliable State Manager and Reliable Collections Transactions and Locking.
ms.topic: conceptual
ms.date: 5/1/2017
ms.openlocfilehash: 5d2cbb517ea5ca45697cd9124b82e9ef13dd32db
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/26/2021
ms.locfileid: "98784347"
---
# <a name="transactions-and-lock-modes-in-azure-service-fabric-reliable-collections"></a>Transações e modos de bloqueio em Azure Service Fabric Reliable Collections

## <a name="transaction"></a>Transação

Uma transação é uma sequência de operações realizadas como uma única unidade lógica de trabalho. Apresenta as propriedades [acid](https://en.wikipedia.org/wiki/ACID) comuns *(atomicidade,* *consistência,* *isolamento,* *durabilidade)* das transações de base de dados:

* **Atomicidade**: Uma transação deve ser uma unidade atómica de trabalho. Por outras palavras, ou todas as suas modificações de dados são realizadas, ou nenhuma delas é realizada.
* **Consistência**: Quando concluída, uma transação deve deixar todos os dados num estado consistente. Todas as estruturas internas de dados devem estar corretas no final da transação.
* **Isolamento:** As modificações efetuadas por transações simultâneas devem ser isoladas das modificações efetuadas por quaisquer outras transações simultâneas. O nível de isolamento utilizado para uma operação no âmbito de uma [ITransaction](/dotnet/api/microsoft.servicefabric.data.itransaction) é determinado pelo [Estado IReliable](/dotnet/api/microsoft.servicefabric.data.ireliablestate) que realiza a operação.
* **Durabilidade**: Após a conclusão de uma transação, os seus efeitos encontram-se permanentemente no sistema. As modificações persistem mesmo em caso de falha do sistema.

### <a name="isolation-levels"></a>Níveis de isolamento

O nível de isolamento define o grau em que a transação deve ser isolada das modificações efetuadas por outras transações.
Existem dois níveis de isolamento que são suportados em Coleções Fiáveis:

* **Repetível Read**: Especifica que as declarações não podem ler dados que foram modificados mas ainda não cometidos por outras transações e que nenhuma outra transação pode modificar dados que foram lidos pela transação em curso até que a transação atual termine.
* **Snapshot**: Especifica que os dados lidos por qualquer declaração numa transação é a versão transaccionalmente consistente dos dados que existiam no início da transação.
  A transação só pode reconhecer modificações de dados que foram cometidas antes do início da transação.
  As modificações de dados efetuadas por outras transações após o início da transação corrente não são visíveis às declarações executadas na transação em curso.
  O efeito é como se as declarações numa transação obtenham uma imagem instantânea dos dados cometidos tal como existiam no início da transação.
  As fotos são consistentes em coleções fiáveis.

As Coleções Fiáveis escolhem automaticamente o nível de isolamento a utilizar para uma determinada operação de leitura, dependendo da operação e do papel da réplica no momento da criação da transação.
Segue-se a tabela que retrata os padrãos de nível de isolamento para operações de Dicionário Fiável e Fila.

| Operação \ Função | Primário | Secundária |
| --- |:--- |:--- |
| Leitura de Entidade Única |Leitura repetível |Instantâneo |
| Enumeração, Contagem |Instantâneo |Instantâneo |

> [!NOTE]
> Exemplos comuns para Operações de Entidade Única `IReliableDictionary.TryGetValueAsync` são, `IReliableQueue.TryPeekAsync` . .
> 

Tanto o Dicionário Fiável como o suporte de fila fiável *lêem as suas escritas*.
Por outras palavras, qualquer escrita dentro de uma transação será visível a uma leitura seguinte que pertence à mesma transação.

## <a name="locks"></a>Bloqueios

Nas Coleções Fiáveis, todas as transações implementam um rigoroso bloqueio de duas fases: uma transação não liberta os cadeados que adquiriu até que a transação termine com um aborto ou um compromisso.

O Dicionário Fiável utiliza o bloqueio ao nível da linha para todas as operações de entidade única.
A Fila Fiável negoceia a conusncy por uma propriedade fifo transacional rigorosa.
A Fila Fiável utiliza bloqueios de nível de operação permitindo uma transação com `TryPeekAsync` e/ou `TryDequeueAsync` uma transação com `EnqueueAsync` cada vez.
Note que para preservar o FIFO, se um `TryPeekAsync` ou alguma vez observar que a Fila Fiável está `TryDequeueAsync` vazia, eles também vão bloquear `EnqueueAsync` .

As operações de escrita levam sempre fechaduras exclusivas.
Para operações de leitura, o bloqueio depende de alguns fatores:

- Qualquer operação de leitura feita com isolamento snapshot está isenta de bloqueio.
- Qualquer operação de Reposição Repetível por predefinição requer fechaduras partilhadas.
- No entanto, para qualquer operação de leitura que suporte a Leitura Repetível, o utilizador pode solicitar um bloqueio de Atualização em vez do bloqueio Partilhado.
Um bloqueio de atualização é um bloqueio assimétrico usado para evitar uma forma comum de impasse que ocorre quando várias transações bloqueiam recursos para potenciais atualizações numa altura posterior.

A matriz de compatibilidade do bloqueio pode ser encontrada na tabela seguinte:

| Pedido \ Concedido | Nenhum | Partilhado | Atualizar | Exclusivo |
| --- |:--- |:--- |:--- |:--- |
| Partilhado |Sem conflitos |Sem conflitos |Conflito |Conflito |
| Atualizar |Sem conflitos |Sem conflitos |Conflito |Conflito |
| Exclusivo |Sem conflitos |Conflito |Conflito |Conflito |

O argumento de tempo limite em APIs de Cobranças Fiáveis é utilizado para a deteção de impasses.
Por exemplo, duas transações (T1 e T2) estão a tentar ler e atualizar o K1.
É possível que fiquem num impasse, porque ambos acabam por ter a fechadura partilhada.
Neste caso, uma ou ambas as operações vão esgotar-se. Neste cenário, um bloqueio de Atualização poderia evitar tal impasse.

## <a name="next-steps"></a>Próximos passos

* [Trabalhar com as Reliable Collections](service-fabric-work-with-reliable-collections.md)
* [Notificações de Serviços Fiáveis](service-fabric-reliable-services-notifications.md)
* [Backup e restauro dos Serviços Fiáveis (recuperação de desastres)](service-fabric-reliable-services-backup-restore.md)
* [Configuração fiável do Gestor de Estado](service-fabric-reliable-services-configuration.md)
* [Referência do programador para Coleções Fiáveis](/dotnet/api/microsoft.servicefabric.data.collections#microsoft_servicefabric_data_collections)
