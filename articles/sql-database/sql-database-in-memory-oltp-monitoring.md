---
title: Monitorar o armazenamento na memória XTP | Microsoft Docs
description: Estimar e monitorar o uso de armazenamento na memória de XTP, capacidade; resolver o erro de capacidade 41823
services: sql-database
ms.service: sql-database
ms.subservice: monitor
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: juliemsft
ms.author: jrasnick
ms.reviewer: genemi
ms.date: 01/25/2019
ms.openlocfilehash: 1c5a57f634c01cc42934a98decd8f392334dede6
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68567971"
---
# <a name="monitor-in-memory-oltp-storage"></a>Monitorar o armazenamento OLTP na memória

Ao usar o [OLTP na memória](sql-database-in-memory.md), os dados em tabelas com otimização de memória e variáveis de tabela residem no armazenamento OLTP na memória. Cada camada de serviço Premium e Comercialmente Crítico tem um tamanho máximo de armazenamento OLTP na memória. Confira [limites de recursos baseados em DTU-banco de dados individual](sql-database-dtu-resource-limits-single-databases.md), [limites de recursos baseados em DTU-pools elásticos](sql-database-dtu-resource-limits-elastic-pools.md),[limites de recursos baseados em VCORE-bancos de dados individuais](sql-database-vcore-resource-limits-single-databases.md) e limites de [recursos baseados em VCORE-pools elásticos](sql-database-vcore-resource-limits-elastic-pools.md).

Quando esse limite for excedido, as operações de inserção e atualização poderão começar a falhar com o erro 41823 para bancos de dados individuais e o erro 41840 para pools elásticos. Nesse ponto, você precisa excluir dados para recuperar memória ou atualizar a camada de serviço ou o tamanho de computação do seu banco de dados.

## <a name="determine-whether-data-fits-within-the-in-memory-oltp-storage-cap"></a>Determinar se os dados se ajustam no limite de armazenamento OLTP na memória

Determine os limites de armazenamento das diferentes camadas de serviço. Confira [limites de recursos baseados em DTU-banco de dados individual](sql-database-dtu-resource-limits-single-databases.md), [limites de recursos baseados em DTU-pools elásticos](sql-database-dtu-resource-limits-elastic-pools.md),[limites de recursos baseados em VCORE-bancos de dados individuais](sql-database-vcore-resource-limits-single-databases.md) e limites de [recursos baseados em VCORE-pools elásticos](sql-database-vcore-resource-limits-elastic-pools.md).

Estimar os requisitos de memória para uma tabela com otimização de memória funciona da mesma maneira para SQL Server como faz no banco de dados SQL do Azure. Reserve alguns minutos para examinar esse artigo no [msdn](https://msdn.microsoft.com/library/dn282389.aspx).

Linhas variáveis de tabela e tabela, bem como índices, contam para o tamanho máximo dos dados do usuário. Além disso, ALTER TABLE precisa de espaço suficiente para criar uma nova versão da tabela inteira e seus índices.

## <a name="monitoring-and-alerting"></a>Monitorização e alertas
Você pode monitorar o uso de armazenamento na memória como uma porcentagem do limite de armazenamento para o tamanho de computação no [portal do Azure](https://portal.azure.com/): 

1. Na folha banco de dados, localize a caixa utilização de recursos e clique em Editar.
2. Selecione a métrica `In-Memory OLTP Storage percentage`.
3. Para adicionar um alerta, clique na caixa utilização de recursos para abrir a folha métrica e clique em adicionar alerta.

Ou use a consulta a seguir para mostrar a utilização de armazenamento na memória:

```sql
    SELECT xtp_storage_percent FROM sys.dm_db_resource_stats
```

## <a name="correct-out-of-in-memory-oltp-storage-situations---errors-41823-and-41840"></a>Corrigir situações de armazenamento OLTP fora de memória-erros 41823 e 41840

Atingir o limite de armazenamento OLTP na memória em seu banco de dados resulta em falha nas operações INSERT, UPDATE, ALTER e CREATE com a mensagem de erro 41823 (para bancos de dados individuais) ou o erro 41840 (para pools elásticos). Os dois erros fazem com que a transação ativa seja anulada.

As mensagens de erro 41823 e 41840 indicam que as tabelas com otimização de memória e as variáveis de tabela no banco de dados ou pool atingiram o tamanho máximo de armazenamento OLTP na memória.

Para resolver esse erro, seja:

* Excluir dados das tabelas com otimização de memória, o que pode descarregar os dados em tabelas tradicionais baseadas em disco; or
* Atualize a camada de serviço para uma com armazenamento de memória suficiente para os dados que você precisa manter em tabelas com otimização de memória.

> [!NOTE] 
> Em casos raros, os erros 41823 e 41840 podem ser transitórios, ou seja, o armazenamento OLTP disponível na memória é suficiente e repetir a operação é realizada com sucesso. Portanto, recomendamos monitorar o armazenamento OLTP em memória geral disponível e tentar novamente quando encontrar o erro 41823 ou 41840 pela primeira vez. Para obter mais informações sobre a lógica de repetição, consulte [detecção de conflitos e lógica de repetição com OLTP na memória](https://docs.microsoft.com/sql/relational-databases/In-memory-oltp/transactions-with-memory-optimized-tables#conflict-detection-and-retry-logic).

## <a name="next-steps"></a>Passos Seguintes
Para obter diretrizes de monitoramento, consulte Monitorando o [banco de dados SQL do Azure usando exibições de gerenciamento dinâmico](sql-database-monitoring-with-dmvs.md).
