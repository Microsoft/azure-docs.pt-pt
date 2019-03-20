---
title: Limites de recurso baseado em vcore da base de dados SQL do Azure - base de dados | Documentos da Microsoft
description: Esta página descreve alguns limites de recursos baseados em vCore comuns para uma base de dados na base de dados do Azure SQL.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: CarlRabeler
ms.author: carlrab
ms.reviewer: ''
manager: craigg
ms.date: 03/12/2019
ms.openlocfilehash: 990e6b4d869df030bfddfeb2d60f0ca0e23ea8ca
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57902268"
---
# <a name="azure-sql-database-vcore-based-purchasing-model-limits-for-a-single-database"></a>Base de dados do SQL do Azure limites de modelo para uma base de dados de compra baseado em vCore

Este artigo fornece os limites de recursos detalhados para a base de dados do Azure SQL únicas bases de dados com o modelo de compra baseado em vCore.

Para baseado em DTU compra modelo limites para bases de dados individuais num servidor de base de dados SQL, consulte [limita a visão geral dos recursos num servidor de base de dados SQL](sql-database-resource-limits-database-server.md).

> [!IMPORTANT]
> Em algumas circunstâncias, poderá ter reduzir uma base de dados para recuperar espaço não utilizado. Para obter mais informações, consulte [gerir o espaço de ficheiro na base de dados do Azure SQL](sql-database-file-space-management.md).

