---
title: limites de recursos vCore – pools elásticos
description: Esta página descreve alguns limites comuns de recursos vCore para pools elásticos no banco de dados SQL do Azure.
services: sql-database
ms.service: sql-database
ms.subservice: elastic-pools
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: oslake
ms.author: moslake
ms.reviewer: carlrab, sstein
ms.date: 01/09/2020
ms.openlocfilehash: f6b7797fbebd3d1df3da3405926543d716e584f4
ms.sourcegitcommit: f53cd24ca41e878b411d7787bd8aa911da4bc4ec
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/10/2020
ms.locfileid: "75835104"
---
# <a name="resource-limits-for-elastic-pools-using-the-vcore-purchasing-model"></a>Limites de recursos para pools elásticos usando o modelo de compra vCore

Este artigo fornece os limites de recursos detalhados para os pools elásticos do banco de dados SQL do Azure e bancos de dados em pool usando o modelo de compra vCore.

Para limites de modelo de compra de DTU, consulte [limites de recursos de DTU do banco de dados SQL – pools elásticos](sql-database-dtu-resource-limits-elastic-pools.md).

> [!IMPORTANT]
> Em algumas circunstâncias, poderá ter reduzir uma base de dados para recuperar espaço não utilizado. Para obter mais informações, consulte [gerir o espaço de ficheiro na base de dados do Azure SQL](sql-database-file-space-management.md).

