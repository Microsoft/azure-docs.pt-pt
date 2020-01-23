---
title: limites de recursos vCore-banco de dados individual
description: Esta página descreve alguns limites de recursos vCore comuns para um único banco de dados no banco de dados SQL do Azure.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 01/22/2020
ms.openlocfilehash: 267779afc749fccba41935741630a759576d6e77
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/22/2020
ms.locfileid: "76515025"
---
# <a name="resource-limits-for-single-databases-using-the-vcore-purchasing-model"></a>Limites de recursos para bancos de dados individuais usando o modelo de compra vCore

Este artigo fornece os limites de recursos detalhados para bancos de dados individuais do banco de dados SQL do Azure usando o modelo de compra vCore.

Para os limites de modelo de compra de DTU para bancos de dados individuais em um servidor de banco de dados SQL, consulte [visão geral dos limites de recursos em um servidor de banco de dados SQL](sql-database-resource-limits-database-server.md).

Você pode definir a camada de serviço, o tamanho da computação e a quantidade de armazenamento para um único banco de dados usando o [portal do Azure](sql-database-single-databases-manage.md#manage-an-existing-sql-database-server), [Transact-SQL](sql-database-single-databases-manage.md#transact-sql-manage-sql-database-servers-and-single-databases), [PowerShell](sql-database-single-databases-manage.md#powershell-manage-sql-database-servers-and-single-databases), o [CLI do Azure](sql-database-single-databases-manage.md#azure-cli-manage-sql-database-servers-and-single-databases)ou a [API REST](sql-database-single-databases-manage.md#rest-api-manage-sql-database-servers-and-single-databases).

> [!IMPORTANT]
> Para obter diretrizes e considerações sobre o dimensionamento, consulte [dimensionar um banco de dados individual](sql-database-single-database-scale.md).

## <a name="general-purpose---serverless-compute---gen5"></a>Computação de uso geral sem servidor-Gen5

A [camada de computação sem servidor](sql-database-serverless.md) está disponível atualmente somente em hardware Gen5.

### <a name="gen5-compute-generation-part-1"></a>Geração de computação Gen5 (parte 1)

|Tamanho da computação|GP_S_Gen5_1|GP_S_Gen5_2|GP_S_Gen5_4|GP_S_Gen5_6|GP_S_Gen5_8|
|:--- | --: |--: |--: |--: |--: |
|Geração de computação|Gen5|Gen5|Gen5|Gen5|Gen5|
|VCores mín. máx.|0.5-1|0.5-2|0,5-4|0,75 a 6|1.0-8|
|Memória mín. máx. (GB)|2.02-3|2.05-6|2.10-12|2,25-18|3,00-24|
|Mínimo de atraso de pausa automática (minutos)|60|60|60|60|60|
|Suporte a Columnstore|Sim|Sim|Sim|Sim|Sim|
|Armazenamento OLTP na memória (GB)|N/A|N/A|N/A|N/A|N/A|
|Tamanho máximo de dados (GB)|512|1024|1024|1024|1536|
|Tamanho máximo do log (GB)|154|307|307|307|461|
|Tamanho máximo de dados de TempDB (GB)|32|64|128|192|256|
|Tipo de armazenamento|SSD remoto|SSD remoto|SSD remoto|SSD remoto|SSD remoto|
|Latência de e/s (aproximada)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|
|IOPS de dados máximo *|320|640|1280|1920|2560|
|Taxa máxima de logs (MBps)|3.8|7.5|15|22.5|30|
|Máximo de trabalhos simultâneos (solicitações)|75|150|300|450|600|
|Sessões simultâneas máx.|30,000|30,000|30,000|30,000|30,000|
|Número de réplicas|1|1|1|1|1|
|Multi-AZ|N/A|N/A|N/A|N/A|N/A|
|Escalamento Horizontal de Leituras|N/A|N/A|N/A|N/A|N/A|
|Armazenamento de backup incluído|tamanho do banco de BD 1X|tamanho do banco de BD 1X|tamanho do banco de BD 1X|tamanho do banco de BD 1X|tamanho do banco de BD 1X|

\* o valor máximo para tamanhos de e/s variando entre 8 KB e 64 KB. IOPS reais são dependentes da carga de trabalho. Para obter detalhes, consulte [governança de e/s de dados](sql-database-resource-limits-database-server.md#resource-governance).

### <a name="gen5-compute-generation-part-2"></a>Geração de computação Gen5 (parte 2)

|Tamanho da computação|GP_S_Gen5_10|GP_S_Gen5_12|GP_S_Gen5_14|GP_S_Gen5_16|
|:--- | --: |--: |--: |--: |
|Geração de computação|Gen5|Gen5|Gen5|Gen5|
|VCores mín. máx.|1,25-10|1,50-12|1,75-14|2,00-16|
|Memória mín. máx. (GB)|3,75-30|4.50-36|5,25-42|6.00-48|
|Mínimo de atraso de pausa automática (minutos)|60|60|60|60|
|Suporte a Columnstore|Sim|Sim|Sim|Sim|
|Armazenamento OLTP na memória (GB)|N/A|N/A|N/A|N/A|
|Tamanho máximo de dados (GB)|1536|3072|3072|3072|
|Tamanho máximo do log (GB)|461|461|461|922|
|Tamanho máximo de dados de TempDB (GB)|320|384|448|512|
|Tipo de armazenamento|SSD remoto|SSD remoto|SSD remoto|SSD remoto|
|Latência de e/s (aproximada)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|
|IOPS de dados máximo *|3200|3840|4480|5120|
|Taxa máxima de logs (MBps)|30|30|30|30|
|Máximo de trabalhos simultâneos (solicitações)|750|900|1050|1200|
|Sessões simultâneas máx.|30,000|30,000|30,000|30,000|
|Número de réplicas|1|1|1|1|
|Multi-AZ|N/A|N/A|N/A|N/A|
|Escalamento Horizontal de Leituras|N/A|N/A|N/A|N/A|
|Armazenamento de backup incluído|tamanho do banco de BD 1X|tamanho do banco de BD 1X|tamanho do banco de BD 1X|tamanho do banco de BD 1X|

\* o valor máximo para tamanhos de e/s variando entre 8 KB e 64 KB. IOPS reais são dependentes da carga de trabalho. Para obter detalhes, consulte [governança de e/s de dados](sql-database-resource-limits-database-server.md#resource-governance).

## <a name="hyperscale---provisioned-compute---gen4"></a>Hiperescala – computação provisionada-Gen4

### <a name="gen4-compute-generation-part-1"></a>Geração de computação Gen4 (parte 1)

|Nível de desempenho|HS_Gen4_1|HS_Gen4_2|HS_Gen4_3|HS_Gen4_4|HS_Gen4_5|HS_Gen4_6|
|:--- | --: |--: |--: |---: | --: |--: |
|Geração de computação|Gen4|Gen4|Gen4|Gen4|Gen4|Gen4|
|vCores|1|2|3|4|5|6|
|Memória (GB)|7|14|21|28|35|42|
|[RBPEX](sql-database-service-tier-hyperscale.md#compute) Tamanho|3X de memória|3X de memória|3X de memória|3X de memória|3X de memória|3X de memória|
|Suporte a Columnstore|Sim|Sim|Sim|Sim|Sim|Sim|
|Armazenamento OLTP na memória (GB)|N/A|N/A|N/A|N/A|N/A|N/A|
|Tamanho máximo de dados (TB)|100 |100 |100 |100 |100 |100|
|Tamanho máximo do log (TB)|1 |1 |1 |1 |1 |1 |
|Tamanho máximo de dados de TempDB (GB)|32|64|96|128|160|192|
|Tipo de armazenamento| [Observação 1](#notes) |[Observação 1](#notes)|[Observação 1](#notes) |[Observação 1](#notes) |[Observação 1](#notes) |[Observação 1](#notes) |
|IOPS de dados máximo *|[Observação 2](#notes)|[Observação 2](#notes)|[Observação 2](#notes)|[Observação 2](#notes)|[Observação 2](#notes)|[Observação 2](#notes)|
|Latência de e/s (aproximada)|[Observação 3](#notes)|[Observação 3](#notes)|[Observação 3](#notes)|[Observação 3](#notes)|[Observação 3](#notes)|[Observação 3](#notes)|
|Máximo de trabalhos simultâneos (solicitações)|200|400|600|800|1000|1200|
|Sessões simultâneas máx.|30,000|30,000|30,000|30,000|30,000|30,000|
|Réplicas secundárias|0-4|0-4|0-4|0-4|0-4|0-4|
|Multi-AZ|N/A|N/A|N/A|N/A|N/A|N/A|
|Escalamento Horizontal de Leituras|Sim|Sim|Sim|Sim|Sim|Sim|
|Retenção de armazenamento de backup|7 dias|7 dias|7 dias|7 dias|7 dias|7 dias|
|||

### <a name="gen4-compute-generation-part-2"></a>Geração de computação Gen4 (parte 2)

|Nível de desempenho|HS_Gen4_7|HS_Gen4_8|HS_Gen4_9|HS_Gen4_10|HS_Gen4_16|HS_Gen4_24|
|:--- | ---: |--: |--: | --: |--: |--: |
|Geração de computação|Gen4|Gen4|Gen4|Gen4|Gen4|Gen4|
|vCores|7|8|9|10|16|24|
|Memória (GB)|49|56|63|70|112|159,5|
|[RBPEX](sql-database-service-tier-hyperscale.md#compute) Tamanho|3X de memória|3X de memória|3X de memória|3X de memória|3X de memória|3X de memória|
|Suporte a Columnstore|Sim|Sim|Sim|Sim|Sim|Sim|
|Armazenamento OLTP na memória (GB)|N/A|N/A|N/A|N/A|N/A|N/A|
|Tamanho máximo de dados (TB)|100 |100 |100 |100 |100 |100 |
|Tamanho máximo do log (TB)|1 |1 |1 |1 |1 |1 |
|Tamanho máximo de dados de TempDB (GB)|224|256|288|320|512|768|
|Tipo de armazenamento| [Observação 1](#notes) |[Observação 1](#notes) |[Observação 1](#notes) |[Observação 1](#notes) |[Observação 1](#notes) |[Observação 1](#notes) |
|IOPS de dados máximo *|[Observação 2](#notes)|[Observação 2](#notes)|[Observação 2](#notes)|[Observação 2](#notes)|[Observação 2](#notes)|[Observação 2](#notes)|
|Latência de e/s (aproximada)|[Observação 3](#notes)|[Observação 3](#notes)|[Observação 3](#notes)|[Observação 3](#notes)|[Observação 3](#notes)|[Observação 3](#notes)|
|Máximo de trabalhos simultâneos (solicitações)|1400|1600|1800|2000|3200|4800|
|Sessões simultâneas máx.|30,000|30,000|30,000|30,000|30,000|30,000|
|Réplicas secundárias|0-4|0-4|0-4|0-4|0-4|0-4|
|Multi-AZ|N/A|N/A|N/A|N/A|N/A|N/A|
|Escalamento Horizontal de Leituras|Sim|Sim|Sim|Sim|Sim|Sim|
|Retenção de armazenamento de backup|7 dias|7 dias|7 dias|7 dias|7 dias|7 dias|
|||

\* o valor máximo para tamanhos de e/s variando entre 8 KB e 64 KB. IOPS reais são dependentes da carga de trabalho. Para obter detalhes, consulte [governança de e/s de dados](sql-database-resource-limits-database-server.md#resource-governance).

## <a name="hyperscale---provisioned-compute---gen5"></a>Hiperescala – computação provisionada-Gen5

### <a name="gen5-compute-generation-part-1"></a>Geração de computação Gen5 (parte 1)

|Nível de desempenho|HS_Gen5_2|HS_Gen5_4|HS_Gen5_6|HS_Gen_8|HS_Gen5_10|HS_Gen5_12|HS_Gen5_14|
|:--- | --: |--: |--: |--: |---: | --: |--: |--: |
|Geração de computação|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|
|vCores|2|4|6|8|10|12|14|
|Memória (GB)|10,4|20,8|31,1|41,5|51,9|62,3|72,7|
|[RBPEX](sql-database-service-tier-hyperscale.md#compute) Tamanho|3X de memória|3X de memória|3X de memória|3X de memória|3X de memória|3X de memória|3X de memória|
|Suporte a Columnstore|Sim|Sim|Sim|Sim|Sim|Sim|Sim|
|Armazenamento OLTP na memória (GB)|N/A|N/A|N/A|N/A|N/A|N/A|N/A|
|Tamanho máximo de dados (TB)|100 |100 |100 |100 |100 |100 |100|
|Tamanho máximo do log (TB)|1 |1 |1 |1 |1 |1 |1 |
|Tamanho máximo de dados de TempDB (GB)|64|128|192|256|320|384|448|
|Tipo de armazenamento| [Observação 1](#notes) |[Observação 1](#notes)|[Observação 1](#notes) |[Observação 1](#notes) |[Observação 1](#notes) |[Observação 1](#notes) |[Observação 1](#notes) |
|IOPS de dados máximo *|[Observação 2](#notes)|[Observação 2](#notes)|[Observação 2](#notes)|[Observação 2](#notes)|[Observação 2](#notes)|[Observação 2](#notes)|[Observação 2](#notes)|
|Latência de e/s (aproximada)|[Observação 3](#notes)|[Observação 3](#notes)|[Observação 3](#notes)|[Observação 3](#notes)|[Observação 3](#notes)|[Observação 3](#notes)|[Observação 3](#notes)|
|Máximo de trabalhos simultâneos (solicitações)|200|400|600|800|1000|1200|1400|
|Sessões simultâneas máx.|30,000|30,000|30,000|30,000|30,000|30,000|30,000|
|Réplicas secundárias|0-4|0-4|0-4|0-4|0-4|0-4|0-4|
|Multi-AZ|N/A|N/A|N/A|N/A|N/A|N/A|N/A|
|Escalamento Horizontal de Leituras|Sim|Sim|Sim|Sim|Sim|Sim|Sim|
|Retenção de armazenamento de backup|7 dias|7 dias|7 dias|7 dias|7 dias|7 dias|7 dias|
|||

\* o valor máximo para tamanhos de e/s variando entre 8 KB e 64 KB. IOPS reais são dependentes da carga de trabalho. Para obter detalhes, consulte [governança de e/s de dados](sql-database-resource-limits-database-server.md#resource-governance).

### <a name="gen5-compute-generation-part-2"></a>Geração de computação Gen5 (parte 2)

|Nível de desempenho|HS_Gen5_16|HS_Gen5_18|HS_Gen5_20|HS_Gen_24|HS_Gen5_32|HS_Gen5_40|HS_Gen5_80|
|:--- | --: |--: |--: |--: |---: |--: |--: |
|Geração de computação|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|
|vCores|16|18|20|24|32|40|80|
|Memória (GB)|83|93,4|103,8|124,6|166,1|207,6|415,2|
|[RBPEX](sql-database-service-tier-hyperscale.md#compute) Tamanho|3X de memória|3X de memória|3X de memória|3X de memória|3X de memória|3X de memória|3X de memória|
|Suporte a Columnstore|Sim|Sim|Sim|Sim|Sim|Sim|Sim|
|Armazenamento OLTP na memória (GB)|N/A|N/A|N/A|N/A|N/A|N/A|N/A|
|Tamanho máximo de dados (TB)|100 |100 |100 |100 |100 |100 |100 |
|Tamanho máximo do log (TB)|1 |1 |1 |1 |1 |1 |1 |
|Tamanho máximo de dados de TempDB (GB)|512|576|640|768|1024|1280|2560|
|Tipo de armazenamento| [Observação 1](#notes) |[Observação 1](#notes)|[Observação 1](#notes)|[Observação 1](#notes) |[Observação 1](#notes) |[Observação 1](#notes) |[Observação 1](#notes) |
|IOPS de dados máximo *|[Observação 2](#notes)|[Observação 2](#notes)|[Observação 2](#notes)|[Observação 2](#notes)|[Observação 2](#notes)|[Observação 2](#notes)|[Observação 2](#notes)|
|Latência de e/s (aproximada)|[Observação 3](#notes)|[Observação 3](#notes)|[Observação 3](#notes)|[Observação 3](#notes)|[Observação 3](#notes)|[Observação 3](#notes)|[Observação 3](#notes)|
|Máximo de trabalhos simultâneos (solicitações)|1600|1800|2000|2400|3200|4000|8000|
|Sessões simultâneas máx.|30,000|30,000|30,000|30,000|30,000|30,000|30,000|
|Réplicas secundárias|0-4|0-4|0-4|0-4|0-4|0-4|0-4|
|Multi-AZ|N/A|N/A|N/A|N/A|N/A|N/A|N/A|
|Escalamento Horizontal de Leituras|Sim|Sim|Sim|Sim|Sim|Sim|Sim|
|Retenção de armazenamento de backup|7 dias|7 dias|7 dias|7 dias|7 dias|7 dias|7 dias|
|||

\* o valor máximo para tamanhos de e/s variando entre 8 KB e 64 KB. IOPS reais são dependentes da carga de trabalho. Para obter detalhes, consulte [governança de e/s de dados](sql-database-resource-limits-database-server.md#resource-governance).

#### <a name="notes"></a>Notas

**Observação 1**: o hiperescala é uma arquitetura de várias camadas com componentes de armazenamento e computação separados: [arquitetura de camada de serviço de hiperescala](sql-database-service-tier-hyperscale.md#distributed-functions-architecture)

**Observação 2**: a arquitetura em várias camadas de hiperescala tem cache em vários níveis. O IOPS efetivo dependerá da carga de trabalho.

**Observação 3**: a latência é 1-2 ms para dados no cache baseado em SSD RBPEX em réplicas de computação, que armazena em cache as páginas de dados mais usadas. Maior latência de dados recuperados de servidores de páginas.

## <a name="general-purpose---provisioned-compute---gen4"></a>Computação provisionada de uso geral-Gen4

> [!IMPORTANT]
> Novos bancos de dados Gen4 não têm mais suporte nas regiões leste da Austrália ou sul do Brasil.

### <a name="gen4-compute-generation-part-1"></a>Geração de computação Gen4 (parte 1)

|Tamanho da computação|GP_Gen4_1|GP_Gen4_2|GP_Gen4_3|GP_Gen4_4|GP_Gen4_5|GP_Gen4_6
|:--- | --: |--: |--: |--: |--: |--: |
|Geração de computação|Gen4|Gen4|Gen4|Gen4|Gen4|Gen4|
|vCores|1|2|3|4|5|6|
|Memória (GB)|7|14|21|28|35|42|
|Suporte a Columnstore|Sim|Sim|Sim|Sim|Sim|Sim|
|Armazenamento OLTP na memória (GB)|N/A|N/A|N/A|N/A|N/A|N/A|
|Tamanho máximo de dados (GB)|1024|1024|1536|1536|1536|3072|
|Tamanho máximo do log (GB)|307|307|461|461|461|922|
|Tamanho máximo de dados de TempDB (GB)|32|64|96|128|160|192|
|Tipo de armazenamento|SSD remoto|SSD remoto|SSD remoto|SSD remoto|SSD remoto|SSD remoto|
|Latência de e/s (aproximada)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|
|IOPS de dados máximo *|320|640|960|1280|1600|1920|
|Taxa máxima de logs (MBps)|3.75|7.5|11.25|15|18,75|22.5|
|Máximo de trabalhos simultâneos (solicitações)|200|400|600|800|1000|1200|
|Sessões simultâneas máx.|30,000|30,000|30,000|30,000|30,000|30,000|
|Número de réplicas|1|1|1|1|1|1|
|Multi-AZ|N/A|N/A|N/A|N/A|N/A|N/A|
|Escalamento Horizontal de Leituras|N/A|N/A|N/A|N/A|N/A|N/A|
|Armazenamento de backup incluído|tamanho do banco de BD 1X|tamanho do banco de BD 1X|tamanho do banco de BD 1X|tamanho do banco de BD 1X|tamanho do banco de BD 1X|tamanho do banco de BD 1X|

\* o valor máximo para tamanhos de e/s variando entre 8 KB e 64 KB. IOPS reais são dependentes da carga de trabalho. Para obter detalhes, consulte [governança de e/s de dados](sql-database-resource-limits-database-server.md#resource-governance).

### <a name="gen4-compute-generation-part-2"></a>Geração de computação Gen4 (parte 2)

|Tamanho da computação|GP_Gen4_7|GP_Gen4_8|GP_Gen4_9|GP_Gen4_10|GP_Gen4_16|GP_Gen4_24
|:--- | --: |--: |--: |--: |--: |--: |
|Geração de computação|Gen4|Gen4|Gen4|Gen4|Gen4|Gen4|
|vCores|7|8|9|10|16|24|
|Memória (GB)|49|56|63|70|112|159,5|
|Suporte a Columnstore|Sim|Sim|Sim|Sim|Sim|Sim|
|Armazenamento OLTP na memória (GB)|N/A|N/A|N/A|N/A|N/A|N/A|
|Tamanho máximo de dados (GB)|3072|3072|3072|3072|4096|4096|
|Tamanho máximo do log (GB)|922|922|922|922|1229|1229|
|Tamanho máximo de dados de TempDB (GB)|224|256|288|320|512|768|
|Tipo de armazenamento|SSD remoto|SSD remoto|SSD remoto|SSD remoto|SSD remoto|SSD remoto|
|Latência de e/s (aproximada)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)
|IOPS de dados máximo *|2240|2560|2880|3200|5120|7680|
|Taxa máxima de logs (MBps)|26,3|30|30|30|30|30|
|Máximo de trabalhos simultâneos (solicitações)|1400|1600|1800|2000|3200|4800|
|Sessões simultâneas máx.|30,000|30,000|30,000|30,000|30,000|30,000|
|Número de réplicas|1|1|1|1|1|1|
|Multi-AZ|N/A|N/A|N/A|N/A|N/A|N/A|
|Escalamento Horizontal de Leituras|N/A|N/A|N/A|N/A|N/A|N/A|
|Armazenamento de backup incluído|tamanho do banco de BD 1X|tamanho do banco de BD 1X|tamanho do banco de BD 1X|tamanho do banco de BD 1X|tamanho do banco de BD 1X|tamanho do banco de BD 1X|

\* o valor máximo para tamanhos de e/s variando entre 8 KB e 64 KB. IOPS reais são dependentes da carga de trabalho. Para obter detalhes, consulte [governança de e/s de dados](sql-database-resource-limits-database-server.md#resource-governance).

## <a name="general-purpose---provisioned-compute---gen5"></a>Computação provisionada de uso geral-Gen5

### <a name="gen5-compute-generation-part-1"></a>Geração de computação Gen5 (parte 1)

|Tamanho da computação|GP_Gen5_2|GP_Gen5_4|GP_Gen5_6|GP_Gen5_8|GP_Gen5_10|GP_Gen5_12|GP_Gen5_14|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|Geração de computação|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|
|vCores|2|4|6|8|10|12|14|
|Memória (GB)|10,4|20,8|31,1|41,5|51,9|62,3|72,7|
|Suporte a Columnstore|Sim|Sim|Sim|Sim|Sim|Sim|Sim|
|Armazenamento OLTP na memória (GB)|N/A|N/A|N/A|N/A|N/A|N/A|N/A|
|Tamanho máximo de dados (GB)|1024|1024|1536|1536|1536|3072|3072|
|Tamanho máximo do log (GB)|307|307|461|461|461|922|922|
|Tamanho máximo de dados de TempDB (GB)|64|128|192|256|320|384|384|
|Tipo de armazenamento|SSD remoto|SSD remoto|SSD remoto|SSD remoto|SSD remoto|SSD remoto|SSD remoto|
|Latência de e/s (aproximada)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|
|IOPS de dados máximo *|640|1280|1920|2560|3200|3840|4480|
|Taxa máxima de logs (MBps)|7.5|15|22.5|30|30|30|30|
|Máximo de trabalhos simultâneos (solicitações)|200|400|600|800|1000|1200|1400|
|Sessões simultâneas máx.|30,000|30,000|30,000|30,000|30,000|30,000|30,000|
|Número de réplicas|1|1|1|1|1|1|1|
|Multi-AZ|N/A|N/A|N/A|N/A|N/A|N/A|N/A|
|Escalamento Horizontal de Leituras|N/A|N/A|N/A|N/A|N/A|N/A|N/A|
|Armazenamento de backup incluído|tamanho do banco de BD 1X|tamanho do banco de BD 1X|tamanho do banco de BD 1X|tamanho do banco de BD 1X|tamanho do banco de BD 1X|tamanho do banco de BD 1X|tamanho do banco de BD 1X|

\* o valor máximo para tamanhos de e/s variando entre 8 KB e 64 KB. IOPS reais são dependentes da carga de trabalho. Para obter detalhes, consulte [governança de e/s de dados](sql-database-resource-limits-database-server.md#resource-governance).

### <a name="gen5-compute-generation-part-2"></a>Geração de computação Gen5 (parte 2)

|Tamanho da computação|GP_Gen5_16|GP_Gen5_18|GP_Gen5_20|GP_Gen5_24|GP_Gen5_32|GP_Gen5_40|GP_Gen5_80|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|Geração de computação|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|
|vCores|16|18|20|24|32|40|80|
|Memória (GB)|83|93,4|103,8|124,6|166,1|207,6|415,2|
|Suporte a Columnstore|Sim|Sim|Sim|Sim|Sim|Sim|Sim|
|Armazenamento OLTP na memória (GB)|N/A|N/A|N/A|N/A|N/A|N/A|N/A|
|Tamanho máximo de dados (GB)|3072|3072|3072|4096|4096|4096|4096|
|Tamanho máximo do log (GB)|922|922|922|1229|1229|1229|1229|
|Tamanho máximo de dados de TempDB (GB)|512|576|640|768|1024|1280|2560|
|Tipo de armazenamento|SSD remoto|SSD remoto|SSD remoto|SSD remoto|SSD remoto|SSD remoto|SSD remoto|
|Latência de e/s (aproximada)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|
|IOPS de dados máximo *|5120|5760|6400|7680|10240|12800|25600|
|Taxa máxima de logs (MBps)|30|30|30|30|30|30|30|
|Máximo de trabalhos simultâneos (solicitações)|1600|1800|2000|2400|3200|4000|8000|
|Sessões simultâneas máx.|30,000|30,000|30,000|30,000|30,000|30,000|30,000|
|Número de réplicas|1|1|1|1|1|1|1|
|Multi-AZ|N/A|N/A|N/A|N/A|N/A|N/A|N/A|
|Escalamento Horizontal de Leituras|N/A|N/A|N/A|N/A|N/A|N/A|N/A|
|Armazenamento de backup incluído|tamanho do banco de BD 1X|tamanho do banco de BD 1X|tamanho do banco de BD 1X|tamanho do banco de BD 1X|tamanho do banco de BD 1X|tamanho do banco de BD 1X|tamanho do banco de BD 1X|

\* o valor máximo para tamanhos de e/s variando entre 8 KB e 64 KB. IOPS reais são dependentes da carga de trabalho. Para obter detalhes, consulte [governança de e/s de dados](sql-database-resource-limits-database-server.md#resource-governance).

## <a name="general-purpose---provisioned-compute---fsv2-series"></a>Computação provisionada de uso geral-série Fsv2

### <a name="fsv2-series-compute-generation-preview"></a>Geração de computação da série Fsv2 (visualização)

|Tamanho da computação|GP_Fsv2_72|
|:--- | --: |
|Geração de computação|Série Fsv2|
|vCores|72|
|Memória (GB)|136,2|
|Suporte a Columnstore|Sim|
|Armazenamento OLTP na memória (GB)|N/A|
|Tamanho máximo de dados (GB)|4096|
|Tamanho máximo do log (GB)|1024|
|Tamanho máximo de dados de TempDB (GB)|333|
|Tipo de armazenamento|SSD remoto|
|Latência de e/s (aproximada)|5-7 ms (gravação)<br>5-10 ms (leitura)|
|IOPS de dados máximo *|12,800|
|Taxa máxima de logs (MBps)|30|
|Máximo de trabalhos simultâneos (solicitações)|3600|
|Inícios de sessão simultâneos máx.|3600|
|Sessões simultâneas máx.|30,000|
|Número de réplicas|1|
|Multi-AZ|N/A|
|Escalamento Horizontal de Leituras|N/A|
|Armazenamento de backup incluído|tamanho do banco de BD 1X|

\* o valor máximo para tamanhos de e/s variando entre 8 KB e 64 KB. IOPS reais são dependentes da carga de trabalho. Para obter detalhes, consulte [governança de e/s de dados](sql-database-resource-limits-database-server.md#resource-governance).

## <a name="business-critical---provisioned-compute---gen4"></a>Comercialmente crítico-computação provisionada-Gen4

> [!IMPORTANT]
> Novos bancos de dados Gen4 não têm mais suporte nas regiões leste da Austrália ou sul do Brasil.

### <a name="gen4-compute-generation-part-1"></a>Geração de computação Gen4 (parte 1)

|Tamanho da computação|BC_Gen4_1|BC_Gen4_2|BC_Gen4_3|BC_Gen4_4|BC_Gen4_5|BC_Gen4_6|
|:--- | --: |--: |--: |--: |--: |--: |
|Geração de computação|Gen4|Gen4|Gen4|Gen4|Gen4|Gen4|
|vCores|1|2|3|4|5|6|
|Memória (GB)|7|14|21|28|35|42|
|Suporte a Columnstore|Sim|Sim|Sim|Sim|Sim|Sim|
|Armazenamento OLTP na memória (GB)|1|2|3|4|5|6|
|Tipo de armazenamento|SSD local|SSD local|SSD local|SSD local|SSD local|SSD local|
|Tamanho máximo de dados (GB)|1024|1024|1024|1024|1024|1024|
|Tamanho máximo do log (GB)|307|307|307|307|307|307|
|Tamanho máximo de dados de TempDB (GB)|32|64|96|128|160|192|
|Latência de e/s (aproximada)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|
|IOPS de dados máximo *|4,000|8,000|12.000|16,000|20,000|24,000|
|Taxa máxima de logs (MBps)|8|16|24|32|40|48|
|Máximo de trabalhos simultâneos (solicitações)|200|400|600|800|1000|1200|
|Inícios de sessão simultâneos máx.|200|400|600|800|1000|1200|
|Sessões simultâneas máx.|30,000|30,000|30,000|30,000|30,000|30,000|
|Número de réplicas|4|4|4|4|4|4|
|Multi-AZ|Sim|Sim|Sim|Sim|Sim|Sim|
|Escalamento Horizontal de Leituras|Sim|Sim|Sim|Sim|Sim|Sim|
|Armazenamento de backup incluído|tamanho do banco de BD 1X|tamanho do banco de BD 1X|tamanho do banco de BD 1X|tamanho do banco de BD 1X|tamanho do banco de BD 1X|tamanho do banco de BD 1X|

\* o valor máximo para tamanhos de e/s variando entre 8 KB e 64 KB. IOPS reais são dependentes da carga de trabalho. Para obter detalhes, consulte [governança de e/s de dados](sql-database-resource-limits-database-server.md#resource-governance).

### <a name="gen4-compute-generation-part-2"></a>Geração de computação Gen4 (parte 2)

|Tamanho da computação|BC_Gen4_7|BC_Gen4_8|BC_Gen4_9|BC_Gen4_10|BC_Gen4_16|BC_Gen4_24|
|:--- | --: |--: |--: |--: |--: |--: |
|Geração de computação|Gen4|Gen4|Gen4|Gen4|Gen4|Gen4|
|vCores|7|8|9|10|16|24|
|Memória (GB)|49|56|63|70|112|159,5|
|Suporte a Columnstore|Sim|Sim|Sim|Sim|Sim|Sim|
|Armazenamento OLTP na memória (GB)|7|8|9.5|11|20|36|
|Tipo de armazenamento|SSD local|SSD local|SSD local|SSD local|SSD local|SSD local|
|Tamanho máximo de dados (GB)|1024|1024|1024|1024|1024|1024|
|Tamanho máximo do log (GB)|307|307|307|307|307|307|
|Tamanho máximo de dados de TempDB (GB)|224|256|288|320|512|768|
|Latência de e/s (aproximada)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|
|IOPS de dados máximo |28.000|32,000|36.000|40,000|64,000|76 800|
|Taxa máxima de logs (MBps)|56|64|64|64|64|64|
|Máximo de trabalhos simultâneos (solicitações)|1400|1600|1800|2000|3200|4800|
|Máximo de logons simultâneos (solicitações)|1400|1600|1800|2000|3200|4800|
|Sessões simultâneas máx.|30,000|30,000|30,000|30,000|30,000|30,000|
|Número de réplicas|4|4|4|4|4|4|
|Multi-AZ|Sim|Sim|Sim|Sim|Sim|Sim|
|Escalamento Horizontal de Leituras|Sim|Sim|Sim|Sim|Sim|Sim|
|Armazenamento de backup incluído|tamanho do banco de BD 1X|tamanho do banco de BD 1X|tamanho do banco de BD 1X|tamanho do banco de BD 1X|tamanho do banco de BD 1X|tamanho do banco de BD 1X|

\* o valor máximo para tamanhos de e/s variando entre 8 KB e 64 KB. IOPS reais são dependentes da carga de trabalho. Para obter detalhes, consulte [governança de e/s de dados](sql-database-resource-limits-database-server.md#resource-governance).

## <a name="business-critical---provisioned-compute---gen5"></a>Comercialmente crítico-computação provisionada-Gen5

### <a name="gen5-compute-generation-part-1"></a>Geração de computação Gen5 (parte 1)

|Tamanho da computação|BC_Gen5_2|BC_Gen5_4|BC_Gen5_6|BC_Gen5_8|BC_Gen5_10|BC_Gen5_12|BC_Gen5_14|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|Geração de computação|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|
|vCores|2|4|6|8|10|12|14|
|Memória (GB)|10,4|20,8|31,1|41,5|51,9|62,3|72,7|
|Suporte a Columnstore|Sim|Sim|Sim|Sim|Sim|Sim|Sim|
|Armazenamento OLTP na memória (GB)|1,57|3,14|4,71|6,28|8,65|11, 2|13,39|
|Tamanho máximo de dados (GB)|1024|1024|1536|1536|1536|3072|3072|
|Tamanho máximo do log (GB)|307|307|461|461|461|922|922|
|Tamanho máximo de dados de TempDB (GB)|64|128|192|256|320|384|448|
|Tipo de armazenamento|SSD local|SSD local|SSD local|SSD local|SSD local|SSD local|SSD local|
|Latência de e/s (aproximada)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|
|IOPS de dados máximo *|8000|16,000|24,000|32,000|40,000|48.000|56.000|
|Taxa máxima de logs (MBps)|24|48|72|96|96|96|96|
|Máximo de trabalhos simultâneos (solicitações)|200|400|600|800|1000|1200|1400|
|Inícios de sessão simultâneos máx.|200|400|600|800|1000|1200|1400|
|Sessões simultâneas máx.|30,000|30,000|30,000|30,000|30,000|30,000|30,000|
|Número de réplicas|4|4|4|4|4|4|4|
|Multi-AZ|Sim|Sim|Sim|Sim|Sim|Sim|Sim|
|Escalamento Horizontal de Leituras|Sim|Sim|Sim|Sim|Sim|Sim|Sim|
|Armazenamento de backup incluído|tamanho do banco de BD 1X|tamanho do banco de BD 1X|tamanho do banco de BD 1X|tamanho do banco de BD 1X|tamanho do banco de BD 1X|tamanho do banco de BD 1X|tamanho do banco de BD 1X|

\* o valor máximo para tamanhos de e/s variando entre 8 KB e 64 KB. IOPS reais são dependentes da carga de trabalho. Para obter detalhes, consulte [governança de e/s de dados](sql-database-resource-limits-database-server.md#resource-governance).

### <a name="gen5-compute-generation-part-2"></a>Geração de computação Gen5 (parte 2)

|Tamanho da computação|BC_Gen5_16|BC_Gen5_18|BC_Gen5_20|BC_Gen5_24|BC_Gen5_32|BC_Gen5_40|BC_Gen5_80|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|Geração de computação|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|
|vCores|16|18|20|24|32|40|80|
|Memória (GB)|83|93,4|103,8|124,6|166,1|207,6|415,2|
|Suporte a Columnstore|Sim|Sim|Sim|Sim|Sim|Sim|Sim|
|Armazenamento OLTP na memória (GB)|15,77|18,14|20,51|25,25|37,94|52,23|131,64|
|Tamanho máximo de dados (GB)|3072|3072|3072|4096|4096|4096|4096|
|Tamanho máximo do log (GB)|922|922|922|1229|1229|1229|1229|
|Tamanho máximo de dados de TempDB (GB)|512|576|640|768|1024|1280|2560|
|Tipo de armazenamento|SSD local|SSD local|SSD local|SSD local|SSD local|SSD local|SSD local|
|Latência de e/s (aproximada)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|
|IOPS de dados máximo *|64,000|72.000|80,000|96.000|128.000|160 000|204.800|
|Taxa máxima de logs (MBps)|96|96|96|96|96|96|96|
|Máximo de trabalhos simultâneos (solicitações)|1600|1800|2000|2400|3200|4000|8000|
|Inícios de sessão simultâneos máx.|1600|1800|2000|2400|3200|4000|8000|
|Sessões simultâneas máx.|30,000|30,000|30,000|30,000|30,000|30,000|30,000|
|Número de réplicas|4|4|4|4|4|4|4|
|Multi-AZ|Sim|Sim|Sim|Sim|Sim|Sim|Sim|
|Escalamento Horizontal de Leituras|Sim|Sim|Sim|Sim|Sim|Sim|Sim|
|Armazenamento de backup incluído|tamanho do banco de BD 1X|tamanho do banco de BD 1X|tamanho do banco de BD 1X|tamanho do banco de BD 1X|tamanho do banco de BD 1X|tamanho do banco de BD 1X|tamanho do banco de BD 1X|

\* o valor máximo para tamanhos de e/s variando entre 8 KB e 64 KB. IOPS reais são dependentes da carga de trabalho. Para obter detalhes, consulte [governança de e/s de dados](sql-database-resource-limits-database-server.md#resource-governance).

## <a name="business-critical---provisioned-compute---m-series"></a>Computação comercialmente crítica-série M

### <a name="m-series-compute-generation-preview"></a>Geração de computação da série M (versão prévia)

|Tamanho da computação|BC_M_128|
|:--- | --: |
|Geração de computação|Série M|
|vCores|128|
|Memória (GB)|3767,1|
|Suporte a Columnstore|Sim|
|Armazenamento OLTP na memória (GB)|1768|
|Tamanho máximo de dados (GB)|4096|
|Tamanho máximo do log (GB)|2048|
|Tamanho máximo de dados de TempDB (GB)|4096|
|Tipo de armazenamento|SSD local|
|Latência de e/s (aproximada)|1-2 ms (gravação)<br>1-2 ms (leitura)|
|IOPS de dados máximo *|160 000|
|Taxa máxima de logs (MBps)|264|
|Máximo de trabalhos simultâneos (solicitações)|12,800|
|Inícios de sessão simultâneos máx.|12,800|
|Sessões simultâneas máx.|30000|
|Número de réplicas|4|
|Multi-AZ|Sim|
|Escalamento Horizontal de Leituras|Sim|
|Armazenamento de backup incluído|tamanho do banco de BD 1X|

\* o valor máximo para tamanhos de e/s variando entre 8 KB e 64 KB. IOPS reais são dependentes da carga de trabalho. Para obter detalhes, consulte [governança de e/s de dados](sql-database-resource-limits-database-server.md#resource-governance).

> [!IMPORTANT]
> Em algumas circunstâncias, poderá ter reduzir uma base de dados para recuperar espaço não utilizado. Para obter mais informações, consulte [gerir o espaço de ficheiro na base de dados do Azure SQL](sql-database-file-space-management.md).

## <a name="next-steps"></a>Passos seguintes

- Para os limites de recursos de DTU para um único banco de dados, consulte [limites de recursos para bancos de dados individuais usando o modelo de compra de DTU](sql-database-dtu-resource-limits-single-databases.md)
- Para limites de recursos vCore para pools elásticos, consulte [limites de recursos para pools elásticos usando o modelo de compra vCore](sql-database-vcore-resource-limits-elastic-pools.md)
- Para os limites de recursos de DTU para pools elásticos, consulte [limites de recursos para pools elásticos usando o modelo de compra de DTU](sql-database-dtu-resource-limits-elastic-pools.md)
- Para os limites de recurso para instâncias gerenciadas, consulte [limites de recursos de instância gerenciada](sql-database-managed-instance-resource-limits.md).
- Para obter informações sobre limites gerais do Azure, consulte [assinatura do Azure e limites de serviço, cotas e restrições](../azure-resource-manager/management/azure-subscription-service-limits.md).
- Para obter informações sobre limites de recursos em um servidor de banco de dados, consulte [visão geral dos limites de recursos em um servidor de banco de dados SQL](sql-database-resource-limits-database-server.md) para obter informações sobre os limites nos níveis de servidor e assinatura.
