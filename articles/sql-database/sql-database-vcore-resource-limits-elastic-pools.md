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
ms.date: 11/25/2019
ms.openlocfilehash: 74cc13386befa5cd97900b6b36d07d3144d9b727
ms.sourcegitcommit: 85e7fccf814269c9816b540e4539645ddc153e6e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/26/2019
ms.locfileid: "74534210"
---
# <a name="resource-limits-for-elastic-pools-using-the-vcore-purchasing-model"></a>Limites de recursos para pools elásticos usando o modelo de compra vCore

Este artigo fornece os limites de recursos detalhados para os pools elásticos do banco de dados SQL do Azure e bancos de dados em pool usando o modelo de compra vCore.

Para limites de modelo de compra de DTU, consulte [limites de recursos de DTU do banco de dados SQL – pools elásticos](sql-database-dtu-resource-limits-elastic-pools.md).

> [!IMPORTANT]
> Em algumas circunstâncias, talvez seja necessário reduzir um banco de dados para recuperar espaço não utilizado. Para obter mais informações, consulte [gerenciar o espaço de arquivo no banco de dados SQL do Azure](sql-database-file-space-management.md).

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
|Tamanho do TempDB (GB)|32|64|96|128|160|192|
|Tipo de armazenamento|Armazenamento Premium (remoto)|Armazenamento Premium (remoto)|Armazenamento Premium (remoto)|Armazenamento Premium (remoto)|Armazenamento Premium (remoto)|Armazenamento Premium (remoto)|
|Latência de e/s (aproximada)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|
|IOPS de destino (64 KB)|500|1000|1500|2000|2500|3000|
|Limites de taxa de log (MBps)|4,6875|9,375|14, 625|18,75|23,4375|28,125|
|Máximo de trabalhos simultâneos por pool (solicitações) * |210|420|630|840|1050|1260|
|Máximo de logons simultâneos por pool * |210|420|630|840|1050|1260|
|Máximo de sessões permitidas|30000|30000|30000|30000|30000|30000|
|Opções vCore de pool elástico mínimo/máximo por banco de dados|0, 0,25, 0,5, 1|0, 0,25, 0,5, 1, 2|0, 0,25, 0,5, 1... 3|0, 0,25, 0,5, 1... 4|0, 0,25, 0,5, 1... 5|0, 0,25, 0,5, 1... 6|
|Número de réplicas|1|1|1|1|1|1|
|Multi-AZ|N/A|N/A|N/A|N/A|N/A|N/A|
|Escalamento Horizontal de Leituras|N/A|N/A|N/A|N/A|N/A|N/A|
|Armazenamento de backup incluído|tamanho do banco de BD 1X|tamanho do banco de BD 1X|tamanho do banco de BD 1X|tamanho do banco de BD 1X|tamanho do banco de BD 1X|tamanho do banco de BD 1X|

\* para o máximo de trabalhos simultâneos (solicitações) para qualquer banco de dados individual, consulte [limites de recurso de banco de dados único](sql-database-vcore-resource-limits-single-databases.md). Por exemplo, se o pool elástico estiver usando Gen5 e seu vCore máximo por banco de dados for 2, o máximo de trabalhos simultâneos será de 200.  Se o vCore máximo por banco de dados for 0,5, o máximo de trabalhos simultâneos será 50, pois, em Gen5, há um máximo de 100 trabalhos simultâneos por vCore.  Para outras configurações de vCore máxima por banco de dados que são menos de 1 vCore ou menos, o número máximo de trabalhos simultâneos é redimensionado de forma semelhante.

### <a name="general-purpose-service-tier-generation-4-compute-platform-part-2"></a>Camada de serviço de uso geral: plataforma de computação de geração 4 (parte 2)

