---
title: DTU limita piscinas elásticas
description: Esta página descreve alguns limites de recursos DTU comuns para piscinas elásticas na Base de Dados Azure SQL.
services: sql-database
ms.service: sql-database
ms.subservice: elastic-pools
ms.custom: seo-lt-2019 sqldbrb=1 references_regions
ms.devlang: ''
ms.topic: reference
author: sachinpMSFT
ms.author: sachinp
ms.reviewer: sstein
ms.date: 07/28/2020
ms.openlocfilehash: d87c5d162b96209c0ce3d3276dc518f42373590f
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/28/2020
ms.locfileid: "92780817"
---
# <a name="resources-limits-for-elastic-pools-using-the-dtu-purchasing-model"></a>Limites de recursos para piscinas elásticas utilizando o modelo de compra DTU
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Este artigo fornece os limites de recursos detalhados para bases de dados na Base de Dados Azure SQL que estão dentro de uma piscina elástica usando o modelo de compra DTU.

* Para os limites de recursos do modelo de compra da DTU para a Base de Dados Azure SQL, consulte [os limites de recursos da DTU - Base de Dados Azure SQL](resource-limits-dtu-single-databases.md).
* Para limites de recursos vCore, consulte [os limites de recursos vCore - Base de Dados Azure SQL](resource-limits-vcore-single-databases.md) e [limites de recursos vCore - piscinas elásticas](resource-limits-vcore-elastic-pools.md).

## <a name="elastic-pool-storage-sizes-and-compute-sizes"></a>Piscina elástica: Tamanhos de armazenamento e tamanhos de cálculo

Para piscinas elásticas Azure SQL Database, as tabelas a seguir mostram os recursos disponíveis em cada nível de serviço e tamanho de cálculo. Pode definir o nível de serviço, o tamanho do cálculo e a quantidade de armazenamento utilizando:

