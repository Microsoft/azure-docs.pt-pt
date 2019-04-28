---
title: Monitorizar o armazenamento de dentro da memória do XTP | Documentos da Microsoft
description: Estimativa e o monitor de armazenamento de dentro da memória do XTP utilizam, capacidade. Resolva o erro de capacidade 41823
services: sql-database
ms.service: sql-database
ms.subservice: monitor
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: juliemsft
ms.author: jrasnick
ms.reviewer: genemi
manager: craigg
ms.date: 01/25/2019
ms.openlocfilehash: 7542e9fa04eb838baca37dbe13f7cdacdfaf041b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61035751"
---
# <a name="monitor-in-memory-oltp-storage"></a>Armazenamento OLTP dentro da memória de monitor

Ao usar [OLTP dentro da memória](sql-database-in-memory.md), dados em tabelas com otimização de memória e as variáveis de tabela residem no armazenamento OLTP dentro da memória. Cada escalão de serviço Premium e crítico para a empresa tem um tamanho máximo de armazenamento OLTP dentro da memória. Ver [limites de recursos baseados em DTU - base de dados individual](sql-database-dtu-resource-limits-single-databases.md), [limites de recursos baseados em DTU - conjuntos elásticos](sql-database-dtu-resource-limits-elastic-pools.md),[limites de recursos baseados em vCore - bases de dados individuais](sql-database-vcore-resource-limits-single-databases.md) e [limites de recursos baseados em vCore - conjuntos elásticos](sql-database-vcore-resource-limits-elastic-pools.md).

Assim que este limite for excedido, inserir e atualizar operações poderão começam a falhar com o erro 41823 para bases de dados individuais e o erro 41840 para conjuntos elásticos. Nesse ponto, precisa optar por eliminar os dados para recuperar memória, ou atualizar o escalão de serviço ou o tamanho da base de dados de computação.

## <a name="determine-whether-data-fits-within-the-in-memory-oltp-storage-cap"></a>Determinam se dados enquadram-se o limite de armazenamento OLTP dentro da memória

Determine os limites de armazenamento de diferentes escalões de serviço. Ver [limites de recursos baseados em DTU - base de dados individual](sql-database-dtu-resource-limits-single-databases.md), [limites de recursos baseados em DTU - conjuntos elásticos](sql-database-dtu-resource-limits-elastic-pools.md),[limites de recursos baseados em vCore - bases de dados individuais](sql-database-vcore-resource-limits-single-databases.md) e [limites de recursos baseados em vCore - conjuntos elásticos](sql-database-vcore-resource-limits-elastic-pools.md).

Estimar requisitos de memória para um funciona de tabela com otimização de memória da mesma forma para o SQL Server como ele faz na base de dados do Azure SQL. Demorar alguns minutos para uma revisão do artigo no [MSDN](https://msdn.microsoft.com/library/dn282389.aspx).

Tabela e linhas de variável de tabela, bem como os índices, contam para o tamanho de dados de utilizador máximo. Além disso, ALTER TABLE tem espaço suficiente para criar uma nova versão de toda a tabela e os seus índices.

## <a name="monitoring-and-alerting"></a>Monitorização e alertas
Pode monitorizar o uso de armazenamento na memória como uma percentagem da extremidade do armazenamento para o seu tamanho de computação no [portal do Azure](https://portal.azure.com/): 

1. No painel da base de dados, localize a caixa de utilização de recursos e clique em Editar.
2. Selecione a métrica `In-Memory OLTP Storage percentage`.
3. Para adicionar um alerta, clique na caixa de utilização de recursos para abrir o painel de métrico, em seguida, clique em Adicionar alerta.

Em alternativa, utilize a seguinte consulta para mostrar a utilização de armazenamento dentro da memória:

```sql
    SELECT xtp_storage_percent FROM sys.dm_db_resource_stats
```

## <a name="correct-out-of-in-memory-oltp-storage-situations---errors-41823-and-41840"></a>Corrigir situações de armazenamento OLTP de-de-em-memória esgotada - erros 41823 e 41840

Atingir o limite de armazenamento OLTP dentro da memória nos seus resultados de base de dados na inserção, ATUALIZAR, alterar e operações a falhar com mensagem de erro 41823 (para bases de dados individuais) ou erro 41840 (para conjuntos elásticos) de criação. Ambos os erros com que a transação do Active Directory para abortar.

Mensagens de erro 41823 e 41840 indicam que as tabelas com otimização de memória e as variáveis de tabela na base de dados ou conjunto atingiu o tamanho máximo de armazenamento OLTP dentro da memória.

Para resolver este erro, optar por:

* Eliminar dados de tabelas com otimização de memória, potencialmente descarregar dados para tabelas tradicionais, com base em disco; ou,
* Atualize o escalão de serviço para um com armazenamento suficiente na memória para os dados que necessários para manter em tabelas com otimização de memória.

> [!NOTE] 
> Em casos raros, os erros 41823 e 41840 podem ser transitórios, o que significa que existe armazenamento OLTP dentro da memória suficiente disponível e repetir a operação for concluída com êxito. Como tal, recomendamos a ambos os monitorizar o armazenamento OLTP dentro da memória disponível em geral e para tentar novamente quando encontrar primeiro erro 41823 ou 41840. Para obter mais informações sobre a lógica de repetição, consulte [deteção de conflitos e lógica de repetição de mensagens em fila com o OLTP dentro da memória](https://docs.microsoft.com/sql/relational-databases/In-memory-oltp/transactions-with-memory-optimized-tables#conflict-detection-and-retry-logic).

## <a name="next-steps"></a>Passos Seguintes
Para monitorizar orientações, veja [Monitoring SQL Database do Azure através de vistas de gestão dinâmica](sql-database-monitoring-with-dmvs.md).