|Tamanho da computação|GP_Gen4_7|GP_Gen4_8|GP_Gen4_9|GP_Gen4_10|GP_Gen4_16|GP_Gen4_24|
|:--- | --: |--: |--: |--: |--: |--: |
|Geração de computação|Gen4|Gen4|Gen4|Gen4|Gen4|Gen4|
|vCores|7|8|9|10|16|24|
|Memória (GB)|49|56|63|70|112|168|
|Número de DBs máximo por conjunto|500|500|500|500|500|500|
|Suporte a Columnstore|Sim|Sim|Sim|Sim|Sim|Sim|
|Armazenamento OLTP na memória (GB)|N/A|N/A|N/A|N/A|N/A|N/A|
|Tamanho máximo de dados (GB)|2048|2048|2048|2048|3584|4096|
|Tamanho máximo do log (GB)|614|614|614|614|1075|1229|
|Tamanho do TempDB (GB)|224|256|288|320|384|384|
|Tipo de armazenamento|Armazenamento Premium (remoto)|Armazenamento Premium (remoto)|Armazenamento Premium (remoto)|Armazenamento Premium (remoto)|Armazenamento Premium (remoto)|Armazenamento Premium (remoto)|
|Latência de e/s (aproximada)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|
|IOPS de destino (64 KB)|3500|4000|4500|5000|7000|7000|
|Limites de taxa de log (MBps)|32,8125|37,5|37,5|37,5|37,5|37,5|
|Máximo de trabalhos simultâneos por pool (solicitações) *|1470|1680|1890|2100|3360|5040|
|Pool de logons simultâneos máximos (solicitações) *|1470|1680|1890|2100|3360|5040|
|Máximo de sessões permitidas|30000|30000|30000|30000|30000|30000|
|Opções vCore de pool elástico mínimo/máximo por banco de dados|0, 0,25, 0,5, 1... 7|0, 0,25, 0,5, 1... 8|0, 0,25, 0,5, 1... 9|0, 0,25, 0,5, 1... 10|0, 0,25, 0,5, 1... 10, 16|0, 0,25, 0,5, 1... 10, 16, 24|
|Número de réplicas|1|1|1|1|1|1|
|Multi-AZ|N/A|N/A|N/A|N/A|N/A|N/A|
|Escalamento Horizontal de Leituras|N/A|N/A|N/A|N/A|N/A|N/A|
|Armazenamento de backup incluído|tamanho do banco de BD 1X|tamanho do banco de BD 1X|tamanho do banco de BD 1X|tamanho do banco de BD 1X|tamanho do banco de BD 1X|tamanho do banco de BD 1X|

\* para o máximo de trabalhos simultâneos (solicitações) para qualquer banco de dados individual, consulte [limites de recurso de banco de dados único](sql-database-vcore-resource-limits-single-databases.md). Por exemplo, se o pool elástico estiver usando Gen5 e seu vCore máximo por banco de dados for 2, o máximo de trabalhos simultâneos será de 200.  Se o vCore máximo por banco de dados for 0,5, o máximo de trabalhos simultâneos será 50, pois, em Gen5, há um máximo de 100 trabalhos simultâneos por vCore.  Para outras configurações de vCore máxima por banco de dados que são menos de 1 vCore ou menos, o número máximo de trabalhos simultâneos é redimensionado de forma semelhante.

## <a name="general-purpose---provisioned-compute---gen5"></a>Computação provisionada de uso geral-Gen5

### <a name="general-purpose-service-tier-generation-5-compute-platform-part-1"></a>Camada de serviço de uso geral: plataforma de computação de geração 5 (parte 1)

