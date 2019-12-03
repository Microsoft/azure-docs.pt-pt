---
title: O recurso de DTU limita pools elásticos
description: Esta página descreve alguns limites comuns de recursos de DTU para pools elásticos no banco de dados SQL do Azure.
services: sql-database
ms.service: sql-database
ms.subservice: elastic-pools
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: sachinpMSFT
ms.author: sachinp
ms.reviewer: carlrab
ms.date: 03/14/2019
ms.openlocfilehash: 1dd0f90a9844bb3afbd15e1f8c804d3a7c6b7fff
ms.sourcegitcommit: 48b7a50fc2d19c7382916cb2f591507b1c784ee5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/02/2019
ms.locfileid: "74687681"
---
# <a name="resources-limits-for-elastic-pools-using-the-dtu-purchasing-model"></a>Recursos limites para pools elásticos usando o modelo de compra de DTU

Este artigo fornece os limites de recursos detalhados para os pools elásticos do banco de dados SQL do Azure e bancos de dados em pool usando o modelo de compra DTU.

Para os limites de recursos do modelo de compra de DTU para bancos de dados individuais, consulte [limites de recursos de DTU-bancos de dados individuais](sql-database-vcore-resource-limits-elastic-pools.md). Para limites de recursos vCore, consulte [limites de recursos VCORE-bancos de dados individuais](sql-database-vcore-resource-limits-single-databases.md) e [limites de recursos VCORE-pools elásticos](sql-database-vcore-resource-limits-elastic-pools.md).

## <a name="elastic-pool-storage-sizes-and-compute-sizes"></a>Pool elástico: tamanhos de armazenamento e tamanhos de computação

