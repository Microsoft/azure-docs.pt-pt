---
title: Limites de recursos baseados em vcore da base de dados SQL do Azure - conjuntos elásticos | Documentos da Microsoft
description: Esta página descreve alguns limites de recursos baseados em vCore comuns para conjuntos elásticos na base de dados do Azure SQL.
services: sql-database
ms.service: sql-database
ms.subservice: elastic-pools
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: oslake
ms.author: moslake
ms.reviewer: carlrab
manager: craigg
ms.date: 03/06/2019
ms.openlocfilehash: a1e76aafa271ff021517c3d06c3c6e02103413d3
ms.sourcegitcommit: dd1a9f38c69954f15ff5c166e456fda37ae1cdf2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57571478"
---
# <a name="resource-limits-for-elastic-pools-using-the-vcore-based-purchasing-model-limits"></a>Limites de recursos para conjuntos elásticos com os limites de compra modelo baseado em vCore

Este artigo fornece os limites de recursos detalhados para conjuntos elásticos da base de dados do Azure SQL e bases de dados agrupadas com o modelo de compra baseado em vCore.

Para baseado em DTU limites de modelo compra, veja [limites de recursos baseados em DTU de base de dados do SQL - conjuntos elásticos](sql-database-dtu-resource-limits-elastic-pools.md).

> [!IMPORTANT]
> Em algumas circunstâncias, poderá ter reduzir uma base de dados para recuperar espaço não utilizado. Para obter mais informações, consulte [gerir o espaço de ficheiro na base de dados do Azure SQL](sql-database-file-space-management.md).