|Tamanho da computação|GP_Gen5_2|GP_Gen5_4|GP_Gen5_6|GP_Gen5_8|GP_Gen5_10|GP_Gen5_12|GP_Gen5_14|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|Geração de computação|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|
|vCores|2|4|6|8|10|12|14|
|Memória (GB)|10.2|20,4|30,6|40,8|51|61,2|71,4|
|Número de DBs máximo por conjunto|100|200|500|500|500|500|500|
|Suporte a Columnstore|Sim|Sim|Sim|Sim|Sim|Sim|Sim|
|Armazenamento OLTP na memória (GB)|N/A|N/A|N/A|N/A|N/A|N/A|N/A|
|Tamanho máximo de dados (GB)|512|756|1536|1536|1536|2048|2048|
|Tamanho máximo do log (GB)|154|227|461|461|461|614|614|
|Tamanho do TempDB (GB)|64|128|192|256|320|384|384|
|Tipo de armazenamento|Armazenamento Premium (remoto)|Armazenamento Premium (remoto)|Armazenamento Premium (remoto)|Armazenamento Premium (remoto)|Armazenamento Premium (remoto)|Armazenamento Premium (remoto)|Armazenamento Premium (remoto)|
|Latência de e/s (aproximada)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|
|IOPS de destino (64 KB)|1000|2000|3000|4000|5000|6000|7000|
|Limites de taxa de log (MBps)|4,6875|9,375|14, 625|18,75|23,4375|28,125|32,8125|
|Máximo de trabalhos simultâneos por pool (solicitações) *|210|420|630|840|1050|1260|1470|
|Máximo de logons simultâneos por pool (solicitações) *|210|420|630|840|1050|1260|1470|
|Máximo de sessões permitidas|30000|30000|30000|30000|30000|30000|30000|
|Opções vCore de pool elástico mínimo/máximo por banco de dados|0, 0,25, 0,5, 1, 2|0, 0,25, 0,5, 1... 4|0, 0,25, 0,5, 1... 6|0, 0,25, 0,5, 1... 8|0, 0,25, 0,5, 1... 10|0, 0,25, 0,5, 1... 12|0, 0,25, 0,5, 1... 14|
|Número de réplicas|1|1|1|1|1|1|1|
|Multi-AZ|N/A|N/A|N/A|N/A|N/A|N/A|N/A|
|Escalamento Horizontal de Leituras|N/A|N/A|N/A|N/A|N/A|N/A|N/A|
|Armazenamento de backup incluído|tamanho do banco de BD 1X|tamanho do banco de BD 1X|tamanho do banco de BD 1X|tamanho do banco de BD 1X|tamanho do banco de BD 1X|tamanho do banco de BD 1X|tamanho do banco de BD 1X|

\* para o máximo de trabalhos simultâneos (solicitações) para qualquer banco de dados individual, consulte [limites de recurso de banco de dados único](sql-database-vcore-resource-limits-single-databases.md). Por exemplo, se o pool elástico estiver usando Gen5 e seu vCore máximo por banco de dados for 2, o máximo de trabalhos simultâneos será de 200.  Se o vCore máximo por banco de dados for 0,5, o máximo de trabalhos simultâneos será 50, pois, em Gen5, há um máximo de 100 trabalhos simultâneos por vCore.  Para outras configurações de vCore máxima por banco de dados que são menos de 1 vCore ou menos, o número máximo de trabalhos simultâneos é redimensionado de forma semelhante.

### <a name="general-purpose-service-tier-generation-5-compute-platform-part-2"></a>Camada de serviço de uso geral: plataforma de computação de geração 5 (parte 2)

|Tamanho da computação|GP_Gen5_16|GP_Gen5_18|GP_Gen5_20|GP_Gen5_24|GP_Gen5_32|GP_Gen5_40|GP_Gen5_80|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|Geração de computação|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|
|vCores|16|18|20|24|32|40|80|
|Memória (GB)|81,6|91,8|102|122,4|163,2|204|408|
|Número de DBs máximo por conjunto|500|500|500|500|500|500|500|
|Suporte a Columnstore|Sim|Sim|Sim|Sim|Sim|Sim|Sim|
|Armazenamento OLTP na memória (GB)|N/A|N/A|N/A|N/A|N/A|N/A|N/A|
|Tamanho máximo de dados (GB)|2048|3072|3072|3072|4096|4096|4096|
|Tamanho máximo do log (GB)|614|922|922|922|1229|1229|1229|
|Tamanho do TempDB (GB)|384|384|384|384|384|384|384|
|Tipo de armazenamento|Armazenamento Premium (remoto)|Armazenamento Premium (remoto)|Armazenamento Premium (remoto)|Armazenamento Premium (remoto)|Armazenamento Premium (remoto)|Armazenamento Premium (remoto)|Armazenamento Premium (remoto)|
|Latência de e/s (aproximada)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|
|IOPS de destino (64 KB)|7000|7000|7000|7000|7000|7000|7000|
|Limites de taxa de log (MBps)|37,5|37,5|37,5|37,5|37,5|37,5|37,5|
|Máximo de trabalhos simultâneos por pool (solicitações) *|1680|1890|2100|2520|3360|4200|8400|
|Máximo de logons simultâneos por pool (solicitações) *|1680|1890|2100|2520|3360|4200|8400|
|Opções vCore de pool elástico mínimo/máximo por banco de dados|0, 0,25, 0,5, 1... 16|0, 0,25, 0,5, 1... 18|0, 0,25, 0,5, 1... 20|0, 0,25, 0,5, 1... 20, 24|0, 0,25, 0,5, 1... 20, 24, 32|0, 0,25, 0,5, 1... 16, 24, 32, 40|0, 0,25, 0,5, 1... 16, 24, 32, 40, 80|
|Número de réplicas|1|1|1|1|1|1|1|
|Multi-AZ|N/A|N/A|N/A|N/A|N/A|N/A|N/A|
|Escalamento Horizontal de Leituras|N/A|N/A|N/A|N/A|N/A|N/A|N/A|
|Armazenamento de backup incluído|tamanho do banco de BD 1X|tamanho do banco de BD 1X|tamanho do banco de BD 1X|tamanho do banco de BD 1X|tamanho do banco de BD 1X|tamanho do banco de BD 1X|tamanho do banco de BD 1X|