Para pools elásticos do banco de dados SQL, as tabelas a seguir mostram os recursos disponíveis em cada camada de serviço e tamanho de computação. Você pode definir a camada de serviço, o tamanho da computação e o valor de armazenamento usando o [portal do Azure](sql-database-elastic-pool-manage.md#azure-portal-manage-elastic-pools-and-pooled-databases), o [PowerShell](sql-database-elastic-pool-manage.md#powershell-manage-elastic-pools-and-pooled-databases), o [CLI do Azure](sql-database-elastic-pool-manage.md#azure-cli-manage-elastic-pools-and-pooled-databases)ou a [API REST](sql-database-elastic-pool-manage.md#rest-api-manage-elastic-pools-and-pooled-databases).

> [!IMPORTANT]
> Para obter diretrizes e considerações sobre o dimensionamento, consulte [dimensionar um pool elástico](sql-database-elastic-pool-scale.md)
> [!NOTE]
> Os limites de recursos de bancos de dados individuais em pools elásticos geralmente são os mesmos dos bancos de dados únicos fora dos pools com base em DTUs e na camada de serviço. Por exemplo, o máximo de trabalhos simultâneos para um banco de dados S2 é de 120 trabalhadores. Assim, o máximo de trabalhos simultâneos para um banco de dados em um pool padrão também será de 120 trabalhadores se o DTU máximo por banco de dados no pool for 50 DTUs (que é equivalente a S2).

### <a name="basic-elastic-pool-limits"></a>Limites do conjunto elástico básico

| eDTUs por conjunto | **50** | **100** | **200** | **300** | **400** | **800** | **1200** | **1600** |
|:---|---:|---:|---:| ---: | ---: | ---: | ---: | ---: |
| Armazenamento incluído por pool (GB) | 5 | 10 | 20 | 29 | 39 | 78 | 117 | 156 |
| Opções de armazenamento máximo por pool (GB) | 5 | 10 | 20 | 29 | 39 | 78 | 117 | 156 |
| Armazenamento máximo OLTP na memória por pool (GB) | N/A | N/A | N/A | N/A | N/A | N/A | N/A | N/A |
| Número de DBs máximo por conjunto | 100 | 200 | 500 | 500 | 500 | 500 | 500 | 500 |
| Máximo de trabalhadores simultâneos (pedidos) por conjunto | 100 | 200 | 400 | 600 | 800 | 1600 | 2400 | 3200 |
| Máximo de sessões simultâneas por conjunto | 30000 | 30000 | 30000 | 30000 |30000 | 30000 | 30000 | 30000 |
| Mínimo de opções de eDTUs por banco de dados | 0, 5 | 0, 5 | 0, 5 | 0, 5 | 0, 5 | 0, 5 | 0, 5 | 0, 5 |
| Máximo de opções de eDTUs por banco de dados | 5 | 5 | 5 | 5 | 5 | 5 | 5 | 5 |
| Armazenamento máximo por base de dados (GB) | 2 | 2 | 2 | 2 | 2 | 2 | 2 | 2 |
||||||||

### <a name="standard-elastic-pool-limits"></a>Limites do conjunto elástico padrão

| eDTUs por conjunto | **50** | **100** | **200** | **300** | **400** | **800**|
|:---|---:|---:|---:| ---: | ---: | ---: |
| Armazenamento incluído por pool (GB) | 50 | 100 | 200 | 300 | 400 | 800 |
| Opções de armazenamento máximo por pool (GB) | 50, 250, 500 | 100, 250, 500, 750 | 200, 250, 500, 750, 1024 | 300, 500, 750, 1024, 1280 | 400, 500, 750, 1024, 1280, 1536 | 800, 1024, 1280, 1536, 1792, 2048 |
| Armazenamento máximo OLTP na memória por pool (GB) | N/A | N/A | N/A | N/A | N/A | N/A |
| Número de DBs máximo por conjunto | 100 | 200 | 500 | 500 | 500 | 500 |
| Máximo de trabalhadores simultâneos (pedidos) por conjunto | 100 | 200 | 400 | 600 | 800 | 1600 |
| Máximo de sessões simultâneas por conjunto | 30000 | 30000 | 30000 | 30000 | 30000 | 30000 |
| Mínimo de opções de eDTUs por banco de dados | 0, 10, 20, 50 | 0, 10, 20, 50, 100 | 0, 10, 20, 50, 100, 200 | 0, 10, 20, 50, 100, 200, 300 | 0, 10, 20, 50, 100, 200, 300, 400 | 0, 10, 20, 50, 100, 200, 300, 400, 800 |
| Máximo de opções de eDTUs por banco de dados | 10, 20, 50 | 10, 20, 50, 100 | 10, 20, 50, 100, 200 | 10, 20, 50, 100, 200, 300 | 10, 20, 50, 100, 200, 300, 400 | 10, 20, 50, 100, 200, 300, 400, 800 |
| Armazenamento máximo por base de dados (GB) | 500 | 750 | 1024 | 1024 | 1024 | 1024 |
||||||||

### <a name="standard-elastic-pool-limits-continued"></a>Limites do conjunto elástico standard (continuação)

| eDTUs por conjunto | **1200** | **1600** | **2000** | **2500** | **3000** |
|:---|---:|---:|---:| ---: | ---: |
| Armazenamento incluído por pool (GB) | 1200 | 1600 | 2000 | 2500 | 3000 |
| Opções de armazenamento máximo por pool (GB) | 1200, 1280, 1536, 1792, 2048, 2304, 2560 | 1600, 1792, 2048, 2304, 2560, 2816, 3072 | 2000, 2048, 2304, 2560, 2816, 3072, 3328, 3584 | 2500, 2560, 2816, 3072, 3328, 3584, 3840, 4096 | 3000, 3072, 3328, 3584, 3840, 4096 |
| Armazenamento máximo OLTP na memória por pool (GB) | N/A | N/A | N/A | N/A | N/A |
| Número de DBs máximo por conjunto | 500 | 500 | 500 | 500 | 500 |
| Máximo de trabalhadores simultâneos (pedidos) por conjunto | 2400 | 3200 | 4000 | 5000 | 6000 |
| Máximo de sessões simultâneas por conjunto | 30000 | 30000 | 30000 | 30000 | 30000 |
| Mínimo de opções de eDTUs por banco de dados | 0, 10, 20, 50, 100, 200, 300, 400, 800, 1200 | 0, 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600 | 0, 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000 | 0, 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000, 2500 | 0, 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000, 2500, 3000 |
| Máximo de opções de eDTUs por banco de dados | 10, 20, 50, 100, 200, 300, 400, 800, 1200 | 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600 | 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000 | 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000, 2500 | 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000, 2500, 3000 |
| Opções de armazenamento máximo por banco de dados (GB) | 1024 | 1024 | 1024 | 1024 | 1024 |
|||||||

### <a name="premium-elastic-pool-limits"></a>Limites do conjunto elástico premium

| eDTUs por conjunto | **125** | **250** | **500** | **1000** | **1500**|
|:---|---:|---:|---:| ---: | ---: |
| Armazenamento incluído por pool (GB) | 250 | 500 | 750 | 1024 | 1536 |
| Opções de armazenamento máximo por pool (GB) | 250, 500, 750, 1024 | 500, 750, 1024 | 750, 1024 | 1024 | 1536 |
| Armazenamento máximo OLTP na memória por pool (GB) | 1 | 2 | 4 | 10 | 12 |
| Número de DBs máximo por conjunto | 50 | 100 | 100 | 100 | 100 |
| Máximo de trabalhadores simultâneos por conjunto (pedidos) | 200 | 400 | 800 | 1600 | 2400 |
| Máximo de sessões simultâneas por conjunto | 30000 | 30000 | 30000 | 30000 | 30000 |
| Mínimo de eDTUs por base de dados | 0, 25, 50, 75, 125 | 0, 25, 50, 75, 125, 250 | 0, 25, 50, 75, 125, 250, 500 | 0, 25, 50, 75, 125, 250, 500, 1000 | 0, 25, 50, 75, 125, 250, 500, 1000, 1500 |
| Máximo de eDTUs por base de dados | 25, 50, 75, 125 | 25, 50, 75, 125, 250 | 25, 50, 75, 125, 250, 500 | 25, 50, 75, 125, 250, 500, 1000 | 25, 50, 75, 125, 250, 500, 1000|
| Armazenamento máximo por base de dados (GB) | 1024 | 1024 | 1024 | 1024 | 1024 |
|||||||

### <a name="premium-elastic-pool-limits-continued"></a>Limites do conjunto elástico premium (continuação)

| eDTUs por conjunto | **2000** | **2500** | **3000** | **3500** | **4000**|
|:---|---:|---:|---:| ---: | ---: |
| Armazenamento incluído por pool (GB) | 2048 | 2560 | 3072 | 3548 | 4096 |
| Opções de armazenamento máximo por pool (GB) | 2048 | 2560 | 3072 | 3548 | 4096|
| Armazenamento máximo OLTP na memória por pool (GB) | 16 | 20 | 24 | 28 | 32 |
| Número de DBs máximo por conjunto | 100 | 100 | 100 | 100 | 100 |
| Máximo de trabalhadores simultâneos (pedidos) por conjunto | 3200 | 4000 | 4800 | 5600 | 6400 |
| Máximo de sessões simultâneas por conjunto | 30000 | 30000 | 30000 | 30000 | 30000 |
| Mínimo de opções de eDTUs por banco de dados | 0, 25, 50, 75, 125, 250, 500, 1000, 1750 | 0, 25, 50, 75, 125, 250, 500, 1000, 1750 | 0, 25, 50, 75, 125, 250, 500, 1000, 1750 | 0, 25, 50, 75, 125, 250, 500, 1000, 1750 | 0, 25, 50, 75, 125, 250, 500, 1000, 1750, 4000 |
| Máximo de opções de eDTUs por banco de dados | 25, 50, 75, 125, 250, 500, 1000, 1750 | 25, 50, 75, 125, 250, 500, 1000, 1750 | 25, 50, 75, 125, 250, 500, 1000, 1750 | 25, 50, 75, 125, 250, 500, 1000, 1750 | 25, 50, 75, 125, 250, 500, 1000, 1750, 4000 |
| Armazenamento máximo por base de dados (GB) | 1024 | 1024 | 1024 | 1024 | 1024 |
|||||||

> [!IMPORTANT]
> Mais de 1 TB de armazenamento na camada Premium está disponível atualmente em todas as regiões, exceto: Leste da China, Norte da China, Alemanha central, Alemanha nordeste, Oeste EUA Central, regiões de US DoD e central do governo dos EUA. Noutras regiões, o armazenamento máximo no escalão Premium está limitado a 1 TB.  Para obter mais informações, consulte [limitações atuais do P11-P15](sql-database-single-database-scale.md#p11-and-p15-constraints-when-max-size-greater-than-1-tb).

Se todas as DTUs de um conjunto elástico forem utilizadas, cada base de dados no conjunto recebe uma quantidade igual de recursos para processar consultas. O serviço Base de Dados SQL fornece equidade de partilha de recursos entre bases de dados, garantindo frações iguais de tempo de computação. A equidade de partilha de recursos de um conjunto elástico é adicional a qualquer quantidade de recursos garantido de outro modo a cada base de dados quando o mínimo de DTUs por base de dados está definido como um valor diferente de zero.

> [!NOTE]
> Para limites de `tempdb`, consulte [limites de tempdb](https://docs.microsoft.com/sql/relational-databases/databases/tempdb-database?view=sql-server-2017#tempdb-database-in-sql-database).

### <a name="database-properties-for-pooled-databases"></a>Propriedades do banco de dados em pools

A tabela a seguir descreve as propriedades de bancos de dados em pool.

| Propriedade | Descrição |
|:--- |:--- |
| Máximo de eDTUs por base de dados |O número máximo de eDTUs que qualquer base de dados no conjunto pode utilizar, caso estejam disponíveis com base na utilização por outras bases de dados no conjunto. O número máximo de eDTUs por base de dados não é uma garantia de recurso para uma base de dados. Esta definição é uma definição global que se aplica a todas as bases de dados no conjunto. Defina um número máximo de eDTUs suficientemente elevado para processar picos na utilização de base de dados. É esperado algum grau de consolidação excessiva, uma vez que, geralmente, o conjunto assume padrões de utilização a frio e a quente para bases de dados em que todas as bases de dados não atingem o pico em simultâneo. Por exemplo, suponha que o pico de utilização por base de dados é 20 eDTUs e apenas 20% das 100 bases de dados no conjunto atingem o pico ao mesmo tempo. Se o número máximo de eDTUs por base de dados estiver definido como 20 eDTUs, é razoável sobreconsolidar o conjunto em 5 vezes e definir o número de eDTUs por conjunto como 400. |
| Mínimo de eDTUs por base de dados |O número mínimo de eDTUs que é garantido a qualquer base de dados no conjunto. Esta definição é uma definição global que se aplica a todas as bases de dados no conjunto. O mínimo de eDTUs por base de dados pode ser definido como 0, que é também o valor predefinido. Esta propriedade é definida como qualquer valor entre 0 e a utilização média de eDTUs por base de dados. O produto do número de bases de dados no conjunto e o número mínimo de eDTUs por base de dados não pode exceder as eDTUs por conjunto. Por exemplo, se um conjunto tiver 20 bases de dados e o número mínimo de eDTUs por base de dados definido como 10 eDTUs, as eDTUs por conjunto têm de ser, pelo menos, 200 eDTUs. |
| Armazenamento máximo por banco de dados |O tamanho máximo do banco de dados definido pelo usuário para um banco de dados em um pool. No entanto, os bancos de dados em pool compartilham o armazenamento de pool alocado. Mesmo que o total de armazenamento máximo *por banco de dados* esteja definido para ser maior que o *espaço de*armazenamento total disponível do pool, o espaço total usado por todos os bancos de dados não poderá exceder o limite de pool disponível. O tamanho máximo do banco de dados refere-se ao tamanho máximo dos arquivos e não inclui o espaço usado pelos arquivos de log. |
|||

## <a name="next-steps"></a>Passos seguintes

- Para limites de recursos vCore para um único banco de dados, confira [limites de recursos para bancos de dados individuais usando o modelo de compra vCore](sql-database-vcore-resource-limits-single-databases.md)
- Para os limites de recursos de DTU para um único banco de dados, consulte [limites de recursos para bancos de dados individuais usando o modelo de compra de DTU](sql-database-dtu-resource-limits-single-databases.md)
- Para limites de recursos vCore para pools elásticos, consulte [limites de recursos para pools elásticos usando o modelo de compra vCore](sql-database-vcore-resource-limits-elastic-pools.md)
- Para os limites de recurso para instâncias gerenciadas, consulte [limites de recursos de instância gerenciada](sql-database-managed-instance-resource-limits.md).
- Para obter informações sobre limites gerais do Azure, consulte [assinatura do Azure e limites de serviço, cotas e restrições](../azure-subscription-service-limits.md).
- Para obter informações sobre limites de recursos em um servidor de banco de dados, consulte [visão geral dos limites de recursos em um servidor de banco de dados SQL](sql-database-resource-limits-database-server.md) para obter informações sobre os limites nos níveis de servidor e assinatura.
