---
title: Monitor armazenamento de memória xtp
description: Estimar e monitorizar o uso de armazenamento de memória xTP, capacidade; resolver erro de capacidade 41823
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: juliemsft
ms.author: jrasnick
ms.reviewer: genemi
ms.date: 01/25/2019
ms.openlocfilehash: 22ff83b1ccd009624082e45073123a45006df70f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79209444"
---
# <a name="monitor-in-memory-oltp-storage"></a>Monitor armazenamento OLTP em memória

Ao utilizar [o OLTP in-memory](sql-database-in-memory.md), os dados em tabelas otimizadas de memória e variáveis de mesa residem no armazenamento OLTP in-Memory. Cada nível de serviço Premium e Business Critical tem um tamanho máximo de armazenamento OLTP em Memória. Ver [limites de recursos baseados em DTU - base](sql-database-dtu-resource-limits-single-databases.md)de dados única, [limites de recursos baseados em DTU - piscinas elásticas](sql-database-dtu-resource-limits-elastic-pools.md),[limites de recursos baseados em vCore - bases](sql-database-vcore-resource-limits-single-databases.md) de dados únicas e [limites de recursos baseados em vCore - piscinas elásticas](sql-database-vcore-resource-limits-elastic-pools.md).

Uma vez ultrapassado este limite, as operações de inserção e atualização podem começar a falhar com o erro 41823 para bases de dados individuais e erro 41840 para piscinas elásticas. Nessa altura, é necessário eliminar dados para recuperar a memória, ou atualizar o nível de serviço ou calcular o tamanho da sua base de dados.

## <a name="determine-whether-data-fits-within-the-in-memory-oltp-storage-cap"></a>Determine se os dados se enquadram na tampa de armazenamento OLTP em memória

Determine as tampas de armazenamento dos diferentes níveis de serviço. Ver [limites de recursos baseados em DTU - base](sql-database-dtu-resource-limits-single-databases.md)de dados única, [limites de recursos baseados em DTU - piscinas elásticas](sql-database-dtu-resource-limits-elastic-pools.md),[limites de recursos baseados em vCore - bases](sql-database-vcore-resource-limits-single-databases.md) de dados únicas e [limites de recursos baseados em vCore - piscinas elásticas](sql-database-vcore-resource-limits-elastic-pools.md).

Estimar os requisitos de memória para uma tabela otimizada pela memória funciona da mesma forma para o Servidor SQL como na Base de Dados Azure SQL. Dere uns minutos para rever o artigo sobre a [MSDN](https://msdn.microsoft.com/library/dn282389.aspx).

As linhas variáveis de tabela e mesa, bem como os índices, contam para o tamanho máximo dos dados dos utilizadores. Além disso, a ALTER TABLE necessita de espaço suficiente para criar uma nova versão de toda a tabela e dos seus índices.

## <a name="monitoring-and-alerting"></a>Monitorização e alertas
Pode monitorizar o armazenamento em memória em percentagem da tampa de armazenamento para o seu tamanho de computação no [portal Azure:](https://portal.azure.com/) 

1. Na lâmina base de dados, localize a caixa de utilização do Recurso e clique em Editar.
2. Selecione `In-Memory OLTP Storage percentage`a métrica .
3. Para adicionar um alerta, clique na caixa de utilização de recursos para abrir a lâmina Métrica e, em seguida, clique em Adicionar alerta.

Ou utilize a seguinte consulta para mostrar a utilização do armazenamento em memória:

```sql
    SELECT xtp_storage_percent FROM sys.dm_db_resource_stats
```

## <a name="correct-out-of-in-memory-oltp-storage-situations---errors-41823-and-41840"></a>Corrigir situações de armazenamento oLTP fora de memória - Erros 41823 e 41840

Bater na tampa de armazenamento OLTP em memória na sua base de dados resulta em operações INSERT, UPDATE, ALTER e CREATE falhando com a mensagem de erro 41823 (para bases de dados únicas) ou erro 41840 (para piscinas elásticas). Ambos os erros fazem com que a transação ativa aborte.

As mensagens de erro 41823 e 41840 indicam que as tabelas otimizadas pela memória e as variáveis de mesa na base de dados ou piscina atingiram o tamanho máximo de armazenamento OLTP em Memória.

Para resolver este erro, também:

* Eliminar dados das tabelas otimizadas pela memória, potencialmente descarregando os dados para tabelas tradicionais baseadas em discos; ou,
* Atualize o nível de serviço para um com armazenamento suficiente na memória para os dados que precisa de guardar em tabelas otimizadas pela memória.

> [!NOTE] 
> Em casos raros, os erros 41823 e 41840 podem ser transitórios, o que significa que há armazenamento oLTP suficiente disponível em Memória, e tentar a operação tem sucesso. Recomendamos, portanto, monitorizar o armazenamento geral disponível em Memória OLTP e retentar quando encontrar o erro 41823 ou 41840. Para obter mais informações sobre a lógica de retry, consulte a Lógica de [Deteção de Conflitos e Retry com OLTP in-memory](https://docs.microsoft.com/sql/relational-databases/In-memory-oltp/transactions-with-memory-optimized-tables#conflict-detection-and-retry-logic).

## <a name="next-steps"></a>Passos seguintes
Para obter orientação de monitorização, consulte [monitorizar a Base de Dados Azure SQL utilizando pontos](sql-database-monitoring-with-dmvs.md)de vista dinâmicos de gestão .