## <a name="general-purpose---provisioned-compute---fsv2-series"></a>Computação provisionada de uso geral-série Fsv2

### <a name="fsv2-series-compute-generation-preview"></a>Geração de computação da série Fsv2 (visualização)

|Tamanho da computação|GP_Fsv2_72|
|:--- | --: |
|Geração de computação|Série Fsv2|
|vCores|72|
|Memória (GB)|136|
|Número de DBs máximo por conjunto|500|
|Suporte a Columnstore|Sim|
|Armazenamento OLTP na memória (GB)|N/A|
|Tamanho máximo de dados (GB)|4096|
|Tamanho máximo do log (GB)|1024|
|Tamanho máximo de dados de TempDB (GB)|333|
|Tipo de armazenamento|Armazenamento Premium (remoto)|
|Latência de e/s (aproximada)|5-7 ms (gravação)<br>5-10 ms (leitura)|
|IOPS de destino (64 KB)|36000|
|Limites de taxa de log (MBps)|37,5|
|Máximo de trabalhos simultâneos por pool (solicitações) *|1680|
|Máximo de logons simultâneos por pool (solicitações) *|1680|
|Opções vCore de pool elástico mínimo/máximo por banco de dados|0-72|
|Número de réplicas|1|
|Multi-AZ|N/A|
|Escalamento Horizontal de Leituras|N/A|
|Armazenamento de backup incluído|tamanho do banco de BD 1X|

\* para o máximo de trabalhos simultâneos (solicitações) para qualquer banco de dados individual, consulte [limites de recurso de banco de dados único](sql-database-vcore-resource-limits-single-databases.md). Por exemplo, se o pool elástico estiver usando Gen5 e seu vCore máximo por banco de dados for 2, o máximo de trabalhos simultâneos será de 200.  Se o vCore máximo por banco de dados for 0,5, o máximo de trabalhos simultâneos será 50, pois, em Gen5, há um máximo de 100 trabalhos simultâneos por vCore.  Para outras configurações de vCore máxima por banco de dados que são menos de 1 vCore ou menos, o número máximo de trabalhos simultâneos é redimensionado de forma semelhante.

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
|Tamanho do TempDB (GB)|64|96|128|160|192|
|Latência de e/s (aproximada)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|
|IOPS de destino (64 KB)|10000|15 000|20000|25000|30000|
|Limites de taxa de log (MBps)|20|30|40|50|60|
|Máximo de trabalhos simultâneos por pool (solicitações) *|420|630|840|1050|1260|
|Máximo de logons simultâneos por pool (solicitações) *|420|630|840|1050|1260|
|Máximo de sessões permitidas|30000|30000|30000|30000|30000|
|Opções vCore de pool elástico mínimo/máximo por banco de dados|0, 0,25, 0,5, 1, 2|0, 0,25, 0,5, 1... 3|0, 0,25, 0,5, 1... 4|0, 0,25, 0,5, 1... 5|0, 0,25, 0,5, 1... 6|
|Número de réplicas|4|4|4|4|4|
|Multi-AZ|Sim|Sim|Sim|Sim|Sim|
|Escalamento Horizontal de Leituras|Sim|Sim|Sim|Sim|Sim|
|Armazenamento de backup incluído|tamanho do banco de BD 1X|tamanho do banco de BD 1X|tamanho do banco de BD 1X|tamanho do banco de BD 1X|tamanho do banco de BD 1X|