* [Portal do Azure](elastic-pool-manage.md#azure-portal)
* [PowerShell](elastic-pool-manage.md#powershell)
* [CLI do Azure](elastic-pool-manage.md#azure-cli)
* [API REST](elastic-pool-manage.md#rest-api).

> [!IMPORTANT]
> Para obter orientação e considerações de escalonamento, consulte [Escala uma piscina elástica](elastic-pool-scale.md)

Os limites de recursos de bases de dados individuais em piscinas elásticas são geralmente os mesmos que para bases de dados individuais fora de piscinas baseadas em DTUs e no nível de serviço. Por exemplo, os trabalhadores máximos simultâneos para uma base de dados S2 são 120 trabalhadores. Assim, os trabalhadores máximos simultâneos para uma base de dados num pool Standard são também 120 trabalhadores se o DTU máximo por base de dados na piscina for de 50 DTUs (o que equivale a S2).
 
Para o mesmo número de DTUs, os recursos fornecidos a uma piscina elástica podem exceder os recursos fornecidos a uma única base de dados fora de uma piscina elástica. Isto significa que é possível que a utilização eDTU de uma piscina elástica seja inferior à soma da utilização do DTU através de bases de dados dentro da piscina, dependendo dos padrões de carga de trabalho. Por exemplo, num caso extremo com apenas uma base de dados numa piscina elástica onde a utilização do DTU na base de dados é de 100%, é possível que a utilização do pool eDTU seja de 50% para determinados padrões de carga de trabalho. Isto pode acontecer mesmo que o DTU máximo por base de dados permaneça no valor máximo suportado para o tamanho da piscina dada.

> [!NOTE]
> O limite de armazenamento por conjunto de recursos em cada uma das seguintes tabelas não inclui o armazenamento temporário e o armazenamento de registos.

### <a name="basic-elastic-pool-limits"></a>Limites do conjunto elástico básico

| eDTUs por conjunto | **50** | **100** | **200** | **300** | **400** | **800** | **1200** | **1600** |
|:---|---:|---:|---:| ---: | ---: | ---: | ---: | ---: |
| Armazenamento incluído por piscina (GB) | 5 | 10 | 20 | 29 | 39 | 78 | 117 | 156 |
| Armazenamento máximo por conjunto (GB) | 5 | 10 | 20 | 29 | 39 | 78 | 117 | 156 |
| Max In-Memory armazenamento OLTP por piscina (GB) | N/D | N/D | N/D | N/D | N/D | N/D | N/D | N/D |
| DBs max por piscina <sup>1</sup> | 100 | 200 | 500 | 500 | 500 | 500 | 500 | 500 |
| Trabalhadores máximos simultâneos (pedidos) por piscina <sup>2</sup> | 100 | 200 | 400 | 600 | 800 | 1600 | 2400 | 3200 |
| Sessões concorrentes max por piscina <sup>2</sup> | 30000 | 30000 | 30000 | 30000 |30000 | 30000 | 30000 | 30000 |
| Min DTU por opções de base de dados | 0, 5 | 0, 5 | 0, 5 | 0, 5 | 0, 5 | 0, 5 | 0, 5 | 0, 5 |
| Max DTU por opções de base de dados | 5 | 5 | 5 | 5 | 5 | 5 | 5 | 5 |
| Armazenamento máximo por base de dados (GB) | 2 | 2 | 2 | 2 | 2 | 2 | 2 | 2 |
||||||||

<sup>1</sup> Consulte [a gestão de recursos em piscinas elásticas densas](elastic-pool-resource-management.md) para obter considerações adicionais.

<sup>2</sup> Para os trabalhadores máximos simultâneos (pedidos) para qualquer base de dados individual, consulte [os limites de recursos de base de dados individuais](resource-limits-vcore-single-databases.md). Por exemplo, se a piscina elástica estiver a utilizar a Gen5 e o max vCore por base de dados for definido em 2, então o valor máximo simultâneo dos trabalhadores é de 200.  Se o máximo vCore por base de dados for definido para 0,5, então o valor máximo simultâneo dos trabalhadores é de 50, uma vez que na Gen5 existe um máximo de 100 trabalhadores simultâneos por vCore. Para outras definições max vCore por base de dados que sejam inferiores a 1 vCore ou menos, o número de trabalhadores máximos simultâneos é igualmente redimensionado.

### <a name="standard-elastic-pool-limits"></a>Limites do conjunto elástico padrão

| eDTUs por conjunto | **50** | **100** | **200** | **300** | **400** | **800**|
|:---|---:|---:|---:| ---: | ---: | ---: |
| Armazenamento incluído por piscina (GB) <sup>1</sup> | 50 | 100 | 200 | 300 | 400 | 800 |
| Armazenamento máximo por conjunto (GB) | 500 | 750 | 1024 | 1280 | 1536 | 2048 |
| Max In-Memory armazenamento OLTP por piscina (GB) | N/D | N/D | N/D | N/D | N/D | N/D |
| DBs max por piscina <sup>2</sup> | 100 | 200 | 500 | 500 | 500 | 500 |
| Trabalhadores máximos simultâneos (pedidos) por piscina <sup>3</sup> | 100 | 200 | 400 | 600 | 800 | 1600 |
| Sessões concorrentes max por piscina <sup>3</sup> | 30000 | 30000 | 30000 | 30000 | 30000 | 30000 |
| Min DTU por opções de base de dados | 0, 10, 20, 50 | 0, 10, 20, 50, 100 | 0, 10, 20, 50, 100, 200 | 0, 10, 20, 50, 100, 200, 300 | 0, 10, 20, 50, 100, 200, 300, 400 | 0, 10, 20, 50, 100, 200, 300, 400, 800 |
| Max DTU por opções de base de dados | 10, 20, 50 | 10, 20, 50, 100 | 10, 20, 50, 100, 200 | 10, 20, 50, 100, 200, 300 | 10, 20, 50, 100, 200, 300, 400 | 10, 20, 50, 100, 200, 300, 400, 800 |
| Armazenamento máximo por base de dados (GB) | 500 | 750 | 1024 | 1024 | 1024 | 1024 |
||||||||

<sup>1</sup> Consulte [as opções de preços da Base de Dados SQL](https://azure.microsoft.com/pricing/details/sql-database/elastic/) para obter detalhes sobre o custo adicional incorrido devido a qualquer armazenamento extra a provisionado.

<sup>2</sup> Consulte [a gestão de recursos em piscinas elásticas densas](elastic-pool-resource-management.md) para obter considerações adicionais.

<sup>3</sup> Para os trabalhadores máximos simultâneos (pedidos) para qualquer base de dados individual, consulte [os limites de recursos de base de dados individuais](resource-limits-vcore-single-databases.md). Por exemplo, se a piscina elástica estiver a utilizar a Gen5 e o max vCore por base de dados for definido em 2, então o valor máximo simultâneo dos trabalhadores é de 200.  Se o máximo vCore por base de dados for definido para 0,5, então o valor máximo simultâneo dos trabalhadores é de 50, uma vez que na Gen5 existe um máximo de 100 trabalhadores simultâneos por vCore. Para outras definições max vCore por base de dados que sejam inferiores a 1 vCore ou menos, o número de trabalhadores máximos simultâneos é igualmente redimensionado.

### <a name="standard-elastic-pool-limits-continued"></a>Limites do conjunto elástico standard (continuação)

| eDTUs por conjunto | **1200** | **1600** | **2000** | **2500** | **3000** |
|:---|---:|---:|---:| ---: | ---: |
| Armazenamento incluído por piscina (GB) <sup>1</sup> | 1200 | 1600 | 2000 | 2500 | 3.000 |
| Armazenamento máximo por conjunto (GB) | 2560 | 3072 | 3584 | 4096 | 4096 |
| Max In-Memory armazenamento OLTP por piscina (GB) | N/D | N/D | N/D | N/D | N/D |
| DBs max por piscina <sup>2</sup> | 500 | 500 | 500 | 500 | 500 |
| Trabalhadores máximos simultâneos (pedidos) por piscina <sup>3</sup> | 2400 | 3200 | 4000 | 5000 | 6000 |
| Sessões concorrentes max por piscina <sup>3</sup> | 30000 | 30000 | 30000 | 30000 | 30000 |
| Min DTU por opções de base de dados | 0, 10, 20, 50, 100, 200, 300, 400, 800, 1200 | 0, 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600 | 0, 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000 | 0, 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000, 2500 | 0, 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000, 2500, 3000 |
| Max DTU por opções de base de dados | 10, 20, 50, 100, 200, 300, 400, 800, 1200 | 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600 | 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000 | 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000, 2500 | 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000, 2500, 3000 |
| Armazenamento máximo por base de dados (GB) | 1024 | 1024 | 1024 | 1024 | 1024 |
|||||||

<sup>1</sup> Consulte [as opções de preços da Base de Dados SQL](https://azure.microsoft.com/pricing/details/sql-database/elastic/) para obter detalhes sobre o custo adicional incorrido devido a qualquer armazenamento extra a provisionado.

<sup>2</sup> Consulte [a gestão de recursos em piscinas elásticas densas](elastic-pool-resource-management.md) para obter considerações adicionais.

<sup>3</sup> Para os trabalhadores máximos simultâneos (pedidos) para qualquer base de dados individual, consulte [os limites de recursos de base de dados individuais](resource-limits-vcore-single-databases.md). Por exemplo, se a piscina elástica estiver a utilizar a Gen5 e o max vCore por base de dados for definido em 2, então o valor máximo simultâneo dos trabalhadores é de 200.  Se o máximo vCore por base de dados for definido para 0,5, então o valor máximo simultâneo dos trabalhadores é de 50, uma vez que na Gen5 existe um máximo de 100 trabalhadores simultâneos por vCore. Para outras definições max vCore por base de dados que sejam inferiores a 1 vCore ou menos, o número de trabalhadores máximos simultâneos é igualmente redimensionado.

### <a name="premium-elastic-pool-limits"></a>Limites do conjunto elástico premium

| eDTUs por conjunto | **125** | **250** | **500** | **1000** | **1500**|
|:---|---:|---:|---:| ---: | ---: |
| Armazenamento incluído por piscina (GB) <sup>1</sup> | 250 | 500 | 750 | 1024 | 1536 |
| Armazenamento máximo por conjunto (GB) | 1024 | 1024 | 1024 | 1024 | 1536 |
| Max In-Memory armazenamento OLTP por piscina (GB) | 1 | 2 | 4 | 10 | 12 |
| DBs max por piscina <sup>2</sup> | 50 | 100 | 100 | 100 | 100 |
| Trabalhadores máximos simultâneos por piscina (pedidos) <sup>3</sup> | 200 | 400 | 800 | 1600 | 2400 |
| Sessões concorrentes max por piscina <sup>3</sup> | 30000 | 30000 | 30000 | 30000 | 30000 |
| Mínimo de eDTUs por base de dados | 0, 25, 50, 75, 125 | 0, 25, 50, 75, 125, 250 | 0, 25, 50, 75, 125, 250, 500 | 0, 25, 50, 75, 125, 250, 500, 1000 | 0, 25, 50, 75, 125, 250, 500, 1000|
| Máximo de eDTUs por base de dados | 25, 50, 75, 125 | 25, 50, 75, 125, 250 | 25, 50, 75, 125, 250, 500 | 25, 50, 75, 125, 250, 500, 1000 | 25, 50, 75, 125, 250, 500, 1000|
| Armazenamento máximo por base de dados (GB) | 1024 | 1024 | 1024 | 1024 | 1024 |
|||||||

<sup>1</sup> Consulte [as opções de preços da Base de Dados SQL](https://azure.microsoft.com/pricing/details/sql-database/elastic/) para obter detalhes sobre o custo adicional incorrido devido a qualquer armazenamento extra a provisionado.

<sup>2</sup> Consulte [a gestão de recursos em piscinas elásticas densas](elastic-pool-resource-management.md) para obter considerações adicionais.

<sup>3</sup> Para os trabalhadores máximos simultâneos (pedidos) para qualquer base de dados individual, consulte [os limites de recursos de base de dados individuais](resource-limits-vcore-single-databases.md). Por exemplo, se a piscina elástica estiver a utilizar a Gen5 e o max vCore por base de dados for definido em 2, então o valor máximo simultâneo dos trabalhadores é de 200.  Se o máximo vCore por base de dados for definido para 0,5, então o valor máximo simultâneo dos trabalhadores é de 50, uma vez que na Gen5 existe um máximo de 100 trabalhadores simultâneos por vCore. Para outras definições max vCore por base de dados que sejam inferiores a 1 vCore ou menos, o número de trabalhadores máximos simultâneos é igualmente redimensionado.

### <a name="premium-elastic-pool-limits-continued"></a>Limites do conjunto elástico premium (continuação)

| eDTUs por conjunto | **2000** | **2500** | **3000** | **3500** | **4000**|
|:---|---:|---:|---:| ---: | ---: |
| Armazenamento incluído por piscina (GB) <sup>1</sup> | 2048 | 2560 | 3072 | 3548 | 4096 |
| Armazenamento máximo por conjunto (GB) | 2048 | 2560 | 3072 | 3548 | 4096|
| Max In-Memory armazenamento OLTP por piscina (GB) | 16 | 20 | 24 | 28 | 32 |
| DBs max por piscina <sup>2</sup> | 100 | 100 | 100 | 100 | 100 |
| Trabalhadores máximos simultâneos (pedidos) por piscina <sup>3</sup> | 3200 | 4000 | 4800 | 5600 | 6400 |
| Sessões concorrentes max por piscina <sup>3</sup> | 30000 | 30000 | 30000 | 30000 | 30000 |
| Min DTU por opções de base de dados | 0, 25, 50, 75, 125, 250, 500, 1000, 1750 | 0, 25, 50, 75, 125, 250, 500, 1000, 1750 | 0, 25, 50, 75, 125, 250, 500, 1000, 1750 | 0, 25, 50, 75, 125, 250, 500, 1000, 1750 | 0, 25, 50, 75, 125, 250, 500, 1000, 1750, 4000 |
| Max DTU por opções de base de dados | 25, 50, 75, 125, 250, 500, 1000, 1750 | 25, 50, 75, 125, 250, 500, 1000, 1750 | 25, 50, 75, 125, 250, 500, 1000, 1750 | 25, 50, 75, 125, 250, 500, 1000, 1750 | 25, 50, 75, 125, 250, 500, 1000, 1750, 4000 |
| Armazenamento máximo por base de dados (GB) | 1024 | 1024 | 1024 | 1024 | 1024 |
|||||||

<sup>1</sup> Consulte [as opções de preços da Base de Dados SQL](https://azure.microsoft.com/pricing/details/sql-database/elastic/) para obter detalhes sobre o custo adicional incorrido devido a qualquer armazenamento extra a provisionado.

<sup>2</sup> Consulte [a gestão de recursos em piscinas elásticas densas](elastic-pool-resource-management.md) para obter considerações adicionais.

<sup>3</sup> Para os trabalhadores máximos simultâneos (pedidos) para qualquer base de dados individual, consulte [os limites de recursos de base de dados individuais](resource-limits-vcore-single-databases.md). Por exemplo, se a piscina elástica estiver a utilizar a Gen5 e o max vCore por base de dados for definido em 2, então o valor máximo simultâneo dos trabalhadores é de 200.  Se o máximo vCore por base de dados for definido para 0,5, então o valor máximo simultâneo dos trabalhadores é de 50, uma vez que na Gen5 existe um máximo de 100 trabalhadores simultâneos por vCore. Para outras definições max vCore por base de dados que sejam inferiores a 1 vCore ou menos, o número de trabalhadores máximos simultâneos é igualmente redimensionado.

> [!IMPORTANT]
> Mais de 1 TB de armazenamento no nível Premium está atualmente disponível em todas as regiões, exceto: China Leste, China Norte, Alemanha Central e Alemanha Nordeste. Nestas regiões, o máximo de armazenamento no nível Premium é limitado a 1 TB.  Para obter mais informações, consulte [as limitações atuais do P11-P15.](single-database-scale.md#p11-and-p15-constraints-when-max-size-greater-than-1-tb)

Se todas as DTUs de um conjunto elástico forem utilizadas, cada base de dados no conjunto recebe uma quantidade igual de recursos para processar consultas. O serviço Base de Dados SQL fornece equidade de partilha de recursos entre bases de dados, garantindo frações iguais de tempo de computação. A equidade de partilha de recursos de um conjunto elástico é adicional a qualquer quantidade de recursos garantido de outro modo a cada base de dados quando o mínimo de DTUs por base de dados está definido como um valor diferente de zero.

> [!NOTE]
> Para `tempdb` limites, consulte [os limites temporários](/sql/relational-databases/databases/tempdb-database?view=sql-server-2017#tempdb-database-in-sql-database).

### <a name="database-properties-for-pooled-databases"></a>Propriedades da base de dados para bases de dados agrizadas

A tabela seguinte descreve as propriedades para bases de dados aginhadas.

| Propriedade | Descrição |
|:--- |:--- |
| Máximo de eDTUs por base de dados |O número máximo de eDTUs que qualquer base de dados no conjunto pode utilizar, caso estejam disponíveis com base na utilização por outras bases de dados no conjunto. O número máximo de eDTUs por base de dados não é uma garantia de recurso para uma base de dados. Esta definição é uma definição global que se aplica a todas as bases de dados no conjunto. Defina um número máximo de eDTUs suficientemente elevado para processar picos na utilização de base de dados. É esperado algum grau de consolidação excessiva, uma vez que, geralmente, o conjunto assume padrões de utilização a frio e a quente para bases de dados em que todas as bases de dados não atingem o pico em simultâneo. Por exemplo, suponha que o pico de utilização por base de dados é 20 eDTUs e apenas 20% das 100 bases de dados no conjunto atingem o pico ao mesmo tempo. Se o número máximo de eDTUs por base de dados estiver definido como 20 eDTUs, é razoável sobreconsolidar o conjunto em 5 vezes e definir o número de eDTUs por conjunto como 400. |
| Mínimo de eDTUs por base de dados |O número mínimo de eDTUs que é garantido a qualquer base de dados no conjunto. Esta definição é uma definição global que se aplica a todas as bases de dados no conjunto. O mínimo de eDTUs por base de dados pode ser definido como 0, que é também o valor predefinido. Esta propriedade é definida como qualquer valor entre 0 e a utilização média de eDTUs por base de dados. O produto do número de bases de dados no conjunto e o número mínimo de eDTUs por base de dados não pode exceder as eDTUs por conjunto. Por exemplo, se um conjunto tiver 20 bases de dados e o número mínimo de eDTUs por base de dados definido como 10 eDTUs, as eDTUs por conjunto têm de ser, pelo menos, 200 eDTUs. |
| Armazenamento máximo por base de dados |O tamanho máximo da base de dados definido pelo utilizador para uma base de dados numa piscina. No entanto, as bases de dados partilhadas partilham o armazenamento de piscinas atribuído. Mesmo que o armazenamento máximo total *por base de dados* seja superior ao espaço total disponível de armazenamento *disponível da piscina,* o espaço total realmente utilizado por todas as bases de dados não poderá exceder o limite de piscina disponível. O tamanho máximo da base de dados refere-se ao tamanho máximo dos ficheiros de dados e não inclui o espaço utilizado pelos ficheiros de registo. |
|||

## <a name="next-steps"></a>Passos seguintes

* Para limites de recursos vCore para uma única base de [dados, consulte os limites de recursos para bases de dados únicas utilizando o modelo de compra vCore](resource-limits-vcore-single-databases.md)
* Para limites de recursos DTU para uma única base de [dados, consulte os limites de recursos para bases de dados únicas utilizando o modelo de compra do DTU](resource-limits-dtu-single-databases.md)
* Para limites de recursos vCore para piscinas elásticas, consulte [os limites de recursos para piscinas elásticas utilizando o modelo de compra vCore](resource-limits-vcore-elastic-pools.md)
* Para limites de recursos para casos geridos em Instância Gerida Azure SQL, consulte [os limites de recursos de instância gerida SQL](../managed-instance/resource-limits.md).
* Para obter informações sobre os limites gerais do Azure, consulte [os limites de subscrição e serviços, quotas e restrições da Azure.](../../azure-resource-manager/management/azure-subscription-service-limits.md)
* Para obter informações sobre os limites de recursos num servidor lógico do SQL, consulte [a visão geral dos limites de recursos num servidor lógico SQL](resource-limits-logical-server.md) para obter informações sobre limites nos níveis de servidor e subscrição.