Pode definir o escalão de serviço, o tamanho de computação e quantidade de armazenamento utilizando o [portal do Azure](sql-database-elastic-pool-manage.md#azure-portal-manage-elastic-pools-and-pooled-databases), [PowerShell](sql-database-elastic-pool-manage.md#powershell-manage-elastic-pools-and-pooled-databases), o [da CLI do Azure](sql-database-elastic-pool-manage.md#azure-cli-manage-elastic-pools-and-pooled-databases), ou o [API de REST](sql-database-elastic-pool-manage.md#rest-api-manage-elastic-pools-and-pooled-databases).

> [!IMPORTANT]
> Para dimensionar a orientação e considerações, consulte [dimensionar um conjunto elástico](sql-database-elastic-pool-scale.md)
> [!NOTE]
> Os limites de recursos de bases de dados individuais em conjuntos elásticos são geralmente as mesmas usadas para bases de dados individuais fora agrupamentos que tem o mesmo tamanho de computação. Por exemplo, os máximos de trabalhadores simultâneos para uma base de dados GP_Gen4_1 é de 200 funções de trabalho. Então, os máximos de trabalhadores simultâneos para uma base de dados num conjunto GP_Gen4_1 também é de 200 funções de trabalho. Tenha em atenção de que o número total de trabalhadores simultâneos no agrupamento de GP_Gen4_1 é 210.

## <a name="general-purpose-service-tier-storage-sizes-and-compute-sizes"></a>Escalão de serviço de fins geral: Tamanhos de armazenamento e tamanhos de computação

### <a name="general-purpose-service-tier-generation-4-compute-platform-part-1"></a>Escalão de serviço de fins geral: Plataforma de computação de geração 4 (parte 1)

|Tamanho de computação|GP_Gen4_1|GP_Gen4_2|GP_Gen4_3|GP_Gen4_4|GP_Gen4_5|GP_Gen4_6
|:--- | --: |--: |--: |--: |--: |--: |
|Geração de H/W|4|4|4|4|4|4|
|vCores|1|2|3|4|5|6|
|Memória (GB)|7|14|21|28|35|42|
|Suporte de Columnstore|Sim|Sim|Sim|Sim|Sim|Sim|
|Armazenamento em OLTP na memória (GB)|N/A|N/D|N/D|N/D|N/D|N/A|
|Tamanho máximo de dados (GB)|512|756|756|1536|1536|1536|
|Tamanho máximo do registo|154|227|227|461|461|461|
|TempDB tamanho (GB)|32|64|96|128|160|192|
|Tipo de armazenamento|Armazenamento Premium (remoto)|Armazenamento Premium (remoto)|Armazenamento Premium (remoto)|Armazenamento Premium (remoto)|Armazenamento Premium (remoto)|Armazenamento Premium (remoto)|
|Latência de e/s (aproximada)|ms de 5 a 7 (escrita)<br>ms de 5 a 10 (ler)|ms de 5 a 7 (escrita)<br>ms de 5 a 10 (ler)|ms de 5 a 7 (escrita)<br>ms de 5 a 10 (ler)|ms de 5 a 7 (escrita)<br>ms de 5 a 10 (ler)|ms de 5 a 7 (escrita)<br>ms de 5 a 10 (ler)|ms de 5 a 7 (escrita)<br>ms de 5 a 10 (ler)|
|Destino IOPS (64 KB)|500|1000|1500|2000|2500|3000|
|Limites de velocidade de registo (MBps)|2.5|5|7.5|10|12.5|15|
|Máximo de trabalhadores simultâneos por conjunto (pedidos) * |210|420|630|840|1050|1260|
|Inícios de sessão simultâneos máx. por conjunto * |210|420|630|840|1050|1260|
|Máximo permitido de sessões|30000|30000|30000|30000|30000|30000|
|Número de DBs máximo por conjunto|100|200|300|500|500|500|
|Opções de vCore de conjuntos elásticos Mín/Máx por base de dados|0, 0.25, 0.5, 1|0, 0.25, 0.5, 1, 2|0, 0.25, 0.5, 1...3|0, 0.25, 0.5, 1...4|0, 0.25, 0.5, 1...5|0, 0.25, 0.5, 1...6|
|Número de réplicas|1|1|1|1|1|1|
|Multi-AZ|N/A|N/D|N/D|N/D|N/D|N/A|
|Leia o Escalamento horizontal|N/A|N/D|N/D|N/D|N/D|N/A|
|Armazenamento de cópia de segurança incluído|Tamanho de BD de X 1|Tamanho de BD de X 1|Tamanho de BD de X 1|Tamanho de BD de X 1|Tamanho de BD de X 1|Tamanho de BD de X 1|

\* Para os máximos de trabalhadores simultâneos (pedidos) para qualquer base de dados individual, consulte [único limites de recursos da base de dados](sql-database-vcore-resource-limits-single-databases.md). Por exemplo, se estiver a utilizar o conjunto elástico Gen5 e seu vCore máximo por base de dados é 2, em seguida, os máximos de trabalhadores simultâneos é 200.  Se vCore máximo por base de dados for 0,5, em seguida, os máximos de trabalhadores simultâneos é 50, pois no Gen5 há um máximo de 100 trabalhadores simultâneos por vcore.  Para outras configurações de máximo de vCore por base de dados que são menos 1 vCore ou menos, o número de trabalhadores simultâneos máx. é da mesma forma redimensionadas.

### <a name="general-purpose-service-tier-generation-4-compute-platform-part-2"></a>Escalão de serviço de fins geral: Plataforma de computação de geração 4 (parte 2)

|Tamanho de computação|GP_Gen4_7|GP_Gen4_8|GP_Gen4_9|GP_Gen4_10|GP_Gen4_16|GP_Gen4_24|
|:--- | --: |--: |--: |--: |--: |--: |
|Geração de H/W|4|4|4|4|4|4|
|vCores|7|8|9|10|16|24|
|Memória (GB)|49|56|63|70|112|168|
|Suporte de Columnstore|Sim|Sim|Sim|Sim|Sim|Sim|
|Armazenamento em OLTP na memória (GB)|N/A|N/D|N/D|N/D|N/D|N/A|
|Tamanho máximo de dados (GB)|1536|2048|2048|2048|3584|4096|
|Tamanho máximo do registo (GB)|461|614|614|614|1075|1229|
|TempDB tamanho (GB)|224|256|288|320|384|384|
|Tipo de armazenamento|Armazenamento Premium (remoto)|Armazenamento Premium (remoto)|Armazenamento Premium (remoto)|Armazenamento Premium (remoto)|Armazenamento Premium (remoto)|Armazenamento Premium (remoto)|
|Latência de e/s (aproximada)|ms de 5 a 7 (escrita)<br>ms de 5 a 10 (ler)|ms de 5 a 7 (escrita)<br>ms de 5 a 10 (ler)|ms de 5 a 7 (escrita)<br>ms de 5 a 10 (ler)|ms de 5 a 7 (escrita)<br>ms de 5 a 10 (ler)|ms de 5 a 7 (escrita)<br>ms de 5 a 10 (ler)|ms de 5 a 7 (escrita)<br>ms de 5 a 10 (ler)|
|Destino IOPS (64 KB)|3500|4000|4500|5000|7000|7000|
|Limites de velocidade de registo (MBps)|17.5|20|20|20|20|20|
|Máximo de trabalhadores simultâneos por conjunto (pedidos) *|1470|1680|1890|2100|3360|5040|
|Conjunto de inícios de sessão simultâneos máx. (pedidos) *|1470|1680|1890|2100|3360|5040|
|Máximo permitido de sessões|30000|30000|30000|30000|30000|30000|
|Número de DBs máximo por conjunto|200|500|500|500|500|500|
|Opções de vCore de conjuntos elásticos Mín/Máx por base de dados|0, 0.25, 0.5, 1...7|0, 0.25, 0.5, 1...8|0, 0.25, 0.5, 1...9|0, 0.25, 0.5, 1...10|0, 0.25, 0.5, 1...10, 16|0, 0.25, 0.5, 1...10, 16, 24|
|Número de réplicas|1|1|1|1|1|1|
|Multi-AZ|N/A|N/D|N/D|N/D|N/D|N/A|
|Leia o Escalamento horizontal|N/A|N/D|N/D|N/D|N/D|N/A|
|Armazenamento de cópia de segurança incluído|Tamanho de BD de X 1|Tamanho de BD de X 1|Tamanho de BD de X 1|Tamanho de BD de X 1|Tamanho de BD de X 1|Tamanho de BD de X 1|

\* Para os máximos de trabalhadores simultâneos (pedidos) para qualquer base de dados individual, consulte [único limites de recursos da base de dados](sql-database-vcore-resource-limits-single-databases.md). Por exemplo, se estiver a utilizar o conjunto elástico Gen5 e seu vCore máximo por base de dados é 2, em seguida, os máximos de trabalhadores simultâneos é 200.  Se vCore máximo por base de dados for 0,5, em seguida, os máximos de trabalhadores simultâneos é 50, pois no Gen5 há um máximo de 100 trabalhadores simultâneos por vcore.  Para outras configurações de máximo de vCore por base de dados que são menos 1 vCore ou menos, o número de trabalhadores simultâneos máx. é da mesma forma redimensionadas.

### <a name="general-purpose-service-tier-generation-5-compute-platform-part-1"></a>Escalão de serviço de fins geral: Plataforma de computação de geração 5 (parte 1)

|Tamanho de computação|GP_Gen5_2|GP_Gen5_4|GP_Gen5_6|GP_Gen5_8|GP_Gen5_10|GP_Gen5_12|GP_Gen5_14|
|:--- | --: |--: |--: |--: |---: | --: |--: |--: |
|Geração de H/W|5|5|5|5|5|5|5|
|vCores|2|4|6|8|10|12|14|
|Memória (GB)|10.2|20.4|30.6|40.8|51|61.2|71.4|
|Suporte de Columnstore|Sim|Sim|Sim|Sim|Sim|Sim|Sim|
|Armazenamento em OLTP na memória (GB)|N/A|N/D|N/D|N/D|N/D|N/D|N/A|
|Tamanho máximo de dados (GB)|512|756|756|1536|1536|1536|
|Tamanho máximo do registo (GB)|154|227|227|461|461|461|461|
|TempDB tamanho (GB)|64|128|192|256|320|384|384|
|Tipo de armazenamento|Armazenamento Premium (remoto)|Armazenamento Premium (remoto)|Armazenamento Premium (remoto)|Armazenamento Premium (remoto)|Armazenamento Premium (remoto)|Armazenamento Premium (remoto)|Armazenamento Premium (remoto)|
|Latência de e/s (aproximada)|ms de 5 a 7 (escrita)<br>ms de 5 a 10 (ler)|ms de 5 a 7 (escrita)<br>ms de 5 a 10 (ler)|ms de 5 a 7 (escrita)<br>ms de 5 a 10 (ler)|ms de 5 a 7 (escrita)<br>ms de 5 a 10 (ler)|ms de 5 a 7 (escrita)<br>ms de 5 a 10 (ler)|ms de 5 a 7 (escrita)<br>ms de 5 a 10 (ler)|ms de 5 a 7 (escrita)<br>ms de 5 a 10 (ler)|
|Destino IOPS (64 KB)|500|1000|1500|2000|2500|3000|3500|
|Limites de velocidade de registo (MBps)|2.5|56|7.5|10|12.5|15|17.5|
|Máximo de trabalhadores simultâneos por conjunto (pedidos) *|210|420|630|840|1050|1260|1470|
|Inícios de sessão simultâneos máx. por conjunto (pedidos) *|210|420|630|840|1050|1260|1470|
|Máximo permitido de sessões|30000|30000|30000|30000|30000|30000|30000|
|Número de DBs máximo por conjunto|200|500|500|500|500|500|500|
|Opções de vCore de conjuntos elásticos Mín/Máx por base de dados|0, 0.25, 0.5, 1, 2|0, 0.25, 0.5, 1...4|0, 0.25, 0.5, 1...6|0, 0.25, 0.5, 1...8|0, 0.25, 0.5, 1...10|0, 0.25, 0.5, 1...12|0, 0.25, 0.5, 1...14|
|Número de réplicas|1|1|1|1|1|1|1|
|Multi-AZ|N/A|N/D|N/D|N/D|N/D|N/D|N/A|
|Leia o Escalamento horizontal|N/A|N/D|N/D|N/D|N/D|N/D|N/A|
|Armazenamento de cópia de segurança incluído|Tamanho de BD de X 1|Tamanho de BD de X 1|Tamanho de BD de X 1|Tamanho de BD de X 1|Tamanho de BD de X 1|Tamanho de BD de X 1|Tamanho de BD de X 1|

\* Para os máximos de trabalhadores simultâneos (pedidos) para qualquer base de dados individual, consulte [único limites de recursos da base de dados](sql-database-vcore-resource-limits-single-databases.md). Por exemplo, se estiver a utilizar o conjunto elástico Gen5 e seu vCore máximo por base de dados é 2, em seguida, os máximos de trabalhadores simultâneos é 200.  Se vCore máximo por base de dados for 0,5, em seguida, os máximos de trabalhadores simultâneos é 50, pois no Gen5 há um máximo de 100 trabalhadores simultâneos por vcore.  Para outras configurações de máximo de vCore por base de dados que são menos 1 vCore ou menos, o número de trabalhadores simultâneos máx. é da mesma forma redimensionadas.

### <a name="general-purpose-service-tier-generation-5-compute-platform-part-2"></a>Escalão de serviço de fins geral: Plataforma de computação de geração 5 (parte 2)

|Tamanho de computação|GP_Gen5_16|GP_Gen5_18|GP_Gen5_20|GP_Gen5_24|GP_Gen5_32|GP_Gen5_40|GP_Gen5_80|
|:--- | --: |--: |--: |--: |---: | --: |--: |--: |
|Geração de H/W|5|5|5|5|5|5|5|
|vCores|16|18|20|24|32|40|80|
|Memória (GB)|81.6|91.8|102|122.4|163.2|204|408|
|Suporte de Columnstore|Sim|Sim|Sim|Sim|Sim|Sim|Sim|
|Armazenamento em OLTP na memória (GB)|N/A|N/D|N/D|N/D|N/D|N/D|N/A|
|Tamanho máximo de dados (GB)|2048|2048|3072|3072|4096|4096|4096|
|Tamanho máximo do registo (GB)|614|614|922|922|1229|1229|1229|
|TempDB tamanho (GB)|384|384|384|384|384|384|384|
|Tipo de armazenamento|Armazenamento Premium (remoto)|Armazenamento Premium (remoto)|Armazenamento Premium (remoto)|Armazenamento Premium (remoto)|Armazenamento Premium (remoto)|Armazenamento Premium (remoto)|Armazenamento Premium (remoto)|
|Latência de e/s (aproximada)|ms de 5 a 7 (escrita)<br>ms de 5 a 10 (ler)|ms de 5 a 7 (escrita)<br>ms de 5 a 10 (ler)|ms de 5 a 7 (escrita)<br>ms de 5 a 10 (ler)|ms de 5 a 7 (escrita)<br>ms de 5 a 10 (ler)|ms de 5 a 7 (escrita)<br>ms de 5 a 10 (ler)|ms de 5 a 7 (escrita)<br>ms de 5 a 10 (ler)|ms de 5 a 7 (escrita)<br>ms de 5 a 10 (ler)|
|Destino IOPS (64 KB)|4000|4500|5000|6000|7000|7000|7000|
|Limites de velocidade de registo (MBps)|20|20|20|20|20|20|20|
|Máximo de trabalhadores simultâneos por conjunto (pedidos) *|1680|1890|2100|2520|33600|4200|8400|
|Inícios de sessão simultâneos máx. por conjunto (pedidos) *|1680|1890|2100|2520|33600|4200|8400|
|Número de DBs máximo por conjunto|500|500|500|500|500|500|500|
|Opções de vCore de conjuntos elásticos Mín/Máx por base de dados|0, 0.25, 0.5, 1...16|0, 0.25, 0.5, 1...18|0, 0.25, 0.5, 1...20|0, 0.25, 0.5, 1...20, 24|0, 0.25, 0.5, 1...20, 24, 32|0, 0.25, 0.5, 1...16, 24, 32, 40|0, 0.25, 0.5, 1...16, 24, 32, 40, 80|
|Número de réplicas|1|1|1|1|1|1|1|
|Multi-AZ|N/A|N/D|N/D|N/D|N/D|N/D|N/A|
|Leia o Escalamento horizontal|N/A|N/D|N/D|N/D|N/D|N/D|N/A|
|Armazenamento de cópia de segurança incluído|Tamanho de BD de X 1|Tamanho de BD de X 1|Tamanho de BD de X 1|Tamanho de BD de X 1|Tamanho de BD de X 1|Tamanho de BD de X 1|Tamanho de BD de X 1|

\* Para os máximos de trabalhadores simultâneos (pedidos) para qualquer base de dados individual, consulte [único limites de recursos da base de dados](sql-database-vcore-resource-limits-single-databases.md). Por exemplo, se estiver a utilizar o conjunto elástico Gen5 e seu vCore máximo por base de dados é 2, em seguida, os máximos de trabalhadores simultâneos é 200.  Se vCore máximo por base de dados for 0,5, em seguida, os máximos de trabalhadores simultâneos é 50, pois no Gen5 há um máximo de 100 trabalhadores simultâneos por vcore.  Para outras configurações de máximo de vCore por base de dados que são menos 1 vCore ou menos, o número de trabalhadores simultâneos máx. é da mesma forma redimensionadas.

## <a name="business-critical-service-tier-storage-sizes-and-compute-sizes"></a>Camada de negócio críticos de serviço: Tamanhos de armazenamento e tamanhos de computação

### <a name="business-critical-service-tier-generation-4-compute-platform-part-1"></a>Camada de negócio críticos de serviço: Plataforma de computação de geração 4 (parte 1)

|Tamanho de computação|BC_Gen4_1|BC_Gen4_2|BC_Gen4_3|BC_Gen4_4|BC_Gen4_5|BC_Gen4_6|
|:--- | --: |--: |--: |--: |--: |--: |
|Geração de H/W|4|4|4|4|4|4|
|vCores|1|2|3|4|5|6|
|Memória (GB)|7|14|21|28|35|42|
|Suporte de Columnstore|Sim|Sim|Sim|Sim|Sim|Sim|
|Armazenamento em OLTP na memória (GB)|1|2|3|4|5|6|
|Tipo de armazenamento|SSD local|SSD local|SSD local|SSD local|SSD local|SSD local|
|Tamanho máximo de dados (GB)|650|650|650|650|650|650|
|Tamanho máximo do registo (GB)|195|195|195|195|195|195|
|TempDB tamanho (GB)|32|64|96|128|160|192|
|Latência de e/s (aproximada)|1 a 2 ms (escrita)<br>ms de 1 a 2 (ler)|1 a 2 ms (escrita)<br>ms de 1 a 2 (ler)|1 a 2 ms (escrita)<br>ms de 1 a 2 (ler)|1 a 2 ms (escrita)<br>ms de 1 a 2 (ler)|1 a 2 ms (escrita)<br>ms de 1 a 2 (ler)|1 a 2 ms (escrita)<br>ms de 1 a 2 (ler)|
|Destino IOPS (64 KB)|5000|10000|15 000|20000|25000|30000|
|Limites de velocidade de registo (MBps)|6|12|18|24|30|36|
|Máximo de trabalhadores simultâneos por conjunto (pedidos) *|210|420|630|840|1050|1260|
|Inícios de sessão simultâneos máx. por conjunto (pedidos) *|210|420|630|840|1050|1260|
|Máximo permitido de sessões|30000|30000|30000|30000|30000|30000|
|Número de DBs máximo por conjunto|Apenas únicos bds são suportadas para este tamanho de computação|50|100|100|100|100|
|Opções de vCore de conjuntos elásticos Mín/Máx por base de dados|N/A|0, 0.25, 0.5, 1, 2|0, 0.25, 0.5, 1...3|0, 0.25, 0.5, 1...4|0, 0.25, 0.5, 1...5|0, 0.25, 0.5, 1...6|
|Número de réplicas|4|4|4|4|4|4|
|Multi-AZ|Sim|Sim|Sim|Sim|Sim|Sim|
|Leia o Escalamento horizontal|Sim|Sim|Sim|Sim|Sim|Sim|
|Armazenamento de cópia de segurança incluído|Tamanho de BD de X 1|Tamanho de BD de X 1|Tamanho de BD de X 1|Tamanho de BD de X 1|Tamanho de BD de X 1|Tamanho de BD de X 1|

\* Para os máximos de trabalhadores simultâneos (pedidos) para qualquer base de dados individual, consulte [único limites de recursos da base de dados](sql-database-vcore-resource-limits-single-databases.md). Por exemplo, se estiver a utilizar o conjunto elástico Gen5 e seu vCore máximo por base de dados é 2, em seguida, os máximos de trabalhadores simultâneos é 200.  Se vCore máximo por base de dados for 0,5, em seguida, os máximos de trabalhadores simultâneos é 50, pois no Gen5 há um máximo de 100 trabalhadores simultâneos por vcore.  Para outras configurações de máximo de vCore por base de dados que são menos 1 vCore ou menos, o número de trabalhadores simultâneos máx. é da mesma forma redimensionadas.

### <a name="business-critical-service-tier-generation-4-compute-platform-part-2"></a>Camada de negócio críticos de serviço: Plataforma de computação de geração 4 (parte 2)

|Tamanho de computação|BC_Gen4_7|BC_Gen4_8|BC_Gen4_9|BC_Gen4_10|BC_Gen4_16|BC_Gen4_24|
|:--- | --: |--: |--: |--: |--: |--: |
|Geração de H/W|4|4|4|4|4|4|
|vCores|7|8|9|10|16|24|
|Memória (GB)|81.6|91.8|102|122.4|163.2|204|408|
|Suporte de Columnstore|N/A|N/D|N/D|N/D|N/D|N/A|
|Armazenamento em OLTP na memória (GB)|7|8|9.5|11|20|36|
|Tipo de armazenamento|SSD local|SSD local|SSD local|SSD local|SSD local|SSD local|
|Tamanho máximo de dados (GB)|650|650|650|650|1024|1024|
|Tamanho máximo do registo (GB)|195|195|195|195|307|307|
|TempDB tamanho (GB)|224|256|288|320|384|384|
|Latência de e/s (aproximada)|1 a 2 ms (escrita)<br>ms de 1 a 2 (ler)|1 a 2 ms (escrita)<br>ms de 1 a 2 (ler)|1 a 2 ms (escrita)<br>ms de 1 a 2 (ler)|1 a 2 ms (escrita)<br>ms de 1 a 2 (ler)|1 a 2 ms (escrita)<br>ms de 1 a 2 (ler)|1 a 2 ms (escrita)<br>ms de 1 a 2 (ler)|
|Destino IOPS (64 KB)|35000|40000|45000|50000|80000|120000|
|Limites de velocidade de registo (MBps)|
|Máximo de trabalhadores simultâneos por conjunto (pedidos) *|1470|1680|1890|2100|3360|5040|
|Inícios de sessão simultâneos máx. por conjunto (pedidos) *|1470|1680|1890|2100|3360|5040|
|Máximo permitido de sessões|30000|30000|30000|30000|30000|30000|
|Número de DBs máximo por conjunto|100|100|100|100|100|100|
|Opções de vCore de conjuntos elásticos Mín/Máx por base de dados|0, 0.25, 0.5, 1...7|0, 0.25, 0.5, 1...8|0, 0.25, 0.5, 1...9|0, 0.25, 0.5, 1...10|0, 0.25, 0.5, 1...10, 16|0, 0.25, 0.5, 1...10, 16, 24|
|Número de réplicas|4|4|4|4|4|4|
|Multi-AZ|Sim|Sim|Sim|Sim|Sim|Sim|
|Leia o Escalamento horizontal|Sim|Sim|Sim|Sim|Sim|Sim|
|Armazenamento de cópia de segurança incluído|Tamanho de BD de X 1|Tamanho de BD de X 1|Tamanho de BD de X 1|Tamanho de BD de X 1|Tamanho de BD de X 1|Tamanho de BD de X 1|

\* Para os máximos de trabalhadores simultâneos (pedidos) para qualquer base de dados individual, consulte [único limites de recursos da base de dados](sql-database-vcore-resource-limits-single-databases.md). Por exemplo, se estiver a utilizar o conjunto elástico Gen5 e seu vCore máximo por base de dados é 2, em seguida, os máximos de trabalhadores simultâneos é 200.  Se vCore máximo por base de dados for 0,5, em seguida, os máximos de trabalhadores simultâneos é 50, pois no Gen5 há um máximo de 100 trabalhadores simultâneos por vcore.  Para outras configurações de máximo de vCore por base de dados que são menos 1 vCore ou menos, o número de trabalhadores simultâneos máx. é da mesma forma redimensionadas.

#### <a name="business-critical-service-tier-generation-5-compute-platform-part-1"></a>Camada de negócio críticos de serviço: Plataforma de computação de geração 5 (parte 1)

|Tamanho de computação|BC_Gen5_2|BC_Gen5_4|BC_Gen5_6|BC_Gen5_8|BC_Gen5_10|BC_Gen5_12|BC_Gen5_14|
|:--- | --: |--: |--: |--: |---: | --: |--: |--: |--: |--: |--: |--: |
|Geração de H/W|5|5|5|5|5|5|5|
|vCores|2|4|6|8|10|12|14|
|Memória (GB)|10.2|20.4|30.6|40.8|51|61.2|71.4|
|Suporte de Columnstore|Sim|Sim|Sim|Sim|Sim|Sim|Sim|
|Armazenamento em OLTP na memória (GB)|1.571|3.142|4.713|6.284|8.655|11.026|13.397|
|Tamanho máximo de dados (GB)|1024|1024|1536|1536|1536|3072|3072|
|Tamanho máximo do registo (GB)|307|307|307|461|461|922|922|
|TempDB tamanho (GB)|64|128|192|256|320|384|384|
|Tipo de armazenamento|SSD local|SSD local|SSD local|SSD local|SSD local|SSD local|SSD local|
|Latência de e/s (aproximada)|1 a 2 ms (escrita)<br>ms de 1 a 2 (ler)|1 a 2 ms (escrita)<br>ms de 1 a 2 (ler)|1 a 2 ms (escrita)<br>ms de 1 a 2 (ler)|1 a 2 ms (escrita)<br>ms de 1 a 2 (ler)|1 a 2 ms (escrita)<br>ms de 1 a 2 (ler)|1 a 2 ms (escrita)<br>ms de 1 a 2 (ler)|1 a 2 ms (escrita)<br>ms de 1 a 2 (ler)|
|Destino IOPS (64 KB)|5000|10000|15 000|20000|25000|30000|35000|
|Limites de velocidade de registo (MBps)|48|48|48|48|48|48|48|
|Máximo de trabalhadores simultâneos por conjunto (pedidos) *|210|420|630|840|1050|1260|1470|
|Inícios de sessão simultâneos máx. por conjunto (pedidos) *|210|420|630|840|1050|1260|1470|
|Máximo permitido de sessões|30000|30000|30000|30000|30000|30000|30000|
|Número de DBs máximo por conjunto|Apenas únicos bds são suportadas para este tamanho de computação|50|100|100|100|100|100|
|Opções de vCore de conjuntos elásticos Mín/Máx por base de dados|N/A|0, 0.25, 0.5, 1...4|0, 0.25, 0.5, 1...6|0, 0.25, 0.5, 1...8|0, 0.25, 0.5, 1...10|0, 0.25, 0.5, 1...12|0, 0.25, 0.5, 1...14|
|Número de réplicas|4|4|4|4|4|4|4|
|Multi-AZ|Sim|Sim|Sim|Sim|Sim|Sim|
|Leia o Escalamento horizontal|Sim|Sim|Sim|Sim|Sim|Sim|Sim|
|Armazenamento de cópia de segurança incluído|Tamanho de BD de X 1|Tamanho de BD de X 1|Tamanho de BD de X 1|Tamanho de BD de X 1|Tamanho de BD de X 1|Tamanho de BD de X 1|Tamanho de BD de X 1|

\* Para os máximos de trabalhadores simultâneos (pedidos) para qualquer base de dados individual, consulte [único limites de recursos da base de dados](sql-database-vcore-resource-limits-single-databases.md). Por exemplo, se estiver a utilizar o conjunto elástico Gen5 e seu vCore máximo por base de dados é 2, em seguida, os máximos de trabalhadores simultâneos é 200.  Se vCore máximo por base de dados for 0,5, em seguida, os máximos de trabalhadores simultâneos é 50, pois no Gen5 há um máximo de 100 trabalhadores simultâneos por vcore.  Para outras configurações de máximo de vCore por base de dados que são menos 1 vCore ou menos, o número de trabalhadores simultâneos máx. é da mesma forma redimensionadas.

#### <a name="business-critical-service-tier-generation-5-compute-platform-part-2"></a>Camada de negócio críticos de serviço: Plataforma de computação de geração 5 (parte 2)

|Tamanho de computação|BC_Gen5_16|BC_Gen5_18|BC_Gen5_20|BC_Gen5_24|BC_Gen5_32|BC_Gen5_40|BC_Gen5_80|
|:--- | --: |--: |--: |--: |---: | --: |--: |--: |--: |--: |--: |--: |
|Geração de H/W|5|5|5|5|5|5|5|
|vCores|16|18|20|24|32|40|80|
|Memória (GB)|81.6|91.8|102|122.4|163.2|204|408|
|Suporte de Columnstore|Sim|Sim|Sim|Sim|Sim|Sim|Sim|
|Armazenamento em OLTP na memória (GB)|15.768|18.139|20.51|25.252|37.936|52.22|131.64|
|Tamanho máximo de dados (GB)|3072|3072|3072|4096|4096|4096|4096|
|Tamanho máximo do registo (GB)|922|922|922|1229|1229|1229|1229|
|TempDB tamanho (GB)|384|384|384|384|384|384|384|
|Tipo de armazenamento|SSD local|SSD local|SSD local|SSD local|SSD local|SSD local|SSD local|
|Latência de e/s (aproximada)|1 a 2 ms (escrita)<br>ms de 1 a 2 (ler)|1 a 2 ms (escrita)<br>ms de 1 a 2 (ler)|1 a 2 ms (escrita)<br>ms de 1 a 2 (ler)|1 a 2 ms (escrita)<br>ms de 1 a 2 (ler)|1 a 2 ms (escrita)<br>ms de 1 a 2 (ler)|1 a 2 ms (escrita)<br>ms de 1 a 2 (ler)|1 a 2 ms (escrita)<br>ms de 1 a 2 (ler)|
|Destino IOPS (64 KB)|40000|45000|50000|60000|80000|100000|200000|
|Limites de velocidade de registo (MBps)|48|48|48|48|48|48|48|
|Máximo de trabalhadores simultâneos por conjunto (pedidos) *|1680|1890|2100|2520|3360|4200|8400|
|Inícios de sessão simultâneos máx. por conjunto (pedidos) *|1680|1890|2100|2520|3360|4200|8400|
|Máximo permitido de sessões|30000|30000|30000|30000|30000|30000|30000|
|Número de DBs máximo por conjunto|100|100|100|100|100|100|100|
|Opções de vCore de conjuntos elásticos Mín/Máx por base de dados|0, 0.25, 0.5, 1...16|0, 0.25, 0.5, 1...18|0, 0.25, 0.5, 1...20|0, 0.25, 0.5, 1...20, 24|0, 0.25, 0.5, 1...20, 24, 32|0, 0.25, 0.5, 1...20, 24, 32, 40|0, 0.25, 0.5, 1...20, 24, 32, 40, 80|
|Número de réplicas|4|4|4|4|4|4|4|
|Multi-AZ|Sim|Sim|Sim|Sim|Sim|Sim|Sim|
|Leia o Escalamento horizontal|Sim|Sim|Sim|Sim|Sim|Sim|Sim|
|Armazenamento de cópia de segurança incluído|Tamanho de BD de X 1|Tamanho de BD de X 1|Tamanho de BD de X 1|Tamanho de BD de X 1|Tamanho de BD de X 1|Tamanho de BD de X 1|Tamanho de BD de X 1|

\* Para os máximos de trabalhadores simultâneos (pedidos) para qualquer base de dados individual, consulte [único limites de recursos da base de dados](sql-database-vcore-resource-limits-single-databases.md). Por exemplo, se estiver a utilizar o conjunto elástico Gen5 e seu vCore máximo por base de dados é 2, em seguida, os máximos de trabalhadores simultâneos é 200.  Se vCore máximo por base de dados for 0,5, em seguida, os máximos de trabalhadores simultâneos é 50, pois no Gen5 há um máximo de 100 trabalhadores simultâneos por vcore.  Para outras configurações de máximo de vCore por base de dados que são menos 1 vCore ou menos, o número de trabalhadores simultâneos máx. é da mesma forma redimensionadas.

Se todos os vCores de um conjunto elástico está ocupados, cada base de dados no conjunto recebe uma quantidade igual de recursos de computação para processar consultas. O serviço Base de Dados SQL fornece equidade de partilha de recursos entre bases de dados, garantindo frações iguais de tempo de computação. Recurso de conjunto elástico equidade de partilha é qualquer quantidade de recursos garantido de outro modo para cada base de dados quando o mínimo de vCore por base de dados está definido como um valor diferente de zero.

## <a name="database-properties-for-pooled-databases"></a>Propriedades de base de dados para bases de dados agrupadas

A tabela seguinte descreve as propriedades de bases de dados agrupadas.

| Propriedade | Descrição |
|:--- |:--- |
| VCores máximo por base de dados |O número máximo de vCores que qualquer base de dados no conjunto pode utilizar, caso estejam disponíveis com base na utilização por outras bases de dados no conjunto. VCores máximo por base de dados não é uma garantia de recurso para uma base de dados. Esta definição é uma definição global que se aplica a todas as bases de dados no conjunto. Definir vCores máximo por base de dados alto o suficiente para lidar com picos na utilização da base de dados. Algum grau de consolidação excessiva é esperado uma vez que o agrupamento geralmente assume padrões de utilização frequente e esporádica para bases de dados em que todas as bases de dados são não simultaneamente com picos.|
| Min vCores por base de dados |O número mínimo de vCores que é a garantia de qualquer base de dados no conjunto. Esta definição é uma definição global que se aplica a todas as bases de dados no conjunto. Os vCores min por base de dados pode ser definidos como 0 e também é o valor predefinido. Esta propriedade é definida como qualquer valor entre 0 e a utilização de vCores média por base de dados. O produto do número de bases de dados no conjunto e os vCores min por base de dados não pode exceder os vCores por conjunto.|
| Armazenamento máximo por base de dados |O tamanho de base de dados máximo definido pelo utilizador para uma base de dados num conjunto. Bases de dados agrupadas partilham o armazenamento de agrupamento alocados, pelo que o tamanho pode chegar a uma base de dados é limitado ao agrupamento de armazenamento mais pequeno de restantes e o tamanho de base de dados. Tamanho da base de dados de máx. refere-se ao tamanho máximo dos ficheiros de dados e não inclui o espaço utilizado por ficheiros de registo. |
|||

## <a name="next-steps"></a>Passos Seguintes

- Para limites de recursos de vCore para uma base de dados, consulte [limites de recursos para bases de dados individuais com o modelo de compra baseado em vCore](sql-database-vcore-resource-limits-single-databases.md)
- Para limites de recursos DTU para bases de dados individuais, consulte [limites de recursos para bases de dados individuais com o modelo de compra baseado em DTU](sql-database-dtu-resource-limits-single-databases.md)
- Para limites de recursos DTU para conjuntos elásticos, consulte [limites de recursos para conjuntos elásticos com o modelo de compra baseado em DTU](sql-database-dtu-resource-limits-elastic-pools.md)
- Para limites de recursos para instâncias geridas, consulte [geridos limites de recursos de instância](sql-database-managed-instance-resource-limits.md).
- Para obter informações sobre os limites do Azure gerais, consulte [subscrição do Azure e limites do serviço, quotas e restrições](../azure-subscription-service-limits.md).
- Para obter informações sobre limites de recursos num servidor de base de dados, consulte [descrição geral dos limites de recursos num servidor de base de dados SQL](sql-database-resource-limits-database-server.md) para obter informações sobre os limites nos níveis de servidor e de subscrição.