\* para o máximo de trabalhos simultâneos (solicitações) para qualquer banco de dados individual, consulte [limites de recurso de banco de dados único](sql-database-vcore-resource-limits-single-databases.md). Por exemplo, se o pool elástico estiver usando Gen5 e seu vCore máximo por banco de dados for 2, o máximo de trabalhos simultâneos será de 200.  Se o vCore máximo por banco de dados for 0,5, o máximo de trabalhos simultâneos será 50, pois, em Gen5, há um máximo de 100 trabalhos simultâneos por vCore.  Para outras configurações de vCore máxima por banco de dados que são menos de 1 vCore ou menos, o número máximo de trabalhos simultâneos é redimensionado de forma semelhante.

### <a name="business-critical-service-tier-generation-4-compute-platform-part-2"></a>Camada de serviço comercialmente crítica: plataforma de computação de geração 4 (parte 2)

|Tamanho da computação|BC_Gen4_7|BC_Gen4_8|BC_Gen4_9|BC_Gen4_10|BC_Gen4_16|BC_Gen4_24|
|:--- | --: |--: |--: |--: |--: |--: |
|Geração de computação|Gen4|Gen4|Gen4|Gen4|Gen4|Gen4|
|vCores|7|8|9|10|16|24|
|Memória (GB)|81,6|91,8|102|122,4|163,2|204|
|Número de DBs máximo por conjunto|100|100|100|100|100|100|
|Suporte a Columnstore|N/A|N/A|N/A|N/A|N/A|N/A|
|Armazenamento OLTP na memória (GB)|7|8|9,5|11|20|36|
|Tipo de armazenamento|SSD local|SSD local|SSD local|SSD local|SSD local|SSD local|
|Tamanho máximo de dados (GB)|1024|1024|1024|1024|1024|1024|
|Tamanho máximo do log (GB)|307|307|307|307|307|307|
|Tamanho do TempDB (GB)|224|256|288|320|384|384|
|Latência de e/s (aproximada)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|
|IOPS de destino (64 KB)|35000|40000|45000|50000|80000|120000|
|Limites de taxa de log (MBps)|70|80|80|80|80|80|
|Máximo de trabalhos simultâneos por pool (solicitações) *|1470|1680|1890|2100|3360|5040|
|Máximo de logons simultâneos por pool (solicitações) *|1470|1680|1890|2100|3360|5040|
|Máximo de sessões permitidas|30000|30000|30000|30000|30000|30000|
|Opções vCore de pool elástico mínimo/máximo por banco de dados|0, 0,25, 0,5, 1... 7|0, 0,25, 0,5, 1... 8|0, 0,25, 0,5, 1... 9|0, 0,25, 0,5, 1... 10|0, 0,25, 0,5, 1... 10, 16|0, 0,25, 0,5, 1... 10, 16, 24|
|Número de réplicas|4|4|4|4|4|4|
|Multi-AZ|Sim|Sim|Sim|Sim|Sim|Sim|
|Escalamento Horizontal de Leituras|Sim|Sim|Sim|Sim|Sim|Sim|
|Armazenamento de backup incluído|tamanho do banco de BD 1X|tamanho do banco de BD 1X|tamanho do banco de BD 1X|tamanho do banco de BD 1X|tamanho do banco de BD 1X|tamanho do banco de BD 1X|

\* para o máximo de trabalhos simultâneos (solicitações) para qualquer banco de dados individual, consulte [limites de recurso de banco de dados único](sql-database-vcore-resource-limits-single-databases.md). Por exemplo, se o pool elástico estiver usando Gen5 e seu vCore máximo por banco de dados for 2, o máximo de trabalhos simultâneos será de 200.  Se o vCore máximo por banco de dados for 0,5, o máximo de trabalhos simultâneos será 50, pois, em Gen5, há um máximo de 100 trabalhos simultâneos por vCore.  Para outras configurações de vCore máxima por banco de dados que são menos de 1 vCore ou menos, o número máximo de trabalhos simultâneos é redimensionado de forma semelhante.

## <a name="business-critical---provisioned-compute---gen5"></a>Comercialmente crítico-computação provisionada-Gen5

