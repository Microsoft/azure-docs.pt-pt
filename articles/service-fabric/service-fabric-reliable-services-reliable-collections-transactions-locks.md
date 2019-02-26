---
title: Transações e modos de bloqueio no Azure Service Fabric Reliable Collections | Documentos da Microsoft
description: Gestor de estado do Azure Service Fabric confiável e transações de coleções fiáveis e de bloqueio.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: timlt
editor: masnider,rajak
ms.assetid: 62857523-604b-434e-bd1c-2141ea4b00d1
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 5/1/2017
ms.author: aljo
ms.openlocfilehash: 246606792797afe0e57dbe2e582c4e94df3133d4
ms.sourcegitcommit: 7f7c2fe58c6cd3ba4fd2280e79dfa4f235c55ac8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/25/2019
ms.locfileid: "56806441"
---
# <a name="transactions-and-lock-modes-in-azure-service-fabric-reliable-collections"></a>Transações e modos de bloqueio no Azure Service Fabric Reliable Collections

## <a name="transaction"></a>Transação
Uma transação é uma sequência de operações executadas como uma unidade lógica única de trabalho.
Uma transação deve apresentar as seguintes propriedades ACID. (consulte: https://technet.microsoft.com/library/ms190612)
* **Atomicidade**: Uma transação tem de ser uma unidade atômica de trabalho. Em outras palavras, ou todas as suas modificações de dados são executadas ou nenhum deles é executada.
* **Consistência**: Quando concluída, uma transação tem de deixar todos os dados num estado consistente. Todas as estruturas de dados interno tem de ser corretas no final da transação.
* **Isolamento**: Modificações feitas por transações simultâneas tem de ser isoladas das modificações feitas por quaisquer outras transações simultâneas. O nível de isolamento utilizado para uma operação dentro de um ITransaction é determinado pelo IReliableState efetuar a operação.
* **Durabilidade**: Depois de concluída a uma transação, seus efeitos são permanentemente no local no sistema. As modificações persistem, mesmo em caso de uma falha de sistema.

### <a name="isolation-levels"></a>Níveis de isolamento
Nível de isolamento define o grau a que a transação tem de ser isolada de modificações feitas por outras transações.
Existem dois níveis de isolamento que são suportadas no Reliable Collections:

* **Leitura repetível**: Especifica que declarações não é possível ler os dados que foi modificados, mas ainda não consolidados por outras transações e que não existem outras transações podem modificar os dados que tenham sido lidos pela transação atual até que termine a transação atual. Para obter mais detalhes, consulte [ https://msdn.microsoft.com/library/ms173763.aspx ](https://msdn.microsoft.com/library/ms173763.aspx).
* **Instantâneo**: Especifica que os dados lidos por qualquer instrução numa transação são a versão transacionalmente consistente dos dados que existiam no início da transação.
  A transação pode reconhecer apenas as modificações de dados que foram confirmadas antes do início da transação.
  As modificações de dados feitas por outras transações após o início da transação atual não estão visíveis para declarações em execução na transação atual.
  O efeito é como se as instruções numa transação obtém um instantâneo dos dados confirmados como ele existia no início da transação.
  Os instantâneos são consistentes em coleções fiáveis.
  Para obter mais detalhes, consulte [ https://msdn.microsoft.com/library/ms173763.aspx ](https://msdn.microsoft.com/library/ms173763.aspx).

As coleções fiáveis automaticamente escolher o nível de isolamento a utilizar para uma operação de leitura determinada consoante a operação e a função da réplica no momento da criação da transação.
Segue-se a tabela que mostra as predefinições de nível de isolamento para operações de dicionário fiável e fila.

| Operação \ função | Primária | Secundária |
| --- |:--- |:--- |
| Leitura de entidade única |Leitura passível de repetição |Instantâneo |
| Enumeração, contagem |Instantâneo |Instantâneo |

> [!NOTE]
> São exemplos comuns para operações de entidade única `IReliableDictionary.TryGetValueAsync`, `IReliableQueue.TryPeekAsync`.
> 

O dicionário fiável e a fila do Reliable suportam leitura Your escreve.
Em outras palavras, qualquer escrita dentro de uma transação estarão visível para uma leitura seguinte que pertence à mesma transação.

## <a name="locks"></a>Bloqueios
Na Reliable Collections, implementar de todas as transações rigorosos dois na fase de bloqueio: uma transação não lançar os bloqueios de obteve até que a transação termina com um aborto ou consolidação.

Dicionário fiável utiliza o nível de linha de bloqueio para todas as operações de entidade única.
Fila do Reliable negocia desativar simultaneidade para strict propriedades de FIFO transacionais.
Fila do Reliable utiliza bloqueios de nível de operação que permite uma transação com `TryPeekAsync` e/ou `TryDequeueAsync` e uma transação com `EnqueueAsync` cada vez.
Observe que, para preservar FIFO, se um `TryPeekAsync` ou `TryDequeueAsync` nunca observa que a fila fiáveis está vazia, eles também irão bloquear `EnqueueAsync`.

Escreva operações sempre utilizem bloqueios exclusivos.
Para operações de leitura, o bloqueio depende de dois fatores.
Qualquer operação de leitura feita usando o isolamento de instantâneo é sem bloqueio.
Qualquer operação de leitura repetida por predefinição faz bloqueios de partilhado.
No entanto, para qualquer operação de leitura que suporta a leitura passível de repetição, o utilizador pode pedir um bloqueio de atualização em vez do bloqueio partilhado.
Um bloqueio de atualização é um bloqueio assimétrico utilizado para impedir que uma forma comum de deadlock ocorre quando várias transações bloquear recursos para atualizações de potenciais num momento posterior.

A matriz de compatibilidade de bloqueio pode ser encontrado na tabela a seguir:

| Pedir \ concedido | Nenhuma | Partilhado | Atualizar | Exclusivo |
| --- |:--- |:--- |:--- |:--- |
| Partilhado |Nenhum conflito |Nenhum conflito |Conflito |Conflito |
| Atualizar |Nenhum conflito |Nenhum conflito |Conflito |Conflito |
| Exclusivo |Nenhum conflito |Conflito |Conflito |Conflito |

O argumento de limite de tempo nas APIs de coleções fiáveis é utilizado para detecção de deadlocks.
Por exemplo, duas transações (T1 e T2) estão a tentar ler e atualizar K1.
É possível que ocorra um deadlock, uma vez que ambos acabam tendo o bloqueio partilhado.
Neste caso, um ou ambos das operações terão tempo limite.

Este cenário de deadlock é um ótimo exemplo de como um bloqueio de atualização pode evitar deadlocks.

## <a name="next-steps"></a>Passos Seguintes
* [Trabalhar com as Reliable Collections](service-fabric-work-with-reliable-collections.md)
* [Notificações do Reliable Services](service-fabric-reliable-services-notifications.md)
* [Cópia de segurança de serviços fiável e de restauro (recuperação após desastre)](service-fabric-reliable-services-backup-restore.md)
* [Configuração do Reliable State Manager](service-fabric-reliable-services-configuration.md)
* [Referência do desenvolvedor do Reliable Collections](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.data.collections.aspx)

