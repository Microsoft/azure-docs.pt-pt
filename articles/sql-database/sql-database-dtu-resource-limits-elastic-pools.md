---
title: Os limites de recursos DTU limitam as piscinas elásticas
description: Esta página descreve alguns limites comuns de recursos DTU para piscinas elásticas na Base de Dados Azure SQL.
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
ms.openlocfilehash: 76085dc29d40944cf704dbc5efc578b3314f499a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81419243"
---
# <a name="resources-limits-for-elastic-pools-using-the-dtu-purchasing-model"></a>Limites de recursos para piscinas elásticas utilizando o modelo de compra dTU

Este artigo fornece os limites de recursos detalhados para piscinas elásticas de base de dados Azure SQL e bases de dados reunidas utilizando o modelo de compra DTU.

Para obter limites de recursos de modelos de compra dTU para bases de dados únicas, consulte [os limites de recursos dTU - bases](sql-database-vcore-resource-limits-elastic-pools.md)de dados únicas . Para os limites de recursos vCore, consulte os limites de [recursos vCore - bases de dados únicas](sql-database-vcore-resource-limits-single-databases.md) e limites de [recursos vCore - piscinas elásticas](sql-database-vcore-resource-limits-elastic-pools.md).

## <a name="elastic-pool-storage-sizes-and-compute-sizes"></a>Piscina elástica: Tamanhos de armazenamento e tamanhos de computação