Pode definir o escalão de serviço, o tamanho de computação e a quantidade de armazenamento para uma base de dados individual utilizando o [portal do Azure](sql-database-single-databases-manage.md#manage-an-existing-sql-database-server), [Transact-SQL](sql-database-single-databases-manage.md#transact-sql-manage-sql-database-servers-and-single-databases), [PowerShell](sql-database-single-databases-manage.md#powershell-manage-sql-database-servers-and-single-databases), o [ CLI do Azure](sql-database-single-databases-manage.md#azure-cli-manage-sql-database-servers-and-single-databases), ou o [API de REST](sql-database-single-databases-manage.md#rest-api-manage-sql-database-servers-and-single-databases).

> [!IMPORTANT]
> Para dimensionar a orientação e considerações, consulte [dimensionar uma base de dados](sql-database-single-database-scale.md).

## <a name="general-purpose-service-tier-storage-sizes-and-compute-sizes"></a>Escalão de serviço de fins geral: Tamanhos de armazenamento e tamanhos de computação

### <a name="general-purpose-service-tier-generation-4-compute-platform-part-1"></a>Escalão de serviço de fins geral: Plataforma de computação de geração 4 (parte 1)

|Tamanho de computação|GP_Gen4_1|GP_Gen4_2|GP_Gen4_3|GP_Gen4_4|GP_Gen4_5|GP_Gen4_6
|:--- | --: |--: |--: |--: |--: |--: |
|Geração de H/W|4|4|4|4|4|4|
|vCores|1|2|3|4|5|6|
|Memória (GB)|7|14|21|28|35|42|
|Suporte de Columnstore|Sim|Sim|Sim|Sim|Sim|Sim|
|Armazenamento em OLTP na memória (GB)|N/A|N/D|N/D|N/D|N/D|N/A|
|Tamanho máximo de dados (GB)|1024|1024|1024|1536|1536|1536|
|Tamanho máximo do registo (GB)|307|307|307|461|461|461|
|TempDB tamanho (GB)|32|64|96|128|160|192|
|Tipo de armazenamento|Armazenamento Premium (remoto)|Armazenamento Premium (remoto)|Armazenamento Premium (remoto)|Armazenamento Premium (remoto)|Armazenamento Premium (remoto)|Armazenamento Premium (remoto)|
|Latência de e/s (aproximada)|ms de 5 a 7 (escrita)<br>ms de 5 a 10 (ler)|ms de 5 a 7 (escrita)<br>ms de 5 a 10 (ler)|ms de 5 a 7 (escrita)<br>ms de 5 a 10 (ler)|ms de 5 a 7 (escrita)<br>ms de 5 a 10 (ler)|ms de 5 a 7 (escrita)<br>ms de 5 a 10 (ler)|ms de 5 a 7 (escrita)<br>ms de 5 a 10 (ler)|
|Destino IOPS (64 KB)|500|1000|1500|2000|2500|3000|
|Limites de velocidade de registo (MBps)|2.5|5|7.5|10|12.5|15|
|Máximo de trabalhadores simultâneos (pedidos)|200|400|600|800|1000|1200|
|Máximo permitido de sessões|30000|30000|30000|30000|30000|30000|
|Número de réplicas|1|1|1|1|1|1|
|Multi-AZ|N/A|N/D|N/D|N/D|N/D|N/A|
|Leia o Escalamento horizontal|N/A|N/D|N/D|N/D|N/D|N/A|
|Armazenamento de cópia de segurança incluído|Tamanho de BD de X 1|Tamanho de BD de X 1|Tamanho de BD de X 1|Tamanho de BD de X 1|Tamanho de BD de X 1|Tamanho de BD de X 1|

### <a name="general-purpose-service-tier-generation-4-compute-platform-part-2"></a>Escalão de serviço de fins geral: Plataforma de computação de geração 4 (parte 2)

|Tamanho de computação|GP_Gen4_7|GP_Gen4_8|GP_Gen4_9|GP_Gen4_10|GP_Gen4_16|GP_Gen4_24
|:--- | --: |--: |--: |--: |--: |--: |
|Geração de H/W|4|4|4|4|4|4|
|vCores|7|8|9|10|16|24|
|Memória (GB)|49|56|63|70|112|168|
|Suporte de Columnstore|Sim|Sim|Sim|Sim|Sim|Sim|
|Armazenamento em OLTP na memória (GB)|N/A|N/D|N/D|N/D|N/D|N/A|
|Tamanho máximo de dados (GB)|1536|3072|3072|3072|4096|4096|
|Tamanho máximo do registo (GB)|461|922|922|922|1229|1229|
|TempDB tamanho (GB)|224|256|288|320|384|384|
|Tipo de armazenamento|Armazenamento Premium (remoto)|Armazenamento Premium (remoto)|Armazenamento Premium (remoto)|Armazenamento Premium (remoto)|Armazenamento Premium (remoto)|Armazenamento Premium (remoto)|
|Latência de e/s (aproximada)|ms de 5 a 7 (escrita)<br>ms de 5 a 10 (ler)|ms de 5 a 7 (escrita)<br>ms de 5 a 10 (ler)|ms de 5 a 7 (escrita)<br>ms de 5 a 10 (ler)|ms de 5 a 7 (escrita)<br>ms de 5 a 10 (ler)|ms de 5 a 7 (escrita)<br>ms de 5 a 10 (ler)|ms de 5 a 7 (escrita)<br>ms de 5 a 10 (ler)
|Destino IOPS (64 KB)|3500|4000|4500|5000|7000|7000|
|Limites de velocidade de registo (MBps)|17.5|20|20|20|20|20|
|Máximo de trabalhadores simultâneos (pedidos)|1400|1600|1800|2000|3200|4800|
|Máximo permitido de sessões|30000|30000|30000|30000|30000|30000|
|Número de réplicas|1|1|1|1|1|1|
|Multi-AZ|N/A|N/D|N/D|N/D|N/D|N/A|
|Leia o Escalamento horizontal|N/A|N/D|N/D|N/D|N/D|N/A|
|Armazenamento de cópia de segurança incluído|Tamanho de BD de X 1|Tamanho de BD de X 1|Tamanho de BD de X 1|Tamanho de BD de X 1|Tamanho de BD de X 1|Tamanho de BD de X 1|

### <a name="general-purpose-service-tier-generation-5-compute-platform-part-1"></a>Escalão de serviço de fins geral: Plataforma de computação de geração 5 (parte 1)

|Tamanho de computação|GP_Gen5_2|GP_Gen5_4|GP_Gen5_6|GP_Gen5_8|GP_Gen5_10|GP_Gen5_12|GP_Gen5_14|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|Geração de H/W|5|5|5|5|5|5|5|
|vCores|2|4|6|8|10|12|14|
|Memória (GB)|10.2|20.4|30.6|40.8|51|61.2|71.4|
|Suporte de Columnstore|Sim|Sim|Sim|Sim|Sim|Sim|Sim|
|Armazenamento em OLTP na memória (GB)|N/A|N/D|N/D|N/D|N/D|N/D|N/A|
|Tamanho máximo de dados (GB)|1024|1024|1536|1536|1536|3072|3072|
|Tamanho máximo do registo (GB)|307|307|307|461|461|461|461|
|TempDB tamanho (GB)|64|128|192|256|320|384|384|
|Tipo de armazenamento|Armazenamento Premium (remoto)|Armazenamento Premium (remoto)|Armazenamento Premium (remoto)|Armazenamento Premium (remoto)|Armazenamento Premium (remoto)|Armazenamento Premium (remoto)|Armazenamento Premium (remoto)|
|Latência de e/s (aproximada)|ms de 5 a 7 (escrita)<br>ms de 5 a 10 (ler)|ms de 5 a 7 (escrita)<br>ms de 5 a 10 (ler)|ms de 5 a 7 (escrita)<br>ms de 5 a 10 (ler)|ms de 5 a 7 (escrita)<br>ms de 5 a 10 (ler)|ms de 5 a 7 (escrita)<br>ms de 5 a 10 (ler)|ms de 5 a 7 (escrita)<br>ms de 5 a 10 (ler)|ms de 5 a 7 (escrita)<br>ms de 5 a 10 (ler)|
|Destino IOPS (64 KB)|500|1000|1500|2000|2500|3000|3500|
|Limites de velocidade de registo (MBps)|2.5|56|7.5|10|12.5|15|17.5|
|Máximo de trabalhadores simultâneos (pedidos)|200|400|600|800|1000|1200|1400|
|Máximo permitido de sessões|30000|30000|30000|30000|30000|30000|30000|
|Número de réplicas|1|1|1|1|1|1|1|
|Multi-AZ|N/A|N/D|N/D|N/D|N/D|N/D|N/A|
|Leia o Escalamento horizontal|N/A|N/D|N/D|N/D|N/D|N/D|N/A|
|Armazenamento de cópia de segurança incluído|Tamanho de BD de X 1|Tamanho de BD de X 1|Tamanho de BD de X 1|Tamanho de BD de X 1|Tamanho de BD de X 1|Tamanho de BD de X 1|Tamanho de BD de X 1|

### <a name="general-purpose-service-tier-generation-5-compute-platform-part-2"></a>Escalão de serviço de fins geral: Plataforma de computação de geração 5 (parte 2)

|Tamanho de computação|GP_Gen5_16|GP_Gen5_18|GP_Gen5_20|GP_Gen5_24|GP_Gen5_32|GP_Gen5_40|GP_Gen5_80|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|Geração de H/W|5|5|5|5|5|5|5|
|vCores|16|18|20|24|32|40|80|
|Memória (GB)|81.6|91.8|102|122.4|163.2|204|408|
|Suporte de Columnstore|Sim|Sim|Sim|Sim|Sim|Sim|Sim|
|Armazenamento em OLTP na memória (GB)|N/A|N/D|N/D|N/D|N/D|N/D|N/A|
|Tamanho máximo de dados (GB)|3072|3072|3072|4096|4096|4096|4096|
|Tamanho máximo do registo (GB)|922|922|922|1229|1229|1229|1229|
|TempDB tamanho (GB)|384|384|384|384|384|384|384|
|Tipo de armazenamento|Armazenamento Premium (remoto)|Armazenamento Premium (remoto)|Armazenamento Premium (remoto)|Armazenamento Premium (remoto)|Armazenamento Premium (remoto)|Armazenamento Premium (remoto)|Armazenamento Premium (remoto)|
|Latência de e/s (aproximada)|ms de 5 a 7 (escrita)<br>ms de 5 a 10 (ler)|ms de 5 a 7 (escrita)<br>ms de 5 a 10 (ler)|ms de 5 a 7 (escrita)<br>ms de 5 a 10 (ler)|ms de 5 a 7 (escrita)<br>ms de 5 a 10 (ler)|ms de 5 a 7 (escrita)<br>ms de 5 a 10 (ler)|ms de 5 a 7 (escrita)<br>ms de 5 a 10 (ler)|ms de 5 a 7 (escrita)<br>ms de 5 a 10 (ler)|
|Destino IOPS (64 KB)|4000|4500|5000|6000|7000|7000|7000|
|Limites de velocidade de registo (MBps)|20|20|20|20|20|20|20|
|Máximo de trabalhadores simultâneos (pedidos)|1600|1800|2000|2400|3200|4000|8000|
|Máximo permitido de sessões|30000|30000|30000|30000|30000|30000|30000|
|Número de réplicas|1|1|1|1|1|1|1|
|Multi-AZ|N/A|N/D|N/D|N/D|N/D|N/D|N/A|
|Leia o Escalamento horizontal|N/A|N/D|N/D|N/D|N/D|N/D|N/A|
|Armazenamento de cópia de segurança incluído|Tamanho de BD de X 1|Tamanho de BD de X 1|Tamanho de BD de X 1|Tamanho de BD de X 1|Tamanho de BD de X 1|Tamanho de BD de X 1|Tamanho de BD de X 1|

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
|Máximo de trabalhadores simultâneos (pedidos)|200|400|600|800|1000|1200|
|Inícios de sessão simultâneos máx.|200|400|600|800|1000|1200|
|Máximo permitido de sessões|30000|30000|30000|30000|30000|30000|
|Número de réplicas|4|4|4|4|4|4|
|Multi-AZ|N/A|N/D|N/D|N/D|N/D|N/A|
|Leia o Escalamento horizontal|Sim|Sim|Sim|Sim|Sim|Sim|
|Armazenamento de cópia de segurança incluído|Tamanho de BD de X 1|Tamanho de BD de X 1|Tamanho de BD de X 1|Tamanho de BD de X 1|Tamanho de BD de X 1|Tamanho de BD de X 1|

### <a name="business-critical-service-tier-generation-4-compute-platform-part-2"></a>Camada de negócio críticos de serviço: Plataforma de computação de geração 4 (parte 2)

|Tamanho de computação|BC_Gen4_7|BC_Gen4_8|BC_Gen4_9|BC_Gen4_10|BC_Gen4_16|BC_Gen4_24|
|:--- | --: |--: |--: |--: |--: |--: |
|Geração de H/W|4|4|4|4|4|4|
|vCores|7|8|9|10|16|24|
|Memória (GB)|49|56|63|70|112|168|
|Suporte de Columnstore|Sim|Sim|Sim|Sim|Sim|Sim|
|Armazenamento em OLTP na memória (GB)|7|8|9.5|11|20|36|
|Tipo de armazenamento|SSD local|SSD local|SSD local|SSD local|SSD local|SSD local|
|Tamanho máximo de dados (GB)|650|650|650|650|1024|1024|
|Tamanho máximo do registo (GB)|195|195|195|195|307|307|
|TempDB tamanho (GB)|224|256|288|320|384|384|
|Latência de e/s (aproximada)|1 a 2 ms (escrita)<br>ms de 1 a 2 (ler)|1 a 2 ms (escrita)<br>ms de 1 a 2 (ler)|1 a 2 ms (escrita)<br>ms de 1 a 2 (ler)|1 a 2 ms (escrita)<br>ms de 1 a 2 (ler)|1 a 2 ms (escrita)<br>ms de 1 a 2 (ler)|1 a 2 ms (escrita)<br>ms de 1 a 2 (ler)|
|Destino IOPS (64 KB)|35000|40000|45000|50000|80000|120000|
|Limites de velocidade de registo (MBps)|42|48|54|60|64|64|
|Máximo de trabalhadores simultâneos (pedidos)|1400|1600|1800|2000|3200|4800|
|Inícios de sessão simultâneos do máx. (pedidos)|1400|1600|1800|2000|3200|4800|
|Máximo permitido de sessões|30000|30000|30000|30000|30000|30000|
|Número de réplicas|4|4|4|4|4|4|
|Multi-AZ|N/A|N/D|N/D|N/D|N/D|N/A|
|Leia o Escalamento horizontal|Sim|Sim|Sim|Sim|Sim|Sim|
|Armazenamento de cópia de segurança incluído|Tamanho de BD de X 1|Tamanho de BD de X 1|Tamanho de BD de X 1|Tamanho de BD de X 1|Tamanho de BD de X 1|Tamanho de BD de X 1|

### <a name="business-critical-service-tier-generation-5-compute-platform-part-1"></a>Camada de negócio críticos de serviço: Plataforma de computação de geração 5 (parte 1)

|Tamanho de computação|BC_Gen5_2|BC_Gen5_4|BC_Gen5_6|BC_Gen5_8|BC_Gen5_10|BC_Gen5_12|BC_Gen5_14|
|:--- | --: |--: |--: |--: |---: | --: |--: |
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
|Limites de velocidade de registo (MBps)|6|12|18|24|30|36|42|
|Máximo de trabalhadores simultâneos (pedidos)|200|400|600|800|1000|1200|1400|
|Inícios de sessão simultâneos máx.|200|400|600|800|1000|1200|1400|
|Máximo permitido de sessões|30000|30000|30000|30000|30000|30000|30000|
|Número de réplicas|4|4|4|4|4|4|4|
|Multi-AZ|Sim|Sim|Sim|Sim|Sim|Sim|Sim|
|Leia o Escalamento horizontal|N/A|N/D|N/D|N/D|N/D|N/D|N/A|
|Armazenamento de cópia de segurança incluído|Tamanho de BD de X 1|Tamanho de BD de X 1|Tamanho de BD de X 1|Tamanho de BD de X 1|Tamanho de BD de X 1|Tamanho de BD de X 1|Tamanho de BD de X 1|

### <a name="business-critical-service-tier-generation-5-compute-platform-part-2"></a>Camada de negócio críticos de serviço: Plataforma de computação de geração 5 (parte 2)

|Tamanho de computação|BC_Gen5_16|BC_Gen5_18|BC_Gen5_20|BC_Gen5_24|BC_Gen5_32|BC_Gen5_40|BC_Gen5_80|
|:--- | --: |--: |--: |--: |---: | --: |--: |
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
|Limites de velocidade de registo (MBps)|48|54|60|72|96|96|96|
|Máximo de trabalhadores simultâneos (pedidos)|1600|1800|2000|2400|3200|4000|8000|
|Inícios de sessão simultâneos máx.|1600|1800|2000|2400|3200|4000|8000|
|Máximo permitido de sessões|30000|30000|30000|30000|30000|30000|30000|
|Número de réplicas|4|4|4|4|4|4|4|
|Multi-AZ|Sim|Sim|Sim|Sim|Sim|Sim|Sim|
|Leia o Escalamento horizontal|N/A|N/D|N/D|N/D|N/D|N/D|N/A|
|Armazenamento de cópia de segurança incluído|Tamanho de BD de X 1|Tamanho de BD de X 1|Tamanho de BD de X 1|Tamanho de BD de X 1|Tamanho de BD de X 1|Tamanho de BD de X 1|Tamanho de BD de X 1|

## <a name="hyperscale-service-tier-preview"></a>Camada de serviços de Hiperescala (pré-visualização)

### <a name="generation-4-compute-platform-storage-sizes-and-compute-sizes"></a>Plataforma de computação de geração 4: Tamanhos de armazenamento e tamanhos de computação

|Nível de desempenho|HS_Gen4_1|HS_Gen4_2|HS_Gen4_4|HS_Gen4_8|HS_Gen4_16|HS_Gen4_24|
|:--- | --: |--: |--: |--: |--: |--: |
|Geração de H/W|4|4|4|4|4|4|
|vCores|1|2|4|8|16|24|
|Memória (GB)|7|14|28|56|112|168|
|Suporte de Columnstore|Sim|Sim|Sim|Sim|Sim|Sim|
|Armazenamento em OLTP na memória (GB)|N/A|N/D|N/D|N/D|N/D|N/A|
|Tamanho máximo de dados (TB)|100 |100 |100 |100 |100 |100 |
|Tamanho máximo do registo (TB)|1 |1 |1 |1 |1 |1 |
|TempDB tamanho (GB)|32|64|128|256|384|384|
|Tipo de armazenamento|SSD local|SSD local|SSD local|SSD local|SSD local|SSD local|
|Destino IOPS (64 KB)|Ser determinada|Ser determinada|Ser determinada|Ser determinada|Ser determinada|Ser determinada|
|Latência de e/s (aproximada)|Ser determinada|Ser determinada|Ser determinada|Ser determinada|Ser determinada|Ser determinada|
|Máximo de trabalhadores simultâneos (pedidos)|200|400|800|1600|3200|4800|
|Máximo permitido de sessões|30000|30000|30000|30000|30000|30000|
|Número de réplicas|2|2|2|2|2|2|
|Multi-AZ|N/A|N/D|N/D|N/D|N/D|N/A|
|Leia o Escalamento horizontal|Sim|Sim|Sim|Sim|Sim|Sim|
|Armazenamento de cópia de segurança incluído|7|7|7|7|7|7|
|||

### <a name="generation-5-compute-platform"></a>Plataforma de computação de geração 5

|Nível de desempenho|HS_Gen5_2|HS_Gen5_4|HS_Gen5_8|HS_Gen5_16|HS_Gen5_24|HS_Gen5_32|HS_Gen5_40|HS_Gen5_80|
|:--- | --: |--: |--: |--: |---: | --: |--: |--: |
|Geração de H/W|5|5|5|5|5|5|5|5|
|vCores|2|4|8|16|24|32|40|80|
|Memória (GB)|10.2|20.4|40.8|81.6|122.4|163.2|204|408|
|Suporte de Columnstore|Sim|Sim|Sim|Sim|Sim|Sim|Sim|Sim|
|Armazenamento em OLTP na memória (GB)|N/A|N/D|N/D|N/D|N/D|N/D|N/D|N/A|
|Tamanho máximo de dados (TB)|100 |100 |100 |100 |100 |100 |100 |100 |
|Tamanho máximo do registo (TB)|1 |1 |1 |1 |1 |1 |1 |1 |
|TempDB tamanho (GB)|64|128|256|384|384|384|384|384|
|Tipo de armazenamento|SSD local|SSD local|SSD local|SSD local|SSD local|SSD local|SSD local|SSD local|
|Destino IOPS (64 KB)|Ser determinada|Ser determinada|Ser determinada|Ser determinada|Ser determinada|Ser determinada|Ser determinada|Ser determinada|
|Latência de e/s (aproximada)|Ser determinada|Ser determinada|Ser determinada|Ser determinada|Ser determinada|Ser determinada|Ser determinada|Ser determinada|
|Máximo de trabalhadores simultâneos (pedidos)|200|400|800|1600|2400|3200|4000|8000|
|Máximo permitido de sessões|30000|30000|30000|30000|30000|30000|30000|30000|
|Número de réplicas|2|2|2|2|2|2|2|2|
|Multi-AZ|N/A|N/D|N/D|N/D|N/D|N/D|N/D|N/A|
|Leia o Escalamento horizontal|Sim|Sim|Sim|Sim|Sim|Sim|Sim|Sim|
|Armazenamento de cópia de segurança incluído (limite de pré-visualização)|7|7|7|7|7|7|7|7|
|||

## <a name="next-steps"></a>Passos Seguintes

- Para limites de recursos DTU para bases de dados individuais, consulte [limites de recursos para bases de dados individuais com o modelo de compra baseado em DTU](sql-database-dtu-resource-limits-single-databases.md)
- Para limites de recursos de vCore para conjuntos elásticos, consulte [limites de recursos para conjuntos elásticos com o modelo de compra baseado em vCore](sql-database-vcore-resource-limits-elastic-pools.md)
- Para limites de recursos DTU para conjuntos elásticos, consulte [limites de recursos para conjuntos elásticos com o modelo de compra baseado em DTU](sql-database-dtu-resource-limits-elastic-pools.md)
- Para limites de recursos para instâncias geridas, consulte [geridos limites de recursos de instância](sql-database-managed-instance-resource-limits.md).
- Para obter informações sobre os limites do Azure gerais, consulte [subscrição do Azure e limites do serviço, quotas e restrições](../azure-subscription-service-limits.md).
- Para obter informações sobre limites de recursos num servidor de base de dados, consulte [descrição geral dos limites de recursos num servidor de base de dados SQL](sql-database-resource-limits-database-server.md) para obter informações sobre os limites nos níveis de servidor e de subscrição.
