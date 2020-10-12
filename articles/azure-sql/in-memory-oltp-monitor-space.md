---
title: Monitor XTP Armazenamento na memória
description: Estimar e monitorizar a utilização, capacidade de armazenamento de memória XTP; resolver erro de capacidade 41823
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: how-to
author: juliemsft
ms.author: jrasnick
ms.reviewer: genemi
ms.date: 01/25/2019
ms.openlocfilehash: 2134cf1fda5f0f1699feb46582813d198304f92e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91616385"
---
# <a name="monitor-in-memory-oltp-storage-in-azure-sql-database-and-azure-sql-managed-instance"></a>Monitor In-Memory armazenamento OLTP em Azure SQL Database e Azure SQL Managed Instance
[!INCLUDE[appliesto-sqldb-sqlmi](includes/appliesto-sqldb-sqlmi.md)]

Ao utilizar [o OLTP in-memory](in-memory-oltp-overview.md), os dados em tabelas otimizadas pela memória e variáveis de tabela residem no armazenamento In-Memory OLTP.

## <a name="determine-whether-data-fits-within-the-in-memory-oltp-storage-cap"></a>Determinar se os dados se enquadram na tampa de armazenamento In-Memory OLTP

Determine as tampas de armazenamento dos diferentes níveis de serviço. Cada nível de serviço Premium e Business Critical tem um tamanho máximo de armazenamento In-Memory OLTP.

- [Limites de recursos baseados em DTU - base de dados única](database/resource-limits-dtu-single-databases.md)
- [Limites de recursos baseados em DTU - piscinas elásticas](database/resource-limits-dtu-elastic-pools.md)
- [limites de recursos baseados em vCore - bases de dados únicas](database/resource-limits-vcore-single-databases.md)
- [limites de recursos baseados em vCore - piscinas elásticas](database/resource-limits-vcore-elastic-pools.md)
- [limites de recursos baseados em vCore - instância gerida](managed-instance/resource-limits.md)

Estimar os requisitos de memória para uma tabela otimizada pela memória funciona da mesma forma para o SQL Server do que na Base de Dados Azure SQL e na Azure SQL Managed Instance. Dedem alguns minutos a rever [os requisitos de memória de estimativa](/sql/relational-databases/in-memory-oltp/estimate-memory-requirements-for-memory-optimized-tables).

As linhas variáveis de tabela e tabela, bem como os índices, contam para o tamanho máximo dos dados do utilizador. Além disso, o ALTER TABLE necessita de espaço suficiente para criar uma nova versão de toda a tabela e seus índices.

Uma vez ultrapassado este limite, as operações de inserção e atualização podem começar a falhar com o erro 41823 para bases de dados únicas na Base de Dados Azure SQL e bases de dados em Azure SQL Managed Instance, e erro 41840 para piscinas elásticas na Base de Dados Azure SQL. Nessa altura, é necessário eliminar dados para recuperar a memória, ou atualizar o nível de serviço ou o tamanho do cálculo da sua base de dados.

## <a name="monitoring-and-alerting"></a>Monitorização e alertas

Pode monitorizar a utilização do armazenamento na memória em percentagem da tampa de armazenamento para o seu tamanho de cálculo no [portal Azure](https://portal.azure.com/):

1. Na lâmina base de dados, localize a caixa de utilização de recursos e clique em Editar.
2. Selecione a métrica `In-Memory OLTP Storage percentage` .
3. Para adicionar um alerta, clique na caixa de utilização de recursos para abrir a lâmina métrica e, em seguida, clique em Adicionar alerta.

Ou utilize a seguinte consulta para mostrar a utilização do armazenamento na memória:

```sql
    SELECT xtp_storage_percent FROM sys.dm_db_resource_stats
```

## <a name="correct-out-of-in-memory-oltp-storage-situations---errors-41823-and-41840"></a>Corrigir situações de armazenamento OLTP fora de memória - Erros 41823 e 41840

Ao acertar na tampa de armazenamento OLTP In-Memory na sua base de dados resulta em operações INSERT, UPDATE, ALTER e CREATE que falham com a mensagem de erro 41823 (para bases de dados únicas) ou erro 41840 (para piscinas elásticas). Ambos os erros fazem com que a transação ativa aborte.

As mensagens de erro 41823 e 41840 indicam que as tabelas e variáveis de tabela otimizadas pela memória na base de dados ou na piscina atingiram o tamanho máximo de armazenamento In-Memory OLTP.

Para resolver este erro, também:

- Eliminar dados das tabelas otimizadas pela memória, potencialmente descarregando os dados para tabelas tradicionais baseadas em discos; ou,
- Atualize o nível de serviço para um com armazenamento de memória suficiente para os dados que precisa de manter em tabelas otimizadas pela memória.

> [!NOTE]
> Em casos raros, os erros 41823 e 41840 podem ser transitórios, o que significa que há suficiente In-Memory armazenamento OLTP, e reorientar a operação tem sucesso. Por isso, recomendamos que monitorizem o armazenamento geral In-Memory OLTP e reagem quando encontrarmos pela primeira vez o erro 41823 ou o 41840. Para obter mais informações sobre a lógica de relemissão, consulte [a Lógica de Deteção de Conflitos e Retripsto com In-Memory OLTP](https://docs.microsoft.com/sql/relational-databases/In-memory-oltp/transactions-with-memory-optimized-tables#conflict-detection-and-retry-logic).

## <a name="next-steps"></a>Passos seguintes

Para orientar a monitorização, consulte [a monitorização utilizando pontos de vista dinâmicos de gestão](database/monitoring-with-dmvs.md).