Para piscinas elásticas SQL Database, as tabelas seguintes mostram os recursos disponíveis em cada nível de serviço e tamanho de computação. Pode definir o nível de serviço, o tamanho da computação e o valor de armazenamento utilizando o [portal Azure,](sql-database-elastic-pool-manage.md#azure-portal-manage-elastic-pools-and-pooled-databases) [PowerShell,](sql-database-elastic-pool-manage.md#powershell-manage-elastic-pools-and-pooled-databases)o [Azure CLI](sql-database-elastic-pool-manage.md#azure-cli-manage-elastic-pools-and-pooled-databases)ou o [REST API](sql-database-elastic-pool-manage.md#rest-api-manage-elastic-pools-and-pooled-databases).

> [!IMPORTANT]
> Para orientação e considerações de escala, consulte [Escala de uma piscina elástica](sql-database-elastic-pool-scale.md)
> [!NOTE]
> Os limites de recursos das bases de dados individuais em piscinas elásticas são geralmente os mesmos que para bases de dados individuais fora de piscinas com base em DTUs e no nível de serviço. Por exemplo, os trabalhadores simultâneos máximos para uma base de dados S2 são 120 trabalhadores. Assim, os trabalhadores simultâneos máximos para uma base de dados numa piscina Standard também são 120 trabalhadores se o DTU max por base de dados na piscina for de 50 DTUs (o que equivale a S2).

> [!NOTE]
> O armazenamento por limite de recursos de piscina em cada uma das tabelas seguintes não inclui o armazenamento temporário e de registo.

### <a name="basic-elastic-pool-limits"></a>Limites do conjunto elástico básico

| eDTUs por conjunto | **50** | **100** | **200** | **300** | **400** | **800** | **1200** | **1600** |
|:---|---:|---:|---:| ---: | ---: | ---: | ---: | ---: |
| Armazenamento incluído por piscina (GB) | 5 | 10 | 20 | 29 | 39 | 78 | 117 | 156 |
| Escolhas de armazenamento máximo por piscina (GB) | 5 | 10 | 20 | 29 | 39 | 78 | 117 | 156 |
| Armazenamento OLTP Max In-Memory por piscina (GB) | N/D | N/D | N/D | N/D | N/D | N/D | N/D | N/D |
| DBs max número por piscina <sup>1</sup> | 100 | 200 | 500 | 500 | 500 | 500 | 500 | 500 |
| Trabalhadores simultâneos max (pedidos) por piscina <sup>2</sup> | 100 | 200 | 400 | 600 | 800 | 1600 | 2400 | 3200 |
| Sessões simultâneas max por piscina <sup>2</sup> | 30000 | 30000 | 30000 | 30000 |30000 | 30000 | 30000 | 30000 |
| Escolhas min eDTUs por base de dados | 0, 5 | 0, 5 | 0, 5 | 0, 5 | 0, 5 | 0, 5 | 0, 5 | 0, 5 |
| Escolhas max eDTUs por base de dados | 5 | 5 | 5 | 5 | 5 | 5 | 5 | 5 |
| Armazenamento máximo por base de dados (GB) | 2 | 2 | 2 | 2 | 2 | 2 | 2 | 2 |
||||||||

<sup>1</sup> Consulte a [gestão de recursos em piscinas elásticas densas](sql-database-elastic-pool-resource-management.md) para considerações adicionais.

<sup>2</sup> Para os trabalhadores simultâneos max (pedidos) de qualquer base de dados individual, consulte os limites de [recursos de base de dados individuais](sql-database-vcore-resource-limits-single-databases.md). Por exemplo, se o pool elástico estiver a utilizar a Gen5 e o vCore max por base de dados for fixado em 2, então o valor máximo dos trabalhadores simultâneos é de 200.  Se o vCore max por base de dados estiver definido para 0,5, então o valor máximo dos trabalhadores simultâneos é de 50, uma vez que na Gen5 existem um máximo de 100 trabalhadores simultâneos por vCore. Para outras definições vCore max por base de dados que sejam menos 1 vCore ou menos, o número de trabalhadores max simultâneos é igualmente redimensionado.

### <a name="standard-elastic-pool-limits"></a>Limites do conjunto elástico padrão

| eDTUs por conjunto | **50** | **100** | **200** | **300** | **400** | **800**|
|:---|---:|---:|---:| ---: | ---: | ---: |
| Armazenamento incluído por piscina (GB) | 50 | 100 | 200 | 300 | 400 | 800 |
| Escolhas de armazenamento máximo por piscina (GB) | 50, 250, 500 | 100, 250, 500, 750 | 200, 250, 500, 750, 1024 | 300, 500, 750, 1024, 1280 | 400, 500, 750, 1024, 1280, 1536 | 800, 1024, 1280, 1536, 1792, 2048 |
| Armazenamento OLTP Max In-Memory por piscina (GB) | N/D | N/D | N/D | N/D | N/D | N/D |
| DBs max número por piscina <sup>1</sup> | 100 | 200 | 500 | 500 | 500 | 500 |
| Trabalhadores simultâneos max (pedidos) por piscina <sup>2</sup> | 100 | 200 | 400 | 600 | 800 | 1600 |
| Sessões simultâneas max por piscina <sup>2</sup> | 30000 | 30000 | 30000 | 30000 | 30000 | 30000 |
| Escolhas min eDTUs por base de dados | 0, 10, 20, 50 | 0, 10, 20, 50, 100 | 0, 10, 20, 50, 100, 200 | 0, 10, 20, 50, 100, 200, 300 | 0, 10, 20, 50, 100, 200, 300, 400 | 0, 10, 20, 50, 100, 200, 300, 400, 800 |
| Escolhas max eDTUs por base de dados | 10, 20, 50 | 10, 20, 50, 100 | 10, 20, 50, 100, 200 | 10, 20, 50, 100, 200, 300 | 10, 20, 50, 100, 200, 300, 400 | 10, 20, 50, 100, 200, 300, 400, 800 |
| Armazenamento máximo por base de dados (GB) | 500 | 750 | 1024 | 1024 | 1024 | 1024 |
||||||||

<sup>1</sup> Consulte a [gestão de recursos em piscinas elásticas densas](sql-database-elastic-pool-resource-management.md) para considerações adicionais.

<sup>2</sup> Para os trabalhadores simultâneos max (pedidos) de qualquer base de dados individual, consulte os limites de [recursos de base de dados individuais](sql-database-vcore-resource-limits-single-databases.md). Por exemplo, se o pool elástico estiver a utilizar a Gen5 e o vCore max por base de dados for fixado em 2, então o valor máximo dos trabalhadores simultâneos é de 200.  Se o vCore max por base de dados estiver definido para 0,5, então o valor máximo dos trabalhadores simultâneos é de 50, uma vez que na Gen5 existem um máximo de 100 trabalhadores simultâneos por vCore. Para outras definições vCore max por base de dados que sejam menos 1 vCore ou menos, o número de trabalhadores max simultâneos é igualmente redimensionado.

### <a name="standard-elastic-pool-limits-continued"></a>Limites do conjunto elástico standard (continuação)

| eDTUs por conjunto | **1200** | **1600** | **2000** | **2500** | **3000** |
|:---|---:|---:|---:| ---: | ---: |
| Armazenamento incluído por piscina (GB) | 1200 | 1600 | 2000 | 2500 | 3.000 |
| Escolhas de armazenamento máximo por piscina (GB) | 1200, 1280, 1536, 1792, 2048, 2304, 2560 | 1600, 1792, 2048, 2304, 2560, 2816, 3072 | 2000, 2048, 2304, 2560, 2816, 3072, 3328, 3584 | 2500, 2560, 2816, 3072, 3328, 3584, 3840, 4096 | 3000, 3072, 3328, 3584, 3840, 4096 |
| Armazenamento OLTP Max In-Memory por piscina (GB) | N/D | N/D | N/D | N/D | N/D |
| DBs max número por piscina <sup>1</sup> | 500 | 500 | 500 | 500 | 500 |
| Trabalhadores simultâneos max (pedidos) por piscina <sup>2</sup> | 2400 | 3200 | 4000 | 5000 | 6000 |
| Sessões simultâneas max por piscina <sup>2</sup> | 30000 | 30000 | 30000 | 30000 | 30000 |
| Escolhas min eDTUs por base de dados | 0, 10, 20, 50, 100, 200, 300, 400, 800, 1200 | 0, 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600 | 0, 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000 | 0, 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000, 2500 | 0, 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000, 2500, 3000 |
| Escolhas max eDTUs por base de dados | 10, 20, 50, 100, 200, 300, 400, 800, 1200 | 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600 | 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000 | 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000, 2500 | 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000, 2500, 3000 |
| Escolhas de armazenamento máximo por base de dados (GB) | 1024 | 1024 | 1024 | 1024 | 1024 |
|||||||

<sup>1</sup> Consulte a [gestão de recursos em piscinas elásticas densas](sql-database-elastic-pool-resource-management.md) para considerações adicionais.

<sup>2</sup> Para os trabalhadores simultâneos max (pedidos) de qualquer base de dados individual, consulte os limites de [recursos de base de dados individuais](sql-database-vcore-resource-limits-single-databases.md). Por exemplo, se o pool elástico estiver a utilizar a Gen5 e o vCore max por base de dados for fixado em 2, então o valor máximo dos trabalhadores simultâneos é de 200.  Se o vCore max por base de dados estiver definido para 0,5, então o valor máximo dos trabalhadores simultâneos é de 50, uma vez que na Gen5 existem um máximo de 100 trabalhadores simultâneos por vCore. Para outras definições vCore max por base de dados que sejam menos 1 vCore ou menos, o número de trabalhadores max simultâneos é igualmente redimensionado.

### <a name="premium-elastic-pool-limits"></a>Limites do conjunto elástico premium

| eDTUs por conjunto | **125** | **250** | **500** | **1000** | **1500**|
|:---|---:|---:|---:| ---: | ---: |
| Armazenamento incluído por piscina (GB) | 250 | 500 | 750 | 1024 | 1536 |
| Escolhas de armazenamento máximo por piscina (GB) | 250, 500, 750, 1024 | 500, 750, 1024 | 750, 1024 | 1024 | 1536 |
| Armazenamento OLTP Max In-Memory por piscina (GB) | 1 | 2 | 4 | 10 | 12 |
| DBs max número por piscina <sup>1</sup> | 50 | 100 | 100 | 100 | 100 |
| Trabalhadores simultâneos max por piscina (pedidos) <sup>2</sup> | 200 | 400 | 800 | 1600 | 2400 |
| Sessões simultâneas max por piscina <sup>2</sup> | 30000 | 30000 | 30000 | 30000 | 30000 |
| Mínimo de eDTUs por base de dados | 0, 25, 50, 75, 125 | 0, 25, 50, 75, 125, 250 | 0, 25, 50, 75, 125, 250, 500 | 0, 25, 50, 75, 125, 250, 500, 1000 | 0, 25, 50, 75, 125, 250, 500, 1000|
| Máximo de eDTUs por base de dados | 25, 50, 75, 125 | 25, 50, 75, 125, 250 | 25, 50, 75, 125, 250, 500 | 25, 50, 75, 125, 250, 500, 1000 | 25, 50, 75, 125, 250, 500, 1000|
| Armazenamento máximo por base de dados (GB) | 1024 | 1024 | 1024 | 1024 | 1024 |
|||||||

<sup>1</sup> Consulte a [gestão de recursos em piscinas elásticas densas](sql-database-elastic-pool-resource-management.md) para considerações adicionais.

<sup>2</sup> Para os trabalhadores simultâneos max (pedidos) de qualquer base de dados individual, consulte os limites de [recursos de base de dados individuais](sql-database-vcore-resource-limits-single-databases.md). Por exemplo, se o pool elástico estiver a utilizar a Gen5 e o vCore max por base de dados for fixado em 2, então o valor máximo dos trabalhadores simultâneos é de 200.  Se o vCore max por base de dados estiver definido para 0,5, então o valor máximo dos trabalhadores simultâneos é de 50, uma vez que na Gen5 existem um máximo de 100 trabalhadores simultâneos por vCore. Para outras definições vCore max por base de dados que sejam menos 1 vCore ou menos, o número de trabalhadores max simultâneos é igualmente redimensionado.

### <a name="premium-elastic-pool-limits-continued"></a>Limites do conjunto elástico premium (continuação)

| eDTUs por conjunto | **2000** | **2500** | **3000** | **3500** | **4000**|
|:---|---:|---:|---:| ---: | ---: |
| Armazenamento incluído por piscina (GB) | 2048 | 2560 | 3072 | 3548 | 4096 |
| Escolhas de armazenamento máximo por piscina (GB) | 2048 | 2560 | 3072 | 3548 | 4096|
| Armazenamento OLTP Max In-Memory por piscina (GB) | 16 | 20 | 24 | 28 | 32 |
| DBs max número por piscina <sup>1</sup> | 100 | 100 | 100 | 100 | 100 |
| Trabalhadores simultâneos max (pedidos) por piscina <sup>2</sup> | 3200 | 4000 | 4800 | 5600 | 6400 |
| Sessões simultâneas max por piscina <sup>2</sup> | 30000 | 30000 | 30000 | 30000 | 30000 |
| Escolhas min eDTUs por base de dados | 0, 25, 50, 75, 125, 250, 500, 1000, 1750 | 0, 25, 50, 75, 125, 250, 500, 1000, 1750 | 0, 25, 50, 75, 125, 250, 500, 1000, 1750 | 0, 25, 50, 75, 125, 250, 500, 1000, 1750 | 0, 25, 50, 75, 125, 250, 500, 1000, 1750, 4000 |
| Escolhas max eDTUs por base de dados | 25, 50, 75, 125, 250, 500, 1000, 1750 | 25, 50, 75, 125, 250, 500, 1000, 1750 | 25, 50, 75, 125, 250, 500, 1000, 1750 | 25, 50, 75, 125, 250, 500, 1000, 1750 | 25, 50, 75, 125, 250, 500, 1000, 1750, 4000 |
| Armazenamento máximo por base de dados (GB) | 1024 | 1024 | 1024 | 1024 | 1024 |
|||||||

<sup>1</sup> Consulte a [gestão de recursos em piscinas elásticas densas](sql-database-elastic-pool-resource-management.md) para considerações adicionais.

<sup>2</sup> Para os trabalhadores simultâneos max (pedidos) de qualquer base de dados individual, consulte os limites de [recursos de base de dados individuais](sql-database-vcore-resource-limits-single-databases.md). Por exemplo, se o pool elástico estiver a utilizar a Gen5 e o vCore max por base de dados for fixado em 2, então o valor máximo dos trabalhadores simultâneos é de 200.  Se o vCore max por base de dados estiver definido para 0,5, então o valor máximo dos trabalhadores simultâneos é de 50, uma vez que na Gen5 existem um máximo de 100 trabalhadores simultâneos por vCore. Para outras definições vCore max por base de dados que sejam menos 1 vCore ou menos, o número de trabalhadores max simultâneos é igualmente redimensionado.

> [!IMPORTANT]
> Mais de 1 TB de armazenamento no nível Premium está atualmente disponível em todas as regiões, exceto: China Leste, China North, Alemanha Central, Alemanha Nordeste, Oeste Dos EUA, regiões do DoD dos EUA e Central do Governo dos EUA. Nestas regiões, o máximo de armazenamento no nível Premium está limitado a 1 TB.  Para mais informações, consulte as [limitações atuais p11-P15](sql-database-single-database-scale.md#p11-and-p15-constraints-when-max-size-greater-than-1-tb).

Se todas as DTUs de um conjunto elástico forem utilizadas, cada base de dados no conjunto recebe uma quantidade igual de recursos para processar consultas. O serviço Base de Dados SQL fornece equidade de partilha de recursos entre bases de dados, garantindo frações iguais de tempo de computação. A equidade de partilha de recursos de um conjunto elástico é adicional a qualquer quantidade de recursos garantido de outro modo a cada base de dados quando o mínimo de DTUs por base de dados está definido como um valor diferente de zero.

> [!NOTE]
> Para `tempdb` limites, consulte [os limites temporários](https://docs.microsoft.com/sql/relational-databases/databases/tempdb-database?view=sql-server-2017#tempdb-database-in-sql-database).

### <a name="database-properties-for-pooled-databases"></a>Propriedades de base de dados para bases de dados reunidas

A tabela seguinte descreve as propriedades para bases de dados reunidas.

| Propriedade | Descrição |
|:--- |:--- |
| Máximo de eDTUs por base de dados |O número máximo de eDTUs que qualquer base de dados no conjunto pode utilizar, caso estejam disponíveis com base na utilização por outras bases de dados no conjunto. O número máximo de eDTUs por base de dados não é uma garantia de recurso para uma base de dados. Esta definição é uma definição global que se aplica a todas as bases de dados no conjunto. Defina um número máximo de eDTUs suficientemente elevado para processar picos na utilização de base de dados. É esperado algum grau de consolidação excessiva, uma vez que, geralmente, o conjunto assume padrões de utilização a frio e a quente para bases de dados em que todas as bases de dados não atingem o pico em simultâneo. Por exemplo, suponha que o pico de utilização por base de dados é 20 eDTUs e apenas 20% das 100 bases de dados no conjunto atingem o pico ao mesmo tempo. Se o número máximo de eDTUs por base de dados estiver definido como 20 eDTUs, é razoável sobreconsolidar o conjunto em 5 vezes e definir o número de eDTUs por conjunto como 400. |
| Mínimo de eDTUs por base de dados |O número mínimo de eDTUs que é garantido a qualquer base de dados no conjunto. Esta definição é uma definição global que se aplica a todas as bases de dados no conjunto. O mínimo de eDTUs por base de dados pode ser definido como 0, que é também o valor predefinido. Esta propriedade é definida como qualquer valor entre 0 e a utilização média de eDTUs por base de dados. O produto do número de bases de dados no conjunto e o número mínimo de eDTUs por base de dados não pode exceder as eDTUs por conjunto. Por exemplo, se um conjunto tiver 20 bases de dados e o número mínimo de eDTUs por base de dados definido como 10 eDTUs, as eDTUs por conjunto têm de ser, pelo menos, 200 eDTUs. |
| Armazenamento máximo por base de dados |O tamanho máximo da base de dados definido pelo utilizador para uma base de dados numa piscina. No entanto, as bases de dados reunidas partilham o armazenamento de piscinas. Mesmo que o armazenamento total máximo *por base de dados* seja superior ao espaço total de armazenamento disponível da *piscina,* o espaço total realmente utilizado por todas as bases de dados não poderá exceder o limite de piscina disponível. O tamanho da base de dados max refere-se ao tamanho máximo dos ficheiros de dados e não inclui o espaço utilizado pelos ficheiros de registo. |
|||

## <a name="next-steps"></a>Passos seguintes

- Para os limites de recursos vCore para uma única base de dados, consulte [os limites de recursos para bases de dados únicas utilizando o modelo](sql-database-vcore-resource-limits-single-databases.md) de compra vCore
- Para os limites de recursos dTU para uma única base de dados, consulte [os limites de recursos para bases de dados únicas utilizando o modelo](sql-database-dtu-resource-limits-single-databases.md) de compra dTU
- Para os limites de recursos vCore para piscinas elásticas, consulte [os limites de recursos para piscinas elásticas utilizando o modelo](sql-database-vcore-resource-limits-elastic-pools.md) de compra vCore
- Para os limites de recursos para casos geridos, consulte [os limites de recursos geridos](sql-database-managed-instance-resource-limits.md)por exemplo .
- Para obter informações sobre os limites gerais do Azure, consulte [os limites de subscrição e serviço do Azure, quotas e restrições.](../azure-resource-manager/management/azure-subscription-service-limits.md)
- Para obter informações sobre os limites de recursos num servidor de base de dados, consulte a [visão geral dos limites de recursos num servidor de base de dados SQL](sql-database-resource-limits-database-server.md) para obter informações sobre limites nos níveis de servidor e subscrição.