Você pode definir a camada de serviço, o tamanho da computação e o valor de armazenamento usando o [portal do Azure](sql-database-elastic-pool-manage.md#azure-portal-manage-elastic-pools-and-pooled-databases), o [PowerShell](sql-database-elastic-pool-manage.md#powershell-manage-elastic-pools-and-pooled-databases), o [CLI do Azure](sql-database-elastic-pool-manage.md#azure-cli-manage-elastic-pools-and-pooled-databases)ou a [API REST](sql-database-elastic-pool-manage.md#rest-api-manage-elastic-pools-and-pooled-databases).

> [!IMPORTANT]
> Para obter diretrizes e considerações sobre o dimensionamento, consulte [dimensionar um pool elástico](sql-database-elastic-pool-scale.md)

## <a name="general-purpose---provisioned-compute---gen4"></a>Computação provisionada de uso geral-Gen4

> [!IMPORTANT]
> Novos bancos de dados Gen4 não têm mais suporte nas regiões leste da Austrália ou sul do Brasil.

### <a name="general-purpose-service-tier-generation-4-compute-platform-part-1"></a>Camada de serviço de uso geral: plataforma de computação de geração 4 (parte 1)

|Tamanho da computação|GP_Gen4_1|GP_Gen4_2|GP_Gen4_3|GP_Gen4_4|GP_Gen4_5|GP_Gen4_6
|:--- | --: |--: |--: |--: |--: |--: |
|Geração de computação|Gen4|Gen4|Gen4|Gen4|Gen4|Gen4|
|vCores|1|2|3|4|5|6|
|Memória (GB)|7|14|21|28|35|42|
|Número de DBs máximo por conjunto|100|200|500|500|500|500|
|Suporte a Columnstore|Sim|Sim|Sim|Sim|Sim|Sim|
|Armazenamento OLTP na memória (GB)|N/A|N/A|N/A|N/A|N/A|N/A|
|Tamanho máximo de dados (GB)|512|756|1536|1536|1536|2048|
|Tamanho máximo do log|154|227|461|461|461|614|
|Tamanho máximo de dados de TempDB (GB)|32|64|96|128|160|192|
|Tipo de armazenamento|Armazenamento Premium (remoto)|Armazenamento Premium (remoto)|Armazenamento Premium (remoto)|Armazenamento Premium (remoto)|Armazenamento Premium (remoto)|Armazenamento Premium (remoto)|
|Latência de e/s (aproximada)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|
|IOPS de dados máximo por pool *|400|800|1200|1600|2000|2400|
|Taxa máxima de log por pool (MBps)|4.7|9.4|14,1|18,8|23,4|28,1|
|Máximo de trabalhos simultâneos por pool (solicitações) * * |210|420|630|840|1050|1260|
|Máximo de logons simultâneos por pool * * |210|420|630|840|1050|1260|
|Sessões simultâneas máx.|30,000|30,000|30,000|30,000|30,000|30,000|
|Opções vCore de pool elástico mínimo/máximo por banco de dados|0, 0,25, 0,5, 1|0, 0,25, 0,5, 1, 2|0, 0,25, 0,5, 1... 3|0, 0,25, 0,5, 1... 4|0, 0,25, 0,5, 1... 5|0, 0,25, 0,5, 1... 6|
|Número de réplicas|1|1|1|1|1|1|
|Multi-AZ|N/A|N/A|N/A|N/A|N/A|N/A|
|Escalamento Horizontal de Leituras|N/A|N/A|N/A|N/A|N/A|N/A|
|Armazenamento de backup incluído|tamanho do banco de BD 1X|tamanho do banco de BD 1X|tamanho do banco de BD 1X|tamanho do banco de BD 1X|tamanho do banco de BD 1X|tamanho do banco de BD 1X|

\* o valor máximo para tamanhos de e/s variando entre 8 KB e 64 KB. IOPS reais são dependentes da carga de trabalho. Para obter detalhes, consulte [governança de e/s de dados](sql-database-resource-limits-database-server.md#resource-governance).

\*\* para o máximo de trabalhos simultâneos (solicitações) para qualquer banco de dados individual, consulte [limites de recurso de banco de dados único](sql-database-vcore-resource-limits-single-databases.md). Por exemplo, se o pool elástico estiver usando Gen5 e o vCore máximo por banco de dados for definido como 2, o valor máximo de trabalhos simultâneos será de 200.  Se o vCore máximo por banco de dados for definido como 0,5, o valor máximo de trabalhos simultâneos será 50, pois, em Gen5, há um máximo de 100 trabalhos simultâneos por vCore.  Para outras configurações de vCore máxima por banco de dados que são menos de 1 vCore ou menos, o número máximo de trabalhos simultâneos é redimensionado de forma semelhante.

### <a name="general-purpose-service-tier-generation-4-compute-platform-part-2"></a>Camada de serviço de uso geral: plataforma de computação de geração 4 (parte 2)

|Tamanho da computação|GP_Gen4_7|GP_Gen4_8|GP_Gen4_9|GP_Gen4_10|GP_Gen4_16|GP_Gen4_24|
|:--- | --: |--: |--: |--: |--: |--: |
|Geração de computação|Gen4|Gen4|Gen4|Gen4|Gen4|Gen4|
|vCores|7|8|9|10|16|24|
|Memória (GB)|49|56|63|70|112|159,5|
|Número de DBs máximo por conjunto|500|500|500|500|500|500|
|Suporte a Columnstore|Sim|Sim|Sim|Sim|Sim|Sim|
|Armazenamento OLTP na memória (GB)|N/A|N/A|N/A|N/A|N/A|N/A|
|Tamanho máximo de dados (GB)|2048|2048|2048|2048|3584|4096|
|Tamanho máximo do log (GB)|614|614|614|614|1075|1229|
|Tamanho máximo de dados de TempDB (GB)|224|256|288|320|512|768|
|Tipo de armazenamento|Armazenamento Premium (remoto)|Armazenamento Premium (remoto)|Armazenamento Premium (remoto)|Armazenamento Premium (remoto)|Armazenamento Premium (remoto)|Armazenamento Premium (remoto)|
|Latência de e/s (aproximada)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|
|IOPS de dados máximo por pool *|2800|3200|3600|4000|6400|9600|
|Taxa máxima de log por pool (MBps)|32,8|37,5|37,5|37,5|37,5|37,5|
|Máximo de trabalhos simultâneos por pool (solicitações) *|1470|1680|1890|2100|3360|5040|
|Pool de logons simultâneos máximos (solicitações) *|1470|1680|1890|2100|3360|5040|
|Sessões simultâneas máx.|30,000|30,000|30,000|30,000|30,000|30,000|
|Opções vCore de pool elástico mínimo/máximo por banco de dados|0, 0,25, 0,5, 1... 7|0, 0,25, 0,5, 1... 8|0, 0,25, 0,5, 1... 9|0, 0,25, 0,5, 1... 10|0, 0,25, 0,5, 1... 10, 16|0, 0,25, 0,5, 1... 10, 16, 24|
|Número de réplicas|1|1|1|1|1|1|
|Multi-AZ|N/A|N/A|N/A|N/A|N/A|N/A|
|Escalamento Horizontal de Leituras|N/A|N/A|N/A|N/A|N/A|N/A|
|Armazenamento de backup incluído|tamanho do banco de BD 1X|tamanho do banco de BD 1X|tamanho do banco de BD 1X|tamanho do banco de BD 1X|tamanho do banco de BD 1X|tamanho do banco de BD 1X|

\* para o máximo de trabalhos simultâneos (solicitações) para qualquer banco de dados individual, consulte [limites de recurso de banco de dados único](sql-database-vcore-resource-limits-single-databases.md). Por exemplo, se o pool elástico estiver usando Gen5 e o vCore máximo por banco de dados for definido como 2, o valor máximo de trabalhos simultâneos será de 200.  Se o vCore máximo por banco de dados for definido como 0,5, o valor máximo de trabalhos simultâneos será 50, pois, em Gen5, há um máximo de 100 trabalhos simultâneos por vCore.  Para outras configurações de vCore máxima por banco de dados que são menos de 1 vCore ou menos, o número máximo de trabalhos simultâneos é redimensionado de forma semelhante.

## <a name="general-purpose---provisioned-compute---gen5"></a>Computação provisionada de uso geral-Gen5

### <a name="general-purpose-service-tier-generation-5-compute-platform-part-1"></a>Camada de serviço de uso geral: plataforma de computação de geração 5 (parte 1)

|Tamanho da computação|GP_Gen5_2|GP_Gen5_4|GP_Gen5_6|GP_Gen5_8|GP_Gen5_10|GP_Gen5_12|GP_Gen5_14|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|Geração de computação|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|
|vCores|2|4|6|8|10|12|14|
|Memória (GB)|10,4|20,8|31,1|41,5|51,9|62,3|72,7|
|Número de DBs máximo por conjunto|100|200|500|500|500|500|500|
|Suporte a Columnstore|Sim|Sim|Sim|Sim|Sim|Sim|Sim|
|Armazenamento OLTP na memória (GB)|N/A|N/A|N/A|N/A|N/A|N/A|N/A|
|Tamanho máximo de dados (GB)|512|756|1536|1536|1536|2048|2048|
|Tamanho máximo do log (GB)|154|227|461|461|461|614|614|
|Tamanho máximo de dados de TempDB (GB)|64|128|192|256|320|384|448|
|Tipo de armazenamento|Armazenamento Premium (remoto)|Armazenamento Premium (remoto)|Armazenamento Premium (remoto)|Armazenamento Premium (remoto)|Armazenamento Premium (remoto)|Armazenamento Premium (remoto)|Armazenamento Premium (remoto)|
|Latência de e/s (aproximada)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|
|IOPS de dados máximo por pool *|800|1600|2400|3200|4000|4800|5600|
|Taxa máxima de log por pool (MBps)|9.4|18,8|28,1|37,5|37,5|37,5|37,5|
|Máximo de trabalhos simultâneos por pool (solicitações) * *|210|420|630|840|1050|1260|1470|
|Máximo de logons simultâneos por pool (solicitações) * *|210|420|630|840|1050|1260|1470|
|Sessões simultâneas máx.|30,000|30,000|30,000|30,000|30,000|30,000|30,000|
|Opções vCore de pool elástico mínimo/máximo por banco de dados|0, 0,25, 0,5, 1, 2|0, 0,25, 0,5, 1... 4|0, 0,25, 0,5, 1... 6|0, 0,25, 0,5, 1... 8|0, 0,25, 0,5, 1... 10|0, 0,25, 0,5, 1... 12|0, 0,25, 0,5, 1... 14|
|Número de réplicas|1|1|1|1|1|1|1|
|Multi-AZ|N/A|N/A|N/A|N/A|N/A|N/A|N/A|
|Escalamento Horizontal de Leituras|N/A|N/A|N/A|N/A|N/A|N/A|N/A|
|Armazenamento de backup incluído|tamanho do banco de BD 1X|tamanho do banco de BD 1X|tamanho do banco de BD 1X|tamanho do banco de BD 1X|tamanho do banco de BD 1X|tamanho do banco de BD 1X|tamanho do banco de BD 1X|

\* o valor máximo para tamanhos de e/s variando entre 8 KB e 64 KB. IOPS reais são dependentes da carga de trabalho. Para obter detalhes, consulte [governança de e/s de dados](sql-database-resource-limits-database-server.md#resource-governance).

\*\* para o máximo de trabalhos simultâneos (solicitações) para qualquer banco de dados individual, consulte [limites de recurso de banco de dados único](sql-database-vcore-resource-limits-single-databases.md). Por exemplo, se o pool elástico estiver usando Gen5 e o vCore máximo por banco de dados for definido como 2, o valor máximo de trabalhos simultâneos será de 200.  Se o vCore máximo por banco de dados for definido como 0,5, o valor máximo de trabalhos simultâneos será 50, pois, no Gen5, há um máximo de 100 trabalhos simultâneos por vCore, para outras configurações de vCore máxima por banco de dados que são menos de 1 vCore ou menos, o número máximo de trabalhos simultâneos é semelhante  redimensionados.

### <a name="general-purpose-service-tier-generation-5-compute-platform-part-2"></a>Camada de serviço de uso geral: plataforma de computação de geração 5 (parte 2)

|Tamanho da computação|GP_Gen5_16|GP_Gen5_18|GP_Gen5_20|GP_Gen5_24|GP_Gen5_32|GP_Gen5_40|GP_Gen5_80|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|Geração de computação|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|
|vCores|16|18|20|24|32|40|80|
|Memória (GB)|83|93,4|103,8|124,6|166,1|207,6|415,2|
|Número de DBs máximo por conjunto|500|500|500|500|500|500|500|
|Suporte a Columnstore|Sim|Sim|Sim|Sim|Sim|Sim|Sim|
|Armazenamento OLTP na memória (GB)|N/A|N/A|N/A|N/A|N/A|N/A|N/A|
|Tamanho máximo de dados (GB)|2048|3072|3072|3072|4096|4096|4096|
|Tamanho máximo do log (GB)|614|922|922|922|1229|1229|1229|
|Tamanho máximo de dados de TempDB (GB)|512|576|640|768|1024|1280|2560|
|Tipo de armazenamento|Armazenamento Premium (remoto)|Armazenamento Premium (remoto)|Armazenamento Premium (remoto)|Armazenamento Premium (remoto)|Armazenamento Premium (remoto)|Armazenamento Premium (remoto)|Armazenamento Premium (remoto)|
|Latência de e/s (aproximada)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|
|IOPS de dados máximo por pool * |6,400|7\.200|8,000|9600|12,800|16,000|32,000|
|Taxa máxima de log por pool (MBps)|37,5|37,5|37,5|37,5|37,5|37,5|37,5|
|Máximo de trabalhos simultâneos por pool (solicitações) * *|1680|1890|2100|2520|3360|4200|8400|
|Máximo de logons simultâneos por pool (solicitações) * *|1680|1890|2100|2520|3360|4200|8400|
|Sessões simultâneas máx.|30,000|30,000|30,000|30,000|30,000|30,000|30,000|
|Opções vCore de pool elástico mínimo/máximo por banco de dados|0, 0,25, 0,5, 1... 16|0, 0,25, 0,5, 1... 18|0, 0,25, 0,5, 1... 20|0, 0,25, 0,5, 1... 20, 24|0, 0,25, 0,5, 1... 20, 24, 32|0, 0,25, 0,5, 1... 16, 24, 32, 40|0, 0,25, 0,5, 1... 16, 24, 32, 40, 80|
|Número de réplicas|1|1|1|1|1|1|1|
|Multi-AZ|N/A|N/A|N/A|N/A|N/A|N/A|N/A|
|Escalamento Horizontal de Leituras|N/A|N/A|N/A|N/A|N/A|N/A|N/A|
|Armazenamento de backup incluído|tamanho do banco de BD 1X|tamanho do banco de BD 1X|tamanho do banco de BD 1X|tamanho do banco de BD 1X|tamanho do banco de BD 1X|tamanho do banco de BD 1X|tamanho do banco de BD 1X|

\* o valor máximo para tamanhos de e/s variando entre 8 KB e 64 KB. IOPS reais são dependentes da carga de trabalho. Para obter detalhes, consulte [governança de e/s de dados](sql-database-resource-limits-database-server.md#resource-governance).

\*\* para o máximo de trabalhos simultâneos (solicitações) para qualquer banco de dados individual, consulte [limites de recurso de banco de dados único](sql-database-vcore-resource-limits-single-databases.md). Por exemplo, se o pool elástico estiver usando Gen5 e o vCore máximo por banco de dados for definido como 2, o valor máximo de trabalhos simultâneos será de 200.  Se o vCore máximo por banco de dados for definido como 0,5, o valor máximo de trabalhos simultâneos será 50, pois, em Gen5, há um máximo de 100 trabalhos simultâneos por vCore.  Para outras configurações de vCore máxima por banco de dados que são menos de 1 vCore ou menos, o número máximo de trabalhos simultâneos é redimensionado de forma semelhante.

## <a name="general-purpose---provisioned-compute---fsv2-series"></a>Computação provisionada de uso geral-série Fsv2

### <a name="fsv2-series-compute-generation-preview"></a>Geração de computação da série Fsv2 (visualização)

|Tamanho da computação|GP_Fsv2_72|
|:--- | --: |
|Geração de computação|Série Fsv2|
|vCores|72|
|Memória (GB)|136,2|
|Número de DBs máximo por conjunto|500|
|Suporte a Columnstore|Sim|
|Armazenamento OLTP na memória (GB)|N/A|
|Tamanho máximo de dados (GB)|4096|
|Tamanho máximo do log (GB)|1024|
|Tamanho máximo de dados de TempDB (GB)|333|
|Tipo de armazenamento|Armazenamento Premium (remoto)|
|Latência de e/s (aproximada)|5-7 ms (gravação)<br>5-10 ms (leitura)|
|IOPS de dados máximo por pool *|16,000|
|Taxa máxima de log por pool (MBps)|37,5|
|Máximo de trabalhos simultâneos por pool (solicitações) * *|3780|
|Máximo de logons simultâneos por pool (solicitações) * *|3780|
|Sessões simultâneas máx.|30,000|
|Opções vCore de pool elástico mínimo/máximo por banco de dados|0-72|
|Número de réplicas|1|
|Multi-AZ|N/A|
|Escalamento Horizontal de Leituras|N/A|
|Armazenamento de backup incluído|tamanho do banco de BD 1X|

\* o valor máximo para tamanhos de e/s variando entre 8 KB e 64 KB. IOPS reais são dependentes da carga de trabalho. Para obter detalhes, consulte [governança de e/s de dados](sql-database-resource-limits-database-server.md#resource-governance).

\*\* para o máximo de trabalhos simultâneos (solicitações) para qualquer banco de dados individual, consulte [limites de recurso de banco de dados único](sql-database-vcore-resource-limits-single-databases.md). Por exemplo, se o pool elástico estiver usando Gen5 e o vCore máximo por banco de dados for definido como 2, o valor máximo de trabalhos simultâneos será de 200.  Se o vCore máximo por banco de dados for definido como 0,5, o valor máximo de trabalhos simultâneos será 50, pois, em Gen5, há um máximo de 100 trabalhos simultâneos por vCore.  Para outras configurações de vCore máxima por banco de dados que são menos de 1 vCore ou menos, o número máximo de trabalhos simultâneos é redimensionado de forma semelhante.

## <a name="business-critical---provisioned-compute---gen4"></a>Comercialmente crítico-computação provisionada-Gen4

> [!IMPORTANT]
> Novos bancos de dados Gen4 não têm mais suporte nas regiões leste da Austrália ou sul do Brasil.

### <a name="business-critical-service-tier-generation-4-compute-platform-part-1"></a>Camada de serviço comercialmente crítica: plataforma de computação de geração 4 (parte 1)

|Tamanho da computação|BC_Gen4_2|BC_Gen4_3|BC_Gen4_4|BC_Gen4_5|BC_Gen4_6|
|:--- | --: |--: |--: |--: |--: |--: |
|Geração de computação|Gen4|Gen4|Gen4|Gen4|Gen4|
|vCores|2|3|4|5|6|
|Memória (GB)|14|21|28|35|42|
|Número de DBs máximo por conjunto|100|100|100|100|100|
|Suporte a Columnstore|Sim|Sim|Sim|Sim|Sim|
|Armazenamento OLTP na memória (GB)|2|3|4|5|6|
|Tipo de armazenamento|SSD local|SSD local|SSD local|SSD local|SSD local|
|Tamanho máximo de dados (GB)|1024|1024|1024|1024|1024|
|Tamanho máximo do log (GB)|307|307|307|307|307|
|Tamanho máximo de dados de TempDB (GB)|64|96|128|160|192|
|Latência de e/s (aproximada)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|
|IOPS de dados máximo por pool *|9000|13.500|18 000|22.500|27.000|
|Taxa máxima de log por pool (MBps)|20|30|40|50|60|
|Máximo de trabalhos simultâneos por pool (solicitações) * *|420|630|840|1050|1260|
|Máximo de logons simultâneos por pool (solicitações) * *|420|630|840|1050|1260|
|Sessões simultâneas máx.|30,000|30,000|30,000|30,000|30,000|
|Opções vCore de pool elástico mínimo/máximo por banco de dados|0, 0,25, 0,5, 1, 2|0, 0,25, 0,5, 1... 3|0, 0,25, 0,5, 1... 4|0, 0,25, 0,5, 1... 5|0, 0,25, 0,5, 1... 6|
|Número de réplicas|4|4|4|4|4|
|Multi-AZ|Sim|Sim|Sim|Sim|Sim|
|Escalamento Horizontal de Leituras|Sim|Sim|Sim|Sim|Sim|
|Armazenamento de backup incluído|tamanho do banco de BD 1X|tamanho do banco de BD 1X|tamanho do banco de BD 1X|tamanho do banco de BD 1X|tamanho do banco de BD 1X|

\* o valor máximo para tamanhos de e/s variando entre 8 KB e 64 KB. IOPS reais são dependentes da carga de trabalho. Para obter detalhes, consulte [governança de e/s de dados](sql-database-resource-limits-database-server.md#resource-governance).

\*\* para o máximo de trabalhos simultâneos (solicitações) para qualquer banco de dados individual, consulte [limites de recurso de banco de dados único](sql-database-vcore-resource-limits-single-databases.md). Por exemplo, se o pool elástico estiver usando Gen5 e o vCore máximo por banco de dados for definido como 2, o valor máximo de trabalhos simultâneos será de 200.  Se o vCore máximo por banco de dados for definido como 0,5, o valor máximo de trabalhos simultâneos será 50, pois, em Gen5, há um máximo de 100 trabalhos simultâneos por vCore.  Para outras configurações de vCore máxima por banco de dados que são menos de 1 vCore ou menos, o número máximo de trabalhos simultâneos é redimensionado de forma semelhante.

### <a name="business-critical-service-tier-generation-4-compute-platform-part-2"></a>Camada de serviço comercialmente crítica: plataforma de computação de geração 4 (parte 2)

|Tamanho da computação|BC_Gen4_7|BC_Gen4_8|BC_Gen4_9|BC_Gen4_10|BC_Gen4_16|BC_Gen4_24|
|:--- | --: |--: |--: |--: |--: |--: |
|Geração de computação|Gen4|Gen4|Gen4|Gen4|Gen4|Gen4|
|vCores|7|8|9|10|16|24|
|Memória (GB)|49|56|63|70|112|159,5|
|Número de DBs máximo por conjunto|100|100|100|100|100|100|
|Suporte a Columnstore|N/A|N/A|N/A|N/A|N/A|N/A|
|Armazenamento OLTP na memória (GB)|7|8|9.5|11|20|36|
|Tipo de armazenamento|SSD local|SSD local|SSD local|SSD local|SSD local|SSD local|
|Tamanho máximo de dados (GB)|1024|1024|1024|1024|1024|1024|
|Tamanho máximo do log (GB)|307|307|307|307|307|307|
|Tamanho máximo de dados de TempDB (GB)|224|256|288|320|512|768|
|Latência de e/s (aproximada)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|
|IOPS de dados máximo por pool *|31.500|36.000|40.500|45,000|72.000|96.000|
|Taxa máxima de log por pool (MBps)|70|80|80|80|80|80|
|Máximo de trabalhos simultâneos por pool (solicitações) * *|1470|1680|1890|2100|3360|5040|
|Máximo de logons simultâneos por pool (solicitações) * *|1470|1680|1890|2100|3360|5040|
|Sessões simultâneas máx.|30,000|30,000|30,000|30,000|30,000|30,000|
|Opções vCore de pool elástico mínimo/máximo por banco de dados|0, 0,25, 0,5, 1... 7|0, 0,25, 0,5, 1... 8|0, 0,25, 0,5, 1... 9|0, 0,25, 0,5, 1... 10|0, 0,25, 0,5, 1... 10, 16|0, 0,25, 0,5, 1... 10, 16, 24|
|Número de réplicas|4|4|4|4|4|4|
|Multi-AZ|Sim|Sim|Sim|Sim|Sim|Sim|
|Escalamento Horizontal de Leituras|Sim|Sim|Sim|Sim|Sim|Sim|
|Armazenamento de backup incluído|tamanho do banco de BD 1X|tamanho do banco de BD 1X|tamanho do banco de BD 1X|tamanho do banco de BD 1X|tamanho do banco de BD 1X|tamanho do banco de BD 1X|

\* o valor máximo para tamanhos de e/s variando entre 8 KB e 64 KB. IOPS reais são dependentes da carga de trabalho. Para obter detalhes, consulte [governança de e/s de dados](sql-database-resource-limits-database-server.md#resource-governance).

\*\* para o máximo de trabalhos simultâneos (solicitações) para qualquer banco de dados individual, consulte [limites de recurso de banco de dados único](sql-database-vcore-resource-limits-single-databases.md). Por exemplo, se o pool elástico estiver usando Gen5 e o vCore máximo por banco de dados for definido como 2, o valor máximo de trabalhos simultâneos será de 200.  Se o vCore máximo por banco de dados for definido como 0,5, o valor máximo de trabalhos simultâneos será 50, pois, em Gen5, há um máximo de 100 trabalhos simultâneos por vCore.  Para outras configurações de vCore máxima por banco de dados que são menos de 1 vCore ou menos, o número máximo de trabalhos simultâneos é redimensionado de forma semelhante.

## <a name="business-critical---provisioned-compute---gen5"></a>Comercialmente crítico-computação provisionada-Gen5

### <a name="business-critical-service-tier-generation-5-compute-platform-part-1"></a>Camada de serviço comercialmente crítica: plataforma de computação de geração 5 (parte 1)

|Tamanho da computação|BC_Gen5_4|BC_Gen5_6|BC_Gen5_8|BC_Gen5_10|BC_Gen5_12|BC_Gen5_14|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|Geração de computação|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|
|vCores|4|6|8|10|12|14|
|Memória (GB)|20,8|31,1|41,5|51,9|62,3|72,7|
|Número de DBs máximo por conjunto|100|100|100|100|100|100|
|Suporte a Columnstore|Sim|Sim|Sim|Sim|Sim|Sim|
|Armazenamento OLTP na memória (GB)|3,14|4,71|6,28|8,65|11, 2|13,39|
|Tamanho máximo de dados (GB)|1024|1536|1536|1536|3072|3072|
|Tamanho máximo do log (GB)|307|307|461|461|922|922|
|Tamanho máximo de dados de TempDB (GB)|128|192|256|320|384|448|
|Tipo de armazenamento|SSD local|SSD local|SSD local|SSD local|SSD local|SSD local|
|Latência de e/s (aproximada)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|
|IOPS de dados máximo por pool *|18 000|27.000|36.000|45,000|54,000|63.000|
|Taxa máxima de log por pool (MBps)|60|90|120|120|120|120|
|Máximo de trabalhos simultâneos por pool (solicitações) * *|420|630|840|1050|1260|1470|
|Máximo de logons simultâneos por pool (solicitações) * *|420|630|840|1050|1260|1470|
|Sessões simultâneas máx.|30,000|30,000|30,000|30,000|30,000|30,000|
|Opções vCore de pool elástico mínimo/máximo por banco de dados|0, 0,25, 0,5, 1... 4|0, 0,25, 0,5, 1... 6|0, 0,25, 0,5, 1... 8|0, 0,25, 0,5, 1... 10|0, 0,25, 0,5, 1... 12|0, 0,25, 0,5, 1... 14|
|Número de réplicas|4|4|4|4|4|4|
|Multi-AZ|Sim|Sim|Sim|Sim|Sim|Sim|
|Escalamento Horizontal de Leituras|Sim|Sim|Sim|Sim|Sim|Sim|
|Armazenamento de backup incluído|tamanho do banco de BD 1X|tamanho do banco de BD 1X|tamanho do banco de BD 1X|tamanho do banco de BD 1X|tamanho do banco de BD 1X|tamanho do banco de BD 1X|

\* o valor máximo para tamanhos de e/s variando entre 8 KB e 64 KB. IOPS reais são dependentes da carga de trabalho. Para obter detalhes, consulte [governança de e/s de dados](sql-database-resource-limits-database-server.md#resource-governance).

\*\* para o máximo de trabalhos simultâneos (solicitações) para qualquer banco de dados individual, consulte [limites de recurso de banco de dados único](sql-database-vcore-resource-limits-single-databases.md). Por exemplo, se o pool elástico estiver usando Gen5 e o vCore máximo por banco de dados for definido como 2, o valor máximo de trabalhos simultâneos será de 200.  Se o vCore máximo por banco de dados for definido como 0,5, o valor máximo de trabalhos simultâneos será 50, pois, em Gen5, há um máximo de 100 trabalhos simultâneos por vCore.  Para outras configurações de vCore máxima por banco de dados que são menos de 1 vCore ou menos, o número máximo de trabalhos simultâneos é redimensionado de forma semelhante.

### <a name="business-critical-service-tier-generation-5-compute-platform-part-2"></a>Camada de serviço comercialmente crítica: plataforma de computação de geração 5 (parte 2)

|Tamanho da computação|BC_Gen5_16|BC_Gen5_18|BC_Gen5_20|BC_Gen5_24|BC_Gen5_32|BC_Gen5_40|BC_Gen5_80|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|Geração de computação|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|
|vCores|16|18|20|24|32|40|80|
|Memória (GB)|83|93,4|103,8|124,6|166,1|207,6|415,2|
|Número de DBs máximo por conjunto|100|100|100|100|100|100|100|
|Suporte a Columnstore|Sim|Sim|Sim|Sim|Sim|Sim|Sim|
|Armazenamento OLTP na memória (GB)|15,77|18,14|20,51|25,25|37,94|52,23|131,68|
|Tamanho máximo de dados (GB)|3072|3072|3072|4096|4096|4096|4096|
|Tamanho máximo do log (GB)|922|922|922|1229|1229|1229|1229|
|Tamanho máximo de dados de TempDB (GB)|512|576|640|768|1024|1280|2560|
|Tipo de armazenamento|SSD local|SSD local|SSD local|SSD local|SSD local|SSD local|SSD local|
|Latência de e/s (aproximada)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|
|IOPS de dados máximo por pool *|72.000|81.000|90,000|108.000|144.000|180,000|256.000|
|Taxa máxima de log por pool (MBps)|120|120|120|120|120|120|120|
|Máximo de trabalhos simultâneos por pool (solicitações) * *|1680|1890|2100|2520|3360|4200|8400|
|Máximo de logons simultâneos por pool (solicitações) * *|1680|1890|2100|2520|3360|4200|8400|
|Sessões simultâneas máx.|30,000|30,000|30,000|30,000|30,000|30,000|30,000|
|Opções vCore de pool elástico mínimo/máximo por banco de dados|0, 0,25, 0,5, 1... 16|0, 0,25, 0,5, 1... 18|0, 0,25, 0,5, 1... 20|0, 0,25, 0,5, 1... 20, 24|0, 0,25, 0,5, 1... 20, 24, 32|0, 0,25, 0,5, 1... 20, 24, 32, 40|0, 0,25, 0,5, 1... 20, 24, 32, 40, 80|
|Número de réplicas|4|4|4|4|4|4|4|
|Multi-AZ|Sim|Sim|Sim|Sim|Sim|Sim|Sim|
|Escalamento Horizontal de Leituras|Sim|Sim|Sim|Sim|Sim|Sim|Sim|
|Armazenamento de backup incluído|tamanho do banco de BD 1X|tamanho do banco de BD 1X|tamanho do banco de BD 1X|tamanho do banco de BD 1X|tamanho do banco de BD 1X|tamanho do banco de BD 1X|tamanho do banco de BD 1X|

\* o valor máximo para tamanhos de e/s variando entre 8 KB e 64 KB. IOPS reais são dependentes da carga de trabalho. Para obter detalhes, consulte [governança de e/s de dados](sql-database-resource-limits-database-server.md#resource-governance).

\*\* para o máximo de trabalhos simultâneos (solicitações) para qualquer banco de dados individual, consulte [limites de recurso de banco de dados único](sql-database-vcore-resource-limits-single-databases.md). Por exemplo, se o pool elástico estiver usando Gen5 e o vCore máximo por banco de dados for definido como 2, o valor máximo de trabalhos simultâneos será de 200.  Se o vCore máximo por banco de dados for definido como 0,5, o valor máximo de trabalhos simultâneos será 50, pois, em Gen5, há um máximo de 100 trabalhos simultâneos por vCore.  Para outras configurações de vCore máxima por banco de dados que são menos de 1 vCore ou menos, o número máximo de trabalhos simultâneos é redimensionado de forma semelhante.

## <a name="business-critical---provisioned-compute---m-series"></a>Computação comercialmente crítica-série M

### <a name="m-series-compute-generation-preview"></a>Geração de computação da série M (versão prévia)

|Tamanho da computação|BC_M_128|
|:--- | --: |
|Geração de computação|Série M|
|vCores|128|
|Memória (GB)|3767,1|
|Número de DBs máximo por conjunto|100|
|Suporte a Columnstore|Sim|
|Armazenamento OLTP na memória (GB)|1768|
|Tamanho máximo de dados (GB)|4096|
|Tamanho máximo do log (GB)|2048|
|Tamanho máximo de dados de TempDB (GB)|4096|
|Tipo de armazenamento|SSD local|
|Latência de e/s (aproximada)|1-2 ms (gravação)<br>1-2 ms (leitura)|
|IOPS de dados máximo por pool *|200,000|
|Taxa máxima de log por pool (MBps)|333|
|Máximo de trabalhos simultâneos por pool (solicitações) *|13.440|
|Máximo de logons simultâneos por pool (solicitações) *|13.440|
|Sessões simultâneas máx.|30,000|
|Opções vCore de pool elástico mínimo/máximo por banco de dados|0-128|
|Número de réplicas|4|
|Multi-AZ|Sim|
|Escalamento Horizontal de Leituras|Sim|
|Armazenamento de backup incluído|tamanho do banco de BD 1X|

\* o valor máximo para tamanhos de e/s variando entre 8 KB e 64 KB. IOPS reais são dependentes da carga de trabalho. Para obter detalhes, consulte [governança de e/s de dados](sql-database-resource-limits-database-server.md#resource-governance).

\*\* para o máximo de trabalhos simultâneos (solicitações) para qualquer banco de dados individual, consulte [limites de recurso de banco de dados único](sql-database-vcore-resource-limits-single-databases.md). Por exemplo, se o pool elástico estiver usando Gen5 e o vCore máximo por banco de dados for definido como 2, o valor máximo de trabalhos simultâneos será de 200.  Se o vCore máximo por banco de dados for definido como 0,5, o valor máximo de trabalhos simultâneos será 50, pois, em Gen5, há um máximo de 100 trabalhos simultâneos por vCore.  Para outras configurações de vCore máxima por banco de dados que são menos de 1 vCore ou menos, o número máximo de trabalhos simultâneos é redimensionado de forma semelhante.

Se todos os vCores de um pool elástico estiverem ocupados, cada banco de dados no pool receberá uma quantidade igual de recursos de computação para processar consultas. O serviço Base de Dados SQL fornece equidade de partilha de recursos entre bases de dados, garantindo frações iguais de tempo de computação. A imparcialidade do compartilhamento de recursos do pool elástico é além de qualquer quantidade de recursos garantidos para cada banco de dados quando o mínimo de vCore por banco de dados é definido como um valor diferente de zero.

## <a name="database-properties-for-pooled-databases"></a>Propriedades do banco de dados em pools

A tabela a seguir descreve as propriedades de bancos de dados em pool.

> [!NOTE]
> Os limites de recursos de bancos de dados individuais em pools elásticos geralmente são os mesmos dos bancos de dados únicos fora dos pools que têm o mesmo tamanho de computação. Por exemplo, o máximo de trabalhos simultâneos para um banco de dados GP_Gen4_1 é de 200 trabalhadores. Portanto, o máximo de trabalhos simultâneos para um banco de dados em um pool de GP_Gen4_1 também é de 200 trabalhadores. Observe que o número total de trabalhos simultâneos no pool de GP_Gen4_1 é 210.

| Propriedade | Descrição |
|:--- |:--- |
| Máximo de vCores por banco de dados |O número máximo de vCores que qualquer banco de dados no pool pode usar, se disponível com base na utilização por outros bancos de dados no pool. O máximo de vCores por banco de dados não é uma garantia de recurso para um banco de dados. Esta definição é uma definição global que se aplica a todas as bases de dados no conjunto. Defina Max vCores por banco de dados alto o suficiente para lidar com picos de utilização de banco de dados. Um certo grau de excesso de confirmações é esperado, pois o pool geralmente assume padrões de uso quente e frio para bancos de dados em que todos os bancos de dados não estão simultaneamente realizando pico.|
| Mínimo de vCores por banco de dados |O número mínimo de vCores que qualquer banco de dados no pool é garantido. Esta definição é uma definição global que se aplica a todas as bases de dados no conjunto. O mínimo de vCores por banco de dados pode ser definido como 0 e também é o valor padrão. Essa propriedade é definida como qualquer lugar entre 0 e a utilização média de vCores por banco de dados. O produto do número de bancos de dados no pool e a quantidade mínima de vCores por Database não podem exceder o vCores por pool.|
| Armazenamento máximo por banco de dados |O tamanho máximo do banco de dados definido pelo usuário para um banco de dados em um pool. Os bancos de dados em pool compartilham o armazenamento de pool alocado, de modo que o tamanho que um Database pode alcançar é limitado ao menor tamanho de armazenamento e do banco de dados do pool restante. O tamanho máximo do banco de dados refere-se ao tamanho máximo dos arquivos e não inclui o espaço usado pelos arquivos de log. |
|||

## <a name="next-steps"></a>Passos seguintes

- Para limites de recursos vCore para um único banco de dados, confira [limites de recursos para bancos de dados individuais usando o modelo de compra vCore](sql-database-vcore-resource-limits-single-databases.md)
- Para os limites de recursos de DTU para um único banco de dados, consulte [limites de recursos para bancos de dados individuais usando o modelo de compra de DTU](sql-database-dtu-resource-limits-single-databases.md)
- Para os limites de recursos de DTU para pools elásticos, consulte [limites de recursos para pools elásticos usando o modelo de compra de DTU](sql-database-dtu-resource-limits-elastic-pools.md)
- Para os limites de recurso para instâncias gerenciadas, consulte [limites de recursos de instância gerenciada](sql-database-managed-instance-resource-limits.md).
- Para obter informações sobre limites gerais do Azure, consulte [assinatura do Azure e limites de serviço, cotas e restrições](../azure-resource-manager/management/azure-subscription-service-limits.md).
- Para obter informações sobre limites de recursos em um servidor de banco de dados, consulte [visão geral dos limites de recursos em um servidor de banco de dados SQL](sql-database-resource-limits-database-server.md) para obter informações sobre os limites nos níveis de servidor e assinatura.