### <a name="business-critical-service-tier-generation-5-compute-platform-part-1"></a>Camada de serviço comercialmente crítica: plataforma de computação de geração 5 (parte 1)

|Tamanho da computação|BC_Gen5_4|BC_Gen5_6|BC_Gen5_8|BC_Gen5_10|BC_Gen5_12|BC_Gen5_14|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|Geração de computação|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|
|vCores|4|6|8|10|12|14|
|Memória (GB)|20,4|30,6|40,8|51|61,2|71,4|
|Número de DBs máximo por conjunto|100|100|100|100|100|100|
|Suporte a Columnstore|Sim|Sim|Sim|Sim|Sim|Sim|
|Armazenamento OLTP na memória (GB)|3,142|4,713|6,284|8,655|11, 26|13,397|
|Tamanho máximo de dados (GB)|1024|1536|1536|1536|3072|3072|
|Tamanho máximo do log (GB)|307|307|461|461|922|922|
|Tamanho do TempDB (GB)|128|192|256|320|384|384|
|Tipo de armazenamento|SSD local|SSD local|SSD local|SSD local|SSD local|SSD local|
|Latência de e/s (aproximada)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|
|IOPS de destino (64 KB)|10000|15 000|20000|25000|30000|35000|
|Limites de taxa de log (MBps)|30|45|60|75|90|105|
|Máximo de trabalhos simultâneos por pool (solicitações) *|420|630|840|1050|1260|1470|
|Máximo de logons simultâneos por pool (solicitações) *|420|630|840|1050|1260|1470|
|Máximo de sessões permitidas|30000|30000|30000|30000|30000|30000|
|Opções vCore de pool elástico mínimo/máximo por banco de dados|0, 0,25, 0,5, 1... 4|0, 0,25, 0,5, 1... 6|0, 0,25, 0,5, 1... 8|0, 0,25, 0,5, 1... 10|0, 0,25, 0,5, 1... 12|0, 0,25, 0,5, 1... 14|
|Número de réplicas|4|4|4|4|4|4|
|Multi-AZ|Sim|Sim|Sim|Sim|Sim|Sim|
|Escalamento Horizontal de Leituras|Sim|Sim|Sim|Sim|Sim|Sim|
|Armazenamento de backup incluído|tamanho do banco de BD 1X|tamanho do banco de BD 1X|tamanho do banco de BD 1X|tamanho do banco de BD 1X|tamanho do banco de BD 1X|tamanho do banco de BD 1X|

\* para o máximo de trabalhos simultâneos (solicitações) para qualquer banco de dados individual, consulte [limites de recurso de banco de dados único](sql-database-vcore-resource-limits-single-databases.md). Por exemplo, se o pool elástico estiver usando Gen5 e seu vCore máximo por banco de dados for 2, o máximo de trabalhos simultâneos será de 200.  Se o vCore máximo por banco de dados for 0,5, o máximo de trabalhos simultâneos será 50, pois, em Gen5, há um máximo de 100 trabalhos simultâneos por vCore.  Para outras configurações de vCore máxima por banco de dados que são menos de 1 vCore ou menos, o número máximo de trabalhos simultâneos é redimensionado de forma semelhante.

### <a name="business-critical-service-tier-generation-5-compute-platform-part-2"></a>Camada de serviço comercialmente crítica: plataforma de computação de geração 5 (parte 2)

