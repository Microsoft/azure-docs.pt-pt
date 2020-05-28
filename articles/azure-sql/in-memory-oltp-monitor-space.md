---
title: Monitor armazenamento de memória xtp
description: Estimar e monitorizar o uso de armazenamento de memória xTP, capacidade; resolver erro de capacidade 41823
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: juliemsft
ms.author: jrasnick
ms.reviewer: genemi
ms.date: 01/25/2019
ms.openlocfilehash: 6ab303c06f1ca6c7ab6a7a192532b79505676811
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "84046881"
---
# <a name="monitor-in-memory-oltp-storage-in-azure-sql-database-and-azure-sql-managed-instance"></a>Monitor armazenamento OLTP em Azure SQL Database e Azure SQL Managed Instance
[!INCLUDE[appliesto-sqldb-sqlmi](includes/appliesto-sqldb-sqlmi.md)]

Ao utilizar [o OLTP in-memory](in-memory-oltp-overview.md), os dados em tabelas otimizadas de memória e variáveis de mesa residem no armazenamento OLTP in-Memory.

## <a name="determine-whether-data-fits-within-the-in-memory-oltp-storage-cap"></a>Determine se os dados se enquadram na tampa de armazenamento OLTP em memória

Determine as tampas de armazenamento dos diferentes níveis de serviço. Cada nível de serviço Premium e Business Critical tem um tamanho máximo de armazenamento OLTP em Memória.

- [Limites de recursos baseados em DTU - base de dados única](database/resource-limits-dtu-single-databases.md)
- [Limites de recursos baseados em DTU - piscinas elásticas](database/resource-limits-dtu-elastic-pools.md)
- [limites de recursos baseados em vCore - bases de dados únicas](database/resource-limits-vcore-single-databases.md)
- [limites de recursos baseados em vCore - piscinas elásticas](database/resource-limits-vcore-elastic-pools.md)
- [limites de recursos baseados em vCore - instância gerida](managed-instance/resource-limits.md)

Estimar os requisitos de memória para uma tabela otimizada pela memória funciona da mesma forma para o Servidor SQL como no Azure SQL Database e Azure SQL Managed Instance. Derelhe alguns minutos para rever [Os requisitos](/sql/relational-databases/in-memory-oltp/estimate-memory-requirements-for-memory-optimized-tables)de memória de estimativa .

As linhas variáveis de tabela e mesa, bem como os índices, contam para o tamanho máximo dos dados dos utilizadores. Além disso, a ALTER TABLE necessita de espaço suficiente para criar uma nova versão de toda a tabela e dos seus índices.

Uma vez ultrapassado este limite, as operações de inserção e atualização podem começar a falhar com o erro 41823 para bases de dados únicas na Base de Dados Azure SQL e bases de dados em 1ª Instância Gerida Azure SQL, e erro 41840 para piscinas elásticas na Base de Dados Azure SQL. Nessa altura, é necessário eliminar dados para recuperar a memória, ou atualizar o nível de serviço ou calcular o tamanho da sua base de dados.

## <a name="monitoring-and-alerting"></a>Monitorização e alertas

Pode monitorizar o armazenamento em memória em percentagem da tampa de armazenamento para o seu tamanho de computação no [portal Azure:](https://portal.azure.com/)

1. Na lâmina base de dados, localize a caixa de utilização do Recurso e clique em Editar.
2. Selecione a métrica `In-Memory OLTP Storage percentage` .
3. Para adicionar um alerta, clique na caixa de utilização de recursos para abrir a lâmina Métrica e, em seguida, clique em Adicionar alerta.

Ou utilize a seguinte consulta para mostrar a utilização do armazenamento em memória:

```sql
    SELECT xtp_storage_percent FROM sys.dm_db_resource_stats
```

## <a name="correct-out-of-in-memory-oltp-storage-situations---errors-41823-and-41840"></a>Corrigir situações de armazenamento oLTP fora de memória - Erros 41823 e 41840

Bater na tampa de armazenamento OLTP em memória na sua base de dados resulta em operações INSERT, UPDATE, ALTER e CREATE falhando com a mensagem de erro 41823 (para bases de dados únicas) ou erro 41840 (para piscinas elásticas). Ambos os erros fazem com que a transação ativa aborte.

As mensagens de erro 41823 e 41840 indicam que as tabelas otimizadas pela memória e as variáveis de mesa na base de dados ou piscina atingiram o tamanho máximo de armazenamento OLTP em Memória.

Para resolver este erro, também:

- Eliminar dados das tabelas otimizadas pela memória, potencialmente descarregando os dados para tabelas tradicionais baseadas em discos; ou,
- Atualize o nível de serviço para um com armazenamento suficiente na memória para os dados que precisa de guardar em tabelas otimizadas pela memória.

> [!NOTE]
> Em casos raros, os erros 41823 e 41840 podem ser transitórios, o que significa que há armazenamento oLTP suficiente disponível em Memória, e tentar a operação tem sucesso. Recomendamos, portanto, monitorizar o armazenamento geral disponível em Memória OLTP e retentar quando encontrar o erro 41823 ou 41840. Para obter mais informações sobre a lógica de retry, consulte a Lógica de [Deteção de Conflitos e Retry com OLTP in-memory](https://docs.microsoft.com/sql/relational-databases/In-memory-oltp/transactions-with-memory-optimized-tables#conflict-detection-and-retry-logic).

## <a name="next-steps"></a>Próximos passos

Para obter orientação de monitorização, consulte [a monitorização utilizando pontos](database/monitoring-with-dmvs.md)de vista dinâmicos de gestão .