|Tamanho da computação|BC_Gen5_16|BC_Gen5_18|BC_Gen5_20|BC_Gen5_24|BC_Gen5_32|BC_Gen5_40|BC_Gen5_80|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|Geração de computação|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|
|vCores|16|18|20|24|32|40|80|
|Memória (GB)|81,6|91,8|102|122,4|163,2|204|408|
|Número de DBs máximo por conjunto|100|100|100|100|100|100|100|
|Suporte a Columnstore|Sim|Sim|Sim|Sim|Sim|Sim|Sim|
|Armazenamento OLTP na memória (GB)|15,768|18,139|20,51|25,252|37,936|52,22|131,64|
|Tamanho máximo de dados (GB)|3072|3072|3072|4096|4096|4096|4096|
|Tamanho máximo do log (GB)|922|922|922|1229|1229|1229|1229|
|Tamanho do TempDB (GB)|384|384|384|384|384|384|384|
|Tipo de armazenamento|SSD local|SSD local|SSD local|SSD local|SSD local|SSD local|SSD local|
|Latência de e/s (aproximada)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|
|IOPS de destino (64 KB)|40000|45000|50000|60000|80000|100000|200000|
|Limites de taxa de log (MBps)|120|120|120|120|120|120|120|
|Máximo de trabalhos simultâneos por pool (solicitações) *|1680|1890|2100|2520|3360|4200|8400|
|Máximo de logons simultâneos por pool (solicitações) *|1680|1890|2100|2520|3360|4200|8400|
|Máximo de sessões permitidas|30000|30000|30000|30000|30000|30000|30000|
|Opções vCore de pool elástico mínimo/máximo por banco de dados|0, 0,25, 0,5, 1... 16|0, 0,25, 0,5, 1... 18|0, 0,25, 0,5, 1... 20|0, 0,25, 0,5, 1... 20, 24|0, 0,25, 0,5, 1... 20, 24, 32|0, 0,25, 0,5, 1... 20, 24, 32, 40|0, 0,25, 0,5, 1... 20, 24, 32, 40, 80|
|Número de réplicas|4|4|4|4|4|4|4|
|Multi-AZ|Sim|Sim|Sim|Sim|Sim|Sim|Sim|
|Escalamento Horizontal de Leituras|Sim|Sim|Sim|Sim|Sim|Sim|Sim|
|Armazenamento de backup incluído|tamanho do banco de BD 1X|tamanho do banco de BD 1X|tamanho do banco de BD 1X|tamanho do banco de BD 1X|tamanho do banco de BD 1X|tamanho do banco de BD 1X|tamanho do banco de BD 1X|

## <a name="business-critical---provisioned-compute---m-series"></a>Computação comercialmente crítica-série M

### <a name="m-series-compute-generation-preview"></a>Geração de computação da série M (versão prévia)

|Tamanho da computação|GP_M_128|
|:--- | --: |
|Geração de computação|Série M|
|vCores|128|
|Memória (GB)|3767|
|Número de DBs máximo por conjunto|100|
|Suporte a Columnstore|Sim|
|Armazenamento OLTP na memória (GB)|481|
|Tamanho máximo de dados (GB)|4096|
|Tamanho máximo do log (GB)|2048|
|Tamanho máximo de dados de TempDB (GB)|4096|
|Tipo de armazenamento|SSD local|
|Latência de e/s (aproximada)|1-2 ms (gravação)<br>1-2 ms (leitura)|
|IOPS de destino (64 KB)|40000|
|Limites de taxa de log (MBps)|120|
|Máximo de trabalhos simultâneos por pool (solicitações) *|1680|
|Máximo de logons simultâneos por pool (solicitações) *|1680|
|Máximo de sessões permitidas|30000|
|Opções vCore de pool elástico mínimo/máximo por banco de dados|0-128|
|Número de réplicas|4|
|Multi-AZ|Sim|
|Escalamento Horizontal de Leituras|Sim|
|Armazenamento de backup incluído|tamanho do banco de BD 1X|



\* para o máximo de trabalhos simultâneos (solicitações) para qualquer banco de dados individual, consulte [limites de recurso de banco de dados único](sql-database-vcore-resource-limits-single-databases.md). Por exemplo, se o pool elástico estiver usando Gen5 e seu vCore máximo por banco de dados for 2, o máximo de trabalhos simultâneos será de 200.  Se o vCore máximo por banco de dados for 0,5, o máximo de trabalhos simultâneos será 50, pois, em Gen5, há um máximo de 100 trabalhos simultâneos por vCore.  Para outras configurações de vCore máxima por banco de dados que são menos de 1 vCore ou menos, o número máximo de trabalhos simultâneos é redimensionado de forma semelhante.

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
- Para obter informações sobre limites gerais do Azure, consulte [assinatura do Azure e limites de serviço, cotas e restrições](../azure-subscription-service-limits.md).
- Para obter informações sobre limites de recursos em um servidor de banco de dados, consulte [visão geral dos limites de recursos em um servidor de banco de dados SQL](sql-database-resource-limits-database-server.md) para obter informações sobre os limites nos níveis de servidor e assinatura.
