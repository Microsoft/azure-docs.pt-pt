---
title: Limites de recursos vCore de base de dados individual
description: Esta página descreve alguns limites de recursos vCore comuns para uma única base de dados na Base de Dados Azure SQL.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: reference
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/22/2021
ms.openlocfilehash: a4be96d35116ed40ca61f00ed8f2ddd786760242
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/23/2021
ms.locfileid: "98735247"
---
# <a name="resource-limits-for-single-databases-using-the-vcore-purchasing-model"></a>Limites de recursos das bases de dados individuais com o modelo de compra baseado em vCore
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Este artigo fornece os limites de recursos detalhados para bases de dados individuais na Base de Dados Azure SQL utilizando o modelo de compra vCore.

Para os limites do modelo de compra de DTU para bases de dados individuais num servidor, consulte [a visão geral dos limites de recursos num servidor](resource-limits-logical-server.md).

Pode definir o nível de serviço, o tamanho do cálculo (objetivo de serviço) e o valor de armazenamento para uma única base de dados utilizando o [portal Azure](single-database-manage.md#the-azure-portal), [Transact-SQL,](single-database-manage.md#transact-sql-t-sql) [PowerShell,](single-database-manage.md#powershell) [O CLI Azure,](single-database-manage.md#the-azure-cli)ou a [API REST](single-database-manage.md#rest-api).

> [!IMPORTANT]
> Para obter orientações e considerações de escalonamento, consulte [Escala uma única base de dados](single-database-scale.md).

## <a name="general-purpose---serverless-compute---gen5"></a>Finalidade geral - computação sem servidor - Gen5

O [nível de computação sem servidor](serverless-tier-overview.md) está atualmente disponível apenas no hardware da Gen5.

### <a name="gen5-compute-generation-part-1"></a>Geração de computação Gen5 (parte 1)

|Tamanho do cálculo (objetivo de serviço)|GP_S_Gen5_1|GP_S_Gen5_2|GP_S_Gen5_4|GP_S_Gen5_6|GP_S_Gen5_8|
|:--- | --: |--: |--: |--: |--: |
|Geração computacional|Gen5|Gen5|Gen5|Gen5|Gen5|
|Min-max vCores|0.5-1|0.5-2|0.5-4|0.75-6|1.0-8|
|Memória min-max (GB)|2.02-3|2.05-6|2.10-12|2.25-18|3.00-24|
|Atraso de pausa automática min-max (minutos)|60-10080|60-10080|60-10080|60-10080|60-10080|
|Suporte de loja de colunas|Sim|Sim|Sim|Sim|Sim|
|Armazenamento OLTP na memória (GB)|N/D|N/D|N/D|N/D|N/D|
|Tamanho máximo dos dados (GB)|512|1024|1024|1024|1536|
|Tamanho do tronco máximo (GB)|154|307|307|307|461|
|Tamanho máximo de dados tempDB (GB)|32|64|128|192|256|
|Tipo de armazenamento|SSD remoto|SSD remoto|SSD remoto|SSD remoto|SSD remoto|
|Latência IO (aproximada)|5-7 ms (escrever)<br>5-10 ms (ler)|5-7 ms (escrever)<br>5-10 ms (ler)|5-7 ms (escrever)<br>5-10 ms (ler)|5-7 ms (escrever)<br>5-10 ms (ler)|5-7 ms (escrever)<br>5-10 ms (ler)|
|Dados max IOPS *|320|640|1280|1920|2560|
|Taxa de registo máximo (MBps)|4,5|9|18|27|36|
|Trabalhadores max simultâneos (pedidos)|75|150|300|450|600|
|Sessões simultâneas máx.|30,000|30,000|30,000|30,000|30,000|
|Número de réplicas|1|1|1|1|1|
|Multi-AZ|N/D|N/D|N/D|N/D|N/D|
|Escalamento Horizontal de Leituras|N/D|N/D|N/D|N/D|N/D|
|Armazenamento de backup incluído|Tamanho 1X DB|Tamanho 1X DB|Tamanho 1X DB|Tamanho 1X DB|Tamanho 1X DB|

\* O valor máximo para tamanhos IO varia entre 8 KB e 64 KB. Os IOPS reais dependem da carga de trabalho. Para mais informações, consulte [a Governação do IO de Dados.](resource-limits-logical-server.md#resource-governance)

### <a name="gen5-compute-generation-part-2"></a>Geração de computação Gen5 (parte 2)

|Tamanho do cálculo (objetivo de serviço)|GP_S_Gen5_10|GP_S_Gen5_12|GP_S_Gen5_14|GP_S_Gen5_16|
|:--- | --: |--: |--: |--: |
|Geração computacional|Gen5|Gen5|Gen5|Gen5|
|Min-max vCores|1.25-10|1.50-12|1.75-14|2.00-16|
|Memória min-max (GB)|3.75-30|4.50-36|5.25-42|6.00-48|
|Atraso de pausa automática min-max (minutos)|60-10080|60-10080|60-10080|60-10080|
|Suporte de loja de colunas|Sim|Sim|Sim|Sim|
|Armazenamento OLTP na memória (GB)|N/D|N/D|N/D|N/D|
|Tamanho máximo dos dados (GB)|1536|3072|3072|3072|
|Tamanho do tronco máximo (GB)|461|461|461|922|
|Tamanho máximo de dados tempDB (GB)|320|384|448|512|
|Tipo de armazenamento|SSD remoto|SSD remoto|SSD remoto|SSD remoto|
|Latência IO (aproximada)|5-7 ms (escrever)<br>5-10 ms (ler)|5-7 ms (escrever)<br>5-10 ms (ler)|5-7 ms (escrever)<br>5-10 ms (ler)|5-7 ms (escrever)<br>5-10 ms (ler)|
|Dados max IOPS *|3200|3840|4480|5120|
|Taxa de registo máximo (MBps)|36|36|36|36|
|Trabalhadores max simultâneos (pedidos)|750|900|1050|1200|
|Sessões simultâneas máx.|30,000|30,000|30,000|30,000|
|Número de réplicas|1|1|1|1|
|Multi-AZ|N/D|N/D|N/D|N/D|
|Escalamento Horizontal de Leituras|N/D|N/D|N/D|N/D|
|Armazenamento de backup incluído|Tamanho 1X DB|Tamanho 1X DB|Tamanho 1X DB|Tamanho 1X DB|

\* O valor máximo para tamanhos IO varia entre 8 KB e 64 KB. Os IOPS reais dependem da carga de trabalho. Para mais informações, consulte [a Governação do IO de Dados.](resource-limits-logical-server.md#resource-governance)

### <a name="gen5-compute-generation-part-3"></a>Geração de computação Gen5 (parte 3)

|Tamanho do cálculo (objetivo de serviço)|GP_S_Gen5_18|GP_S_Gen5_20|GP_S_Gen5_24|GP_S_Gen5_32|GP_S_Gen5_40|
|:--- | --: |--: |--: |--: |--:|
|Geração computacional|Gen5|Gen5|Gen5|Gen5|Gen5|
|Min-max vCores|2.25-18|2.5-20|3-24|4-32|5-40|
|Memória min-max (GB)|6.75-54|7.5-60|9-72|12-96|15-120|
|Atraso de pausa automática min-max (minutos)|60-10080|60-10080|60-10080|60-10080|60-10080|
|Suporte de loja de colunas|Sim|Sim|Sim|Sim|Sim|
|Armazenamento OLTP na memória (GB)|N/D|N/D|N/D|N/D|N/D|
|Tamanho máximo dos dados (GB)|3072|3072|4096|4096|4096|
|Tamanho do tronco máximo (GB)|922|922|1024|1024|1024|
|Tamanho máximo de dados tempDB (GB)|576|640|768|1024|1280|
|Tipo de armazenamento|SSD remoto|SSD remoto|SSD remoto|SSD remoto|SSD remoto|
|Latência IO (aproximada)|5-7 ms (escrever)<br>5-10 ms (ler)|5-7 ms (escrever)<br>5-10 ms (ler)|5-7 ms (escrever)<br>5-10 ms (ler)|5-7 ms (escrever)<br>5-10 ms (ler)|5-7 ms (escrever)<br>5-10 ms (ler)|
|Dados max IOPS *|5760|6400|7680|10240|12800|
|Taxa de registo máximo (MBps)|36|36|36|36|36|
|Trabalhadores max simultâneos (pedidos)|1350|1500|1800|2400|3.000|
|Sessões simultâneas máx.|30,000|30,000|30,000|30,000|30,000|
|Número de réplicas|1|1|1|1|1|
|Multi-AZ|N/D|N/D|N/D|N/D|N/D|
|Escalamento Horizontal de Leituras|N/D|N/D|N/D|N/D|N/D|
|Armazenamento de backup incluído|Tamanho 1X DB|Tamanho 1X DB|Tamanho 1X DB|Tamanho 1X DB|Tamanho 1X DB|

\* O valor máximo para tamanhos IO varia entre 8 KB e 64 KB. Os IOPS reais dependem da carga de trabalho. Para mais informações, consulte [a Governação do IO de Dados.](resource-limits-logical-server.md#resource-governance)


## <a name="hyperscale---provisioned-compute---gen4"></a>Hiperescala - computação a provisionada - Gen4

### <a name="gen4-compute-generation-part-1"></a>Geração de computação Gen4 (parte 1)

|Tamanho do cálculo (objetivo de serviço)|HS_Gen4_1|HS_Gen4_2|HS_Gen4_3|HS_Gen4_4|HS_Gen4_5|HS_Gen4_6|
|:--- | --: |--: |--: |---: | --: |--: |
|Geração computacional|Gen4|Gen4|Gen4|Gen4|Gen4|Gen4|
|vCores|1|2|3|4|5|6|
|Memória (GB)|7|14|21|28|35|42|
|[RBPEX](service-tier-hyperscale.md#compute) Tamanho|3X Memória|3X Memória|3X Memória|3X Memória|3X Memória|3X Memória|
|Suporte de loja de colunas|Sim|Sim|Sim|Sim|Sim|Sim|
|Armazenamento OLTP na memória (GB)|N/D|N/D|N/D|N/D|N/D|N/D|
|Tamanho máximo dos dados (TB)|100 |100 |100 |100 |100 |100|
|Tamanho do tronco máximo (TB)|Ilimitado |Ilimitado |Ilimitado |Ilimitado |Ilimitado |Ilimitado |
|Tamanho máximo de dados tempDB (GB)|32|64|96|128|160|192|
|Tipo de armazenamento| [Nota 1](#notes) |[Nota 1](#notes)|[Nota 1](#notes) |[Nota 1](#notes) |[Nota 1](#notes) |[Nota 1](#notes) |
|Max local SSD IOPS *|4000 |8000 |12000 |16000 |20 000 |24000 |
|Taxa de registo máximo (MBps)|100 |100 |100 |100 |100 |100 |
|Latência IO (aproximada)|[Nota 2](#notes)|[Nota 2](#notes)|[Nota 2](#notes)|[Nota 2](#notes)|[Nota 2](#notes)|[Nota 2](#notes)|
|Trabalhadores max simultâneos (pedidos)|200|400|600|800|1000|1200|
|Sessões simultâneas máx.|30,000|30,000|30,000|30,000|30,000|30,000|
|Réplicas secundárias|0-4|0-4|0-4|0-4|0-4|0-4|
|Multi-AZ|N/D|N/D|N/D|N/D|N/D|N/D|
|Escalamento Horizontal de Leituras|Sim|Sim|Sim|Sim|Sim|Sim|
|Retenção de armazenamento de backup|7 dias|7 dias|7 dias|7 dias|7 dias|7 dias|
|||

\* Além do SSD IO local, as cargas de trabalho utilizarão [iO do servidor](service-tier-hyperscale.md#page-server) de página remota. O IOPS eficaz dependerá da carga de trabalho. Para mais informações, consulte [a Governação do IO de Dados](resource-limits-logical-server.md#resource-governance)e [a IO de Dados nas estatísticas de utilização de recursos](hyperscale-performance-diagnostics.md#data-io-in-resource-utilization-statistics).

### <a name="gen4-compute-generation-part-2"></a>Geração de computação Gen4 (parte 2)

|Tamanho do cálculo (objetivo de serviço)|HS_Gen4_7|HS_Gen4_8|HS_Gen4_9|HS_Gen4_10|HS_Gen4_16|HS_Gen4_24|
|:--- | ---: |--: |--: | --: |--: |--: |
|Geração computacional|Gen4|Gen4|Gen4|Gen4|Gen4|Gen4|
|vCores|7|8|9|10|16|24|
|Memória (GB)|49|56|63|70|112|159.5|
|[RBPEX](service-tier-hyperscale.md#compute) Tamanho|3X Memória|3X Memória|3X Memória|3X Memória|3X Memória|3X Memória|
|Suporte de loja de colunas|Sim|Sim|Sim|Sim|Sim|Sim|
|Armazenamento OLTP na memória (GB)|N/D|N/D|N/D|N/D|N/D|N/D|
|Tamanho máximo dos dados (TB)|100 |100 |100 |100 |100 |100 |
|Tamanho do tronco máximo (TB)|Ilimitado |Ilimitado |Ilimitado |Ilimitado |Ilimitado |Ilimitado |
|Tamanho máximo de dados tempDB (GB)|224|256|288|320|512|768|
|Tipo de armazenamento| [Nota 1](#notes) |[Nota 1](#notes) |[Nota 1](#notes) |[Nota 1](#notes) |[Nota 1](#notes) |[Nota 1](#notes) |
|Max local SSD IOPS *|28000 |32000 |36000 |40000 |64000 |76800 |
|Taxa de registo máximo (MBps)|100 |100 |100 |100 |100 |100 |
|Latência IO (aproximada)|[Nota 2](#notes)|[Nota 2](#notes)|[Nota 2](#notes)|[Nota 2](#notes)|[Nota 2](#notes)|[Nota 2](#notes)|
|Trabalhadores max simultâneos (pedidos)|1400|1600|1800|2000|3200|4800|
|Sessões simultâneas máx.|30,000|30,000|30,000|30,000|30,000|30,000|
|Réplicas secundárias|0-4|0-4|0-4|0-4|0-4|0-4|
|Multi-AZ|N/D|N/D|N/D|N/D|N/D|N/D|
|Escalamento Horizontal de Leituras|Sim|Sim|Sim|Sim|Sim|Sim|
|Retenção de armazenamento de backup|7 dias|7 dias|7 dias|7 dias|7 dias|7 dias|
|||

\* Além do SSD IO local, as cargas de trabalho utilizarão [iO do servidor](service-tier-hyperscale.md#page-server) de página remota. O IOPS eficaz dependerá da carga de trabalho. Para mais informações, consulte [a Governação do IO de Dados](resource-limits-logical-server.md#resource-governance)e [a IO de Dados nas estatísticas de utilização de recursos](hyperscale-performance-diagnostics.md#data-io-in-resource-utilization-statistics).

## <a name="hyperscale---provisioned-compute---gen5"></a>Hiperescala - computação a provisionada - Gen5

### <a name="gen5-compute-generation-part-1"></a>Geração de computação Gen5 (parte 1)

|Tamanho do cálculo (objetivo de serviço)|HS_Gen5_2|HS_Gen5_4|HS_Gen5_6|HS_Gen_8|HS_Gen5_10|HS_Gen5_12|HS_Gen5_14|
|:--- | --: |--: |--: |--: |---: | --: |--: |--: |
|Geração computacional|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|
|vCores|2|4|6|8|10|12|14|
|Memória (GB)|10.4|20.8|31.1|41.5|51.9|62.3|72.7|
|[RBPEX](service-tier-hyperscale.md#compute) Tamanho|3X Memória|3X Memória|3X Memória|3X Memória|3X Memória|3X Memória|3X Memória|
|Suporte de loja de colunas|Sim|Sim|Sim|Sim|Sim|Sim|Sim|
|Armazenamento OLTP na memória (GB)|N/D|N/D|N/D|N/D|N/D|N/D|N/D|
|Tamanho máximo dos dados (TB)|100 |100 |100 |100 |100 |100 |100|
|Tamanho do tronco máximo (TB)|Ilimitado |Ilimitado |Ilimitado |Ilimitado |Ilimitado |Ilimitado |Ilimitado |
|Tamanho máximo de dados tempDB (GB)|64|128|192|256|320|384|448|
|Tipo de armazenamento| [Nota 1](#notes) |[Nota 1](#notes)|[Nota 1](#notes) |[Nota 1](#notes) |[Nota 1](#notes) |[Nota 1](#notes) |[Nota 1](#notes) |
|Max local SSD IOPS *|8000 |16000 |24000 |32000 |40000 |48000 |56000 |
|Taxa de registo máximo (MBps)|100 |100 |100 |100 |100 |100 |100 |
|Latência IO (aproximada)|[Nota 2](#notes)|[Nota 2](#notes)|[Nota 2](#notes)|[Nota 2](#notes)|[Nota 2](#notes)|[Nota 2](#notes)|[Nota 2](#notes)|
|Trabalhadores max simultâneos (pedidos)|200|400|600|800|1000|1200|1400|
|Sessões simultâneas máx.|30,000|30,000|30,000|30,000|30,000|30,000|30,000|
|Réplicas secundárias|0-4|0-4|0-4|0-4|0-4|0-4|0-4|
|Multi-AZ|N/D|N/D|N/D|N/D|N/D|N/D|N/D|
|Escalamento Horizontal de Leituras|Sim|Sim|Sim|Sim|Sim|Sim|Sim|
|Retenção de armazenamento de backup|7 dias|7 dias|7 dias|7 dias|7 dias|7 dias|7 dias|
|||

\* Além do SSD IO local, as cargas de trabalho utilizarão [iO do servidor](service-tier-hyperscale.md#page-server) de página remota. O IOPS eficaz dependerá da carga de trabalho. Para mais informações, consulte [a Governação do IO de Dados](resource-limits-logical-server.md#resource-governance)e [a IO de Dados nas estatísticas de utilização de recursos](hyperscale-performance-diagnostics.md#data-io-in-resource-utilization-statistics).

### <a name="gen5-compute-generation-part-2"></a>Geração de computação Gen5 (parte 2)

|Tamanho do cálculo (objetivo de serviço)|HS_Gen5_16|HS_Gen5_18|HS_Gen5_20|HS_Gen_24|HS_Gen5_32|HS_Gen5_40|HS_Gen5_80|
|:--- | --: |--: |--: |--: |---: |--: |--: |
|Geração computacional|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|
|vCores|16|18|20|24|32|40|80|
|Memória (GB)|83|93,4|103,8|124.6|166.1|207.6|415.2|
|[RBPEX](service-tier-hyperscale.md#compute) Tamanho|3X Memória|3X Memória|3X Memória|3X Memória|3X Memória|3X Memória|3X Memória|
|Suporte de loja de colunas|Sim|Sim|Sim|Sim|Sim|Sim|Sim|
|Armazenamento OLTP na memória (GB)|N/D|N/D|N/D|N/D|N/D|N/D|N/D|
|Tamanho máximo dos dados (TB)|100 |100 |100 |100 |100 |100 |100 |
|Tamanho do tronco máximo (TB)|Ilimitado |Ilimitado |Ilimitado |Ilimitado |Ilimitado |Ilimitado |Ilimitado |
|Tamanho máximo de dados tempDB (GB)|512|576|640|768|1024|1280|2560|
|Tipo de armazenamento| [Nota 1](#notes) |[Nota 1](#notes)|[Nota 1](#notes)|[Nota 1](#notes) |[Nota 1](#notes) |[Nota 1](#notes) |[Nota 1](#notes) |
|Max local SSD IOPS *|64000 |72000 |80000 |96000 |160000 |192000 |204800 |
|Taxa de registo máximo (MBps)|100 |100 |100 |100 |100 |100 |100 |
|Latência IO (aproximada)|[Nota 2](#notes)|[Nota 2](#notes)|[Nota 2](#notes)|[Nota 2](#notes)|[Nota 2](#notes)|[Nota 2](#notes)|[Nota 2](#notes)|
|Trabalhadores max simultâneos (pedidos)|1600|1800|2000|2400|3200|4000|8000|
|Sessões simultâneas máx.|30,000|30,000|30,000|30,000|30,000|30,000|30,000|
|Réplicas secundárias|0-4|0-4|0-4|0-4|0-4|0-4|0-4|
|Multi-AZ|N/D|N/D|N/D|N/D|N/D|N/D|N/D|
|Escalamento Horizontal de Leituras|Sim|Sim|Sim|Sim|Sim|Sim|Sim|
|Retenção de armazenamento de backup|7 dias|7 dias|7 dias|7 dias|7 dias|7 dias|7 dias|
|||

\* Além do SSD IO local, as cargas de trabalho utilizarão [iO do servidor](service-tier-hyperscale.md#page-server) de página remota. O IOPS eficaz dependerá da carga de trabalho. Para mais informações, consulte [a Governação do IO de Dados](resource-limits-logical-server.md#resource-governance)e [a IO de Dados nas estatísticas de utilização de recursos](hyperscale-performance-diagnostics.md#data-io-in-resource-utilization-statistics).

#### <a name="notes"></a>Notas

**Nota 1**: Hyperscale é uma arquitetura multi-camadas com componentes de computação e armazenamento separados: [Hyperscale Service Tier Architecture](service-tier-hyperscale.md#distributed-functions-architecture)

**Nota 2**: A latência é de 1-2 ms para dados sobre a réplica de computação local SSD, que caches mais páginas de dados usadas. Maior latência para dados obtidos a partir de servidores de página.

## <a name="hyperscale---provisioned-compute---dc-series"></a>Hiperescala - cálculo a provisionado - série DC

|Tamanho do cálculo (objetivo de serviço)|HS_DC_2|HS_DC_4|HS_DC_6|HS_DC_8|
|:--- | --: |--: |--: |--: |---: | 
|Geração computacional|Série DC|Série DC|Série DC|Série DC|
|vCores|2|4|6|8|
|Memória (GB)|9|18|27|36|
|[RBPEX](service-tier-hyperscale.md#compute) Tamanho|3X Memória|3X Memória|3X Memória|3X Memória|
|Suporte de loja de colunas|Sim|Sim|Sim|Sim|
|Armazenamento OLTP na memória (GB)|N/D|N/D|N/D|N/D|
|Tamanho máximo dos dados (TB)|100 |100 |100 |100 |
|Tamanho do tronco máximo (TB)|Ilimitado |Ilimitado |Ilimitado |Ilimitado |
|Tamanho máximo de dados tempDB (GB)|64|128|192|256|
|Tipo de armazenamento| [Nota 1](#notes) |[Nota 1](#notes)|[Nota 1](#notes) |[Nota 1](#notes) |
|Max local SSD IOPS *|8000 |16000 |24000 |32000 |
|Taxa de registo máximo (MBps)|100 |100 |100 |100 |
|Latência IO (aproximada)|[Nota 2](#notes)|[Nota 2](#notes)|[Nota 2](#notes)|[Nota 2](#notes)|
|Trabalhadores max simultâneos (pedidos)|160|320|480|640|
|Sessões simultâneas máx.|30,000|30,000|30,000|30,000|
|Réplicas secundárias|0-4|0-4|0-4|0-4|
|Multi-AZ|N/D|N/D|N/D|N/D|
|Escalamento Horizontal de Leituras|Sim|Sim|Sim|Sim|
|Retenção de armazenamento de backup|7 dias|7 dias|7 dias|7 dias|
|||

### <a name="notes"></a>Notas

**Nota 1**: Hyperscale é uma arquitetura multi-camadas com componentes de computação e armazenamento separados: [Hyperscale Service Tier Architecture](service-tier-hyperscale.md#distributed-functions-architecture)

**Nota 2**: A latência é de 1-2 ms para dados sobre a réplica de computação local SSD, que caches mais páginas de dados usadas. Maior latência para dados obtidos a partir de servidores de página.

## <a name="general-purpose---provisioned-compute---gen4"></a>Finalidade geral - computação a provisionada - Gen4

> [!IMPORTANT]
> As novas bases de dados da Gen4 já não são suportadas nas regiões leste ou sul da Austrália.

### <a name="gen4-compute-generation-part-1"></a>Geração de computação Gen4 (parte 1)

|Tamanho do cálculo (objetivo de serviço)|GP_Gen4_1|GP_Gen4_2|GP_Gen4_3|GP_Gen4_4|GP_Gen4_5|GP_Gen4_6
|:--- | --: |--: |--: |--: |--: |--: |
|Geração computacional|Gen4|Gen4|Gen4|Gen4|Gen4|Gen4|
|vCores|1|2|3|4|5|6|
|Memória (GB)|7|14|21|28|35|42|
|Suporte de loja de colunas|Sim|Sim|Sim|Sim|Sim|Sim|
|Armazenamento OLTP na memória (GB)|N/D|N/D|N/D|N/D|N/D|N/D|
|Tamanho máximo dos dados (GB)|1024|1024|1536|1536|1536|3072|
|Tamanho do tronco máximo (GB)|307|307|461|461|461|922|
|Tamanho máximo de dados tempDB (GB)|32|64|96|128|160|192|
|Tipo de armazenamento|SSD remoto|SSD remoto|SSD remoto|SSD remoto|SSD remoto|SSD remoto|
|Latência IO (aproximada)|5-7 ms (escrever)<br>5-10 ms (ler)|5-7 ms (escrever)<br>5-10 ms (ler)|5-7 ms (escrever)<br>5-10 ms (ler)|5-7 ms (escrever)<br>5-10 ms (ler)|5-7 ms (escrever)<br>5-10 ms (ler)|5-7 ms (escrever)<br>5-10 ms (ler)|
|Dados max IOPS *|320|640|960|1280|1600|1920|
|Taxa de registo máximo (MBps)|4,5|9|13,5|18|22,5|27|
|Trabalhadores max simultâneos (pedidos)|200|400|600|800|1000|1200|
|Sessões simultâneas máx.|30,000|30,000|30,000|30,000|30,000|30,000|
|Número de réplicas|1|1|1|1|1|1|
|Multi-AZ|N/D|N/D|N/D|N/D|N/D|N/D|
|Escalamento Horizontal de Leituras|N/D|N/D|N/D|N/D|N/D|N/D|
|Armazenamento de backup incluído|Tamanho 1X DB|Tamanho 1X DB|Tamanho 1X DB|Tamanho 1X DB|Tamanho 1X DB|Tamanho 1X DB|

\* O valor máximo para tamanhos IO varia entre 8 KB e 64 KB. Os IOPS reais dependem da carga de trabalho. Para mais informações, consulte [a Governação do IO de Dados.](resource-limits-logical-server.md#resource-governance)

### <a name="gen4-compute-generation-part-2"></a>Geração de computação Gen4 (parte 2)

|Tamanho do cálculo (objetivo de serviço)|GP_Gen4_7|GP_Gen4_8|GP_Gen4_9|GP_Gen4_10|GP_Gen4_16|GP_Gen4_24
|:--- | --: |--: |--: |--: |--: |--: |
|Geração computacional|Gen4|Gen4|Gen4|Gen4|Gen4|Gen4|
|vCores|7|8|9|10|16|24|
|Memória (GB)|49|56|63|70|112|159.5|
|Suporte de loja de colunas|Sim|Sim|Sim|Sim|Sim|Sim|
|Armazenamento OLTP na memória (GB)|N/D|N/D|N/D|N/D|N/D|N/D|
|Tamanho máximo dos dados (GB)|3072|3072|3072|3072|4096|4096|
|Tamanho do tronco máximo (GB)|922|922|922|922|1229|1229|
|Tamanho máximo de dados tempDB (GB)|224|256|288|320|512|768|
|Tipo de armazenamento|SSD remoto|SSD remoto|SSD remoto|SSD remoto|SSD remoto|SSD remoto|
|Latência IO (aproximada)|5-7 ms (escrever)<br>5-10 ms (ler)|5-7 ms (escrever)<br>5-10 ms (ler)|5-7 ms (escrever)<br>5-10 ms (ler)|5-7 ms (escrever)<br>5-10 ms (ler)|5-7 ms (escrever)<br>5-10 ms (ler)|5-7 ms (escrever)<br>5-10 ms (ler)
|Dados max IOPS *|2240|2560|2880|3200|5120|7680|
|Taxa de registo máximo (MBps)|31,5|36|36|36|36|36|
|Trabalhadores max simultâneos (pedidos)|1400|1600|1800|2000|3200|4800|
|Sessões simultâneas máx.|30,000|30,000|30,000|30,000|30,000|30,000|
|Número de réplicas|1|1|1|1|1|1|
|Multi-AZ|N/D|N/D|N/D|N/D|N/D|N/D|
|Escalamento Horizontal de Leituras|N/D|N/D|N/D|N/D|N/D|N/D|
|Armazenamento de backup incluído|Tamanho 1X DB|Tamanho 1X DB|Tamanho 1X DB|Tamanho 1X DB|Tamanho 1X DB|Tamanho 1X DB|

\* O valor máximo para tamanhos IO varia entre 8 KB e 64 KB. Os IOPS reais dependem da carga de trabalho. Para mais informações, consulte [a Governação do IO de Dados.](resource-limits-logical-server.md#resource-governance)

## <a name="general-purpose---provisioned-compute---gen5"></a>Finalidade geral - computação a provisionada - Gen5

### <a name="gen5-compute-generation-part-1"></a>Geração de computação Gen5 (parte 1)

|Tamanho do cálculo (objetivo de serviço)|GP_Gen5_2|GP_Gen5_4|GP_Gen5_6|GP_Gen5_8|GP_Gen5_10|GP_Gen5_12|GP_Gen5_14|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|Geração computacional|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|
|vCores|2|4|6|8|10|12|14|
|Memória (GB)|10.4|20.8|31.1|41.5|51.9|62.3|72.7|
|Suporte de loja de colunas|Sim|Sim|Sim|Sim|Sim|Sim|Sim|
|Armazenamento OLTP na memória (GB)|N/D|N/D|N/D|N/D|N/D|N/D|N/D|
|Tamanho máximo dos dados (GB)|1024|1024|1536|1536|1536|3072|3072|
|Tamanho do tronco máximo (GB)|307|307|461|461|461|922|922|
|Tamanho máximo de dados tempDB (GB)|64|128|192|256|320|384|384|
|Tipo de armazenamento|SSD remoto|SSD remoto|SSD remoto|SSD remoto|SSD remoto|SSD remoto|SSD remoto|
|Latência IO (aproximada)|5-7 ms (escrever)<br>5-10 ms (ler)|5-7 ms (escrever)<br>5-10 ms (ler)|5-7 ms (escrever)<br>5-10 ms (ler)|5-7 ms (escrever)<br>5-10 ms (ler)|5-7 ms (escrever)<br>5-10 ms (ler)|5-7 ms (escrever)<br>5-10 ms (ler)|5-7 ms (escrever)<br>5-10 ms (ler)|
|Dados max IOPS *|640|1280|1920|2560|3200|3840|4480|
|Taxa de registo máximo (MBps)|9|18|27|36|36|36|36|
|Trabalhadores max simultâneos (pedidos)|200|400|600|800|1000|1200|1400|
|Sessões simultâneas máx.|30,000|30,000|30,000|30,000|30,000|30,000|30,000|
|Número de réplicas|1|1|1|1|1|1|1|
|Multi-AZ|[Disponível em pré-visualização](high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)|[Disponível em pré-visualização](high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)|[Disponível em pré-visualização](high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)|[Disponível em pré-visualização](high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)|[Disponível em pré-visualização](high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)|[Disponível em pré-visualização](high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)|[Disponível em pré-visualização](high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)|
|Escalamento Horizontal de Leituras|N/D|N/D|N/D|N/D|N/D|N/D|N/D|
|Armazenamento de backup incluído|Tamanho 1X DB|Tamanho 1X DB|Tamanho 1X DB|Tamanho 1X DB|Tamanho 1X DB|Tamanho 1X DB|Tamanho 1X DB|

\* O valor máximo para tamanhos IO varia entre 8 KB e 64 KB. Os IOPS reais dependem da carga de trabalho. Para mais informações, consulte [a Governação do IO de Dados.](resource-limits-logical-server.md#resource-governance)

### <a name="gen5-compute-generation-part-2"></a>Geração de computação Gen5 (parte 2)

|Tamanho do cálculo (objetivo de serviço)|GP_Gen5_16|GP_Gen5_18|GP_Gen5_20|GP_Gen5_24|GP_Gen5_32|GP_Gen5_40|GP_Gen5_80|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|Geração computacional|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|
|vCores|16|18|20|24|32|40|80|
|Memória (GB)|83|93,4|103,8|124.6|166.1|207.6|415.2|
|Suporte de loja de colunas|Sim|Sim|Sim|Sim|Sim|Sim|Sim|
|Armazenamento OLTP na memória (GB)|N/D|N/D|N/D|N/D|N/D|N/D|N/D|
|Tamanho máximo dos dados (GB)|3072|3072|3072|4096|4096|4096|4096|
|Tamanho do tronco máximo (GB)|922|922|922|1024|1024|1024|1024|
|Tamanho máximo de dados tempDB (GB)|512|576|640|768|1024|1280|2560|
|Tipo de armazenamento|SSD remoto|SSD remoto|SSD remoto|SSD remoto|SSD remoto|SSD remoto|SSD remoto|
|Latência IO (aproximada)|5-7 ms (escrever)<br>5-10 ms (ler)|5-7 ms (escrever)<br>5-10 ms (ler)|5-7 ms (escrever)<br>5-10 ms (ler)|5-7 ms (escrever)<br>5-10 ms (ler)|5-7 ms (escrever)<br>5-10 ms (ler)|5-7 ms (escrever)<br>5-10 ms (ler)|5-7 ms (escrever)<br>5-10 ms (ler)|
|Dados max IOPS *|5120|5760|6400|7680|10240|12800|12800|
|Taxa de registo máximo (MBps)|36|36|36|36|36|36|36|
|Trabalhadores max simultâneos (pedidos)|1600|1800|2000|2400|3200|4000|8000|
|Sessões simultâneas máx.|30,000|30,000|30,000|30,000|30,000|30,000|30,000|
|Número de réplicas|1|1|1|1|1|1|1|
|Multi-AZ|[Disponível em pré-visualização](high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)|[Disponível em pré-visualização](high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)|[Disponível em pré-visualização](high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)|[Disponível em pré-visualização](high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)|[Disponível em pré-visualização](high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)|[Disponível em pré-visualização](high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)|[Disponível em pré-visualização](high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)|
|Escalamento Horizontal de Leituras|N/D|N/D|N/D|N/D|N/D|N/D|N/D|
|Armazenamento de backup incluído|Tamanho 1X DB|Tamanho 1X DB|Tamanho 1X DB|Tamanho 1X DB|Tamanho 1X DB|Tamanho 1X DB|Tamanho 1X DB|

\* O valor máximo para tamanhos IO varia entre 8 KB e 64 KB. Os IOPS reais dependem da carga de trabalho. Para mais informações, consulte [a Governação do IO de Dados.](resource-limits-logical-server.md#resource-governance)

## <a name="general-purpose---provisioned-compute---fsv2-series"></a>Finalidade geral - cálculo provisionado - Série Fsv2

### <a name="fsv2-series-compute-generation-part-1"></a>Geração de computação da série Fsv2 (parte 1)

|Tamanho do cálculo (objetivo de serviço)|GP_Fsv2_8|GP_Fsv2_10|GP_Fsv2_12|GP_Fsv2_14| GP_Fsv2_16|
|:---| ---:|---:|---:|---:|---:|
|Geração computacional|Série Fsv2|Série Fsv2|Série Fsv2|Série Fsv2|Série Fsv2|
|vCores|8|10|12|14|16|
|Memória (GB)|15.1|18,9|22.7|26,5|30.2|
|Suporte de loja de colunas|Sim|Sim|Sim|Sim|Sim|
|Armazenamento OLTP na memória (GB)|N/D|N/D|N/D|N/D|N/D|
|Tamanho máximo dos dados (GB)|1024|1024|1024|1024|1536|
|Tamanho do tronco máximo (GB)|336|336|336|336|512|
|Tamanho máximo de dados tempDB (GB)|333|333|333|333|333|
|Tipo de armazenamento|SSD remoto|SSD remoto|SSD remoto|SSD remoto|SSD remoto|
|Latência IO (aproximada)|5-7 ms (escrever)<br>5-10 ms (ler)|5-7 ms (escrever)<br>5-10 ms (ler)|5-7 ms (escrever)<br>5-10 ms (ler)|5-7 ms (escrever)<br>5-10 ms (ler)|5-7 ms (escrever)<br>5-10 ms (ler)|
|Dados max IOPS *|2560|3200|3840|4480|5120|
|Taxa de registo máximo (MBps)|36|36|36|36|36|
|Trabalhadores max simultâneos (pedidos)|400|500|600|700|800|
|Inícios de sessão simultâneos máx.|800|1000|1200|1400|1600|
|Sessões simultâneas máx.|30,000|30,000|30,000|30,000|30,000|
|Número de réplicas|1|1|1|1|1|
|Multi-AZ|N/D|N/D|N/D|N/D|N/D|
|Escalamento Horizontal de Leituras|N/D|N/D|N/D|N/D|N/D|
|Armazenamento de backup incluído|Tamanho 1X DB|Tamanho 1X DB|Tamanho 1X DB|Tamanho 1X DB|Tamanho 1X DB|

\* O valor máximo para tamanhos IO varia entre 8 KB e 64 KB. Os IOPS reais dependem da carga de trabalho. Para mais informações, consulte [a Governação do IO de Dados.](resource-limits-logical-server.md#resource-governance)

### <a name="fsv2-series-compute-generation-part-2"></a>Geração de computação da série Fsv2 (parte 2)

|Tamanho do cálculo (objetivo de serviço)|GP_Fsv2_18|GP_Fsv2_20|GP_Fsv2_24|GP_Fsv2_32| GP_Fsv2_36|GP_Fsv2_72|
|:---| ---:|---:|---:|---:|---:|---:|
|Geração computacional|Série Fsv2|Série Fsv2|Série Fsv2|Série Fsv2|Série Fsv2|Série Fsv2|
|vCores|18|20|24|32|36|72|
|Memória (GB)|34,0|37.8|45.4|60.5|68,0|136.0|
|Suporte de loja de colunas|Sim|Sim|Sim|Sim|Sim|Sim|
|Armazenamento OLTP na memória (GB)|N/D|N/D|N/D|N/D|N/D|N/D|
|Tamanho máximo dos dados (GB)|1536|1536|1536|3072|3072|4096|
|Tamanho do tronco máximo (GB)|512|512|512|1024|1024|1024|
|Tamanho máximo de dados tempDB (GB)|83.25|92.5|111|148|166.5|333|
|Tipo de armazenamento|SSD remoto|SSD remoto|SSD remoto|SSD remoto|SSD remoto|SSD remoto|
|Latência IO (aproximada)|5-7 ms (escrever)<br>5-10 ms (ler)|5-7 ms (escrever)<br>5-10 ms (ler)|5-7 ms (escrever)<br>5-10 ms (ler)|5-7 ms (escrever)<br>5-10 ms (ler)|5-7 ms (escrever)<br>5-10 ms (ler)|5-7 ms (escrever)<br>5-10 ms (ler)|
|Dados max IOPS *|5760|6400|7680|10240|11520|12800|
|Taxa de registo máximo (MBps)|36|36|36|36|36|36|
|Trabalhadores max simultâneos (pedidos)|900|1000|1200|1600|1800|3600|
|Inícios de sessão simultâneos máx.|1800|2000|2400|3200|3600|7200|
|Sessões simultâneas máx.|30,000|30,000|30,000|30,000|30,000|30,000|
|Número de réplicas|1|1|1|1|1|1|
|Multi-AZ|N/D|N/D|N/D|N/D|N/D|N/D|
|Escalamento Horizontal de Leituras|N/D|N/D|N/D|N/D|N/D|N/D|
|Armazenamento de backup incluído|Tamanho 1X DB|Tamanho 1X DB|Tamanho 1X DB|Tamanho 1X DB|Tamanho 1X DB|Tamanho 1X DB|

\* O valor máximo para tamanhos IO varia entre 8 KB e 64 KB. Os IOPS reais dependem da carga de trabalho. Para mais informações, consulte [a Governação do IO de Dados.](resource-limits-logical-server.md#resource-governance)

## <a name="general-purpose---provisioned-compute---dc-series"></a>Finalidade geral - cálculo provisionado - séries DC

|Tamanho do cálculo (objetivo de serviço)|GP_DC_2|GP_DC_4|GP_DC_6|GP_DC_8| 
|:---| ---:|---:|---:|---:|
|Geração computacional|Série DC|Série DC|Série DC|Série DC|
|vCores|2|4|6|8|
|Memória (GB)|9|18|27|36|
|Suporte de loja de colunas|Sim|Sim|Sim|Sim|
|Armazenamento OLTP na memória (GB)|N/D|N/D|N/D|N/D|
|Tamanho máximo dos dados (GB)|1024|1536|3072|3072|
|Tamanho do tronco máximo (GB)|307|461|922|922|
|Tamanho máximo de dados tempDB (GB)|64|128|192|256|
|Tipo de armazenamento|SSD remoto|SSD remoto|SSD remoto|SSD remoto|
|Latência IO (aproximada)|5-7 ms (escrever)<br>5-10 ms (ler)|5-7 ms (escrever)<br>5-10 ms (ler)|5-7 ms (escrever)<br>5-10 ms (ler)|5-7 ms (escrever)<br>5-10 ms (ler)|
|Dados max IOPS *|640|1280|1920|2560|
|Taxa de registo máximo (MBps)|9|18|27|36|
|Trabalhadores max simultâneos (pedidos)|160|320|480|640|
|Sessões simultâneas máx.|30,000|30,000|30,000|30,000|
|Número de réplicas|1|1|1|1|
|Multi-AZ|N/D|N/D|N/D|N/D|
|Escalamento Horizontal de Leituras|N/D|N/D|N/D|N/D|
|Armazenamento de backup incluído|Tamanho 1X DB|Tamanho 1X DB|Tamanho 1X DB|Tamanho 1X DB|


\* O valor máximo para tamanhos IO varia entre 8 KB e 64 KB. Os IOPS reais dependem da carga de trabalho. Para mais informações, consulte [a Governação do IO de Dados.](resource-limits-logical-server.md#resource-governance)

## <a name="business-critical---provisioned-compute---gen4"></a>Business critical - provisioned compute - Gen4

> [!IMPORTANT]
> As novas bases de dados da Gen4 já não são suportadas nas regiões leste ou sul da Austrália.

### <a name="gen4-compute-generation-part-1"></a>Geração de computação Gen4 (parte 1)

|Tamanho do cálculo (objetivo de serviço)|BC_Gen4_1|BC_Gen4_2|BC_Gen4_3|BC_Gen4_4|BC_Gen4_5|BC_Gen4_6|
|:--- | --: |--: |--: |--: |--: |--: |
|Geração computacional|Gen4|Gen4|Gen4|Gen4|Gen4|Gen4|
|vCores|1|2|3|4|5|6|
|Memória (GB)|7|14|21|28|35|42|
|Suporte de loja de colunas|Sim|Sim|Sim|Sim|Sim|Sim|
|Armazenamento OLTP na memória (GB)|1|2|3|4|5|6|
|Tipo de armazenamento|Local SSD|Local SSD|Local SSD|Local SSD|Local SSD|Local SSD|
|Tamanho máximo dos dados (GB)|1024|1024|1024|1024|1024|1024|
|Tamanho do tronco máximo (GB)|307|307|307|307|307|307|
|Tamanho máximo de dados tempDB (GB)|32|64|96|128|160|192|
|Latência IO (aproximada)|1-2 ms (escrever)<br>1-2 ms (ler)|1-2 ms (escrever)<br>1-2 ms (ler)|1-2 ms (escrever)<br>1-2 ms (ler)|1-2 ms (escrever)<br>1-2 ms (ler)|1-2 ms (escrever)<br>1-2 ms (ler)|1-2 ms (escrever)<br>1-2 ms (ler)|
|Dados max IOPS *|4000|8,000|12.000|16 000|20 000|24,000|
|Taxa de registo máximo (MBps)|8|16|24|32|40|48|
|Trabalhadores max simultâneos (pedidos)|200|400|600|800|1000|1200|
|Inícios de sessão simultâneos máx.|200|400|600|800|1000|1200|
|Sessões simultâneas máx.|30,000|30,000|30,000|30,000|30,000|30,000|
|Número de réplicas|4|4|4|4|4|4|
|Multi-AZ|Sim|Sim|Sim|Sim|Sim|Sim|
|Escalamento Horizontal de Leituras|Sim|Sim|Sim|Sim|Sim|Sim|
|Armazenamento de backup incluído|Tamanho 1X DB|Tamanho 1X DB|Tamanho 1X DB|Tamanho 1X DB|Tamanho 1X DB|Tamanho 1X DB|

\* O valor máximo para tamanhos IO varia entre 8 KB e 64 KB. Os IOPS reais dependem da carga de trabalho. Para mais informações, consulte [a Governação do IO de Dados.](resource-limits-logical-server.md#resource-governance)

### <a name="gen4-compute-generation-part-2"></a>Geração de computação Gen4 (parte 2)

|Tamanho do cálculo (objetivo de serviço)|BC_Gen4_7|BC_Gen4_8|BC_Gen4_9|BC_Gen4_10|BC_Gen4_16|BC_Gen4_24|
|:--- | --: |--: |--: |--: |--: |--: |
|Geração computacional|Gen4|Gen4|Gen4|Gen4|Gen4|Gen4|
|vCores|7|8|9|10|16|24|
|Memória (GB)|49|56|63|70|112|159.5|
|Suporte de loja de colunas|Sim|Sim|Sim|Sim|Sim|Sim|
|Armazenamento OLTP na memória (GB)|7|8|9.5|11|20|36|
|Tipo de armazenamento|Local SSD|Local SSD|Local SSD|Local SSD|Local SSD|Local SSD|
|Tamanho máximo dos dados (GB)|1024|1024|1024|1024|1024|1024|
|Tamanho do tronco máximo (GB)|307|307|307|307|307|307|
|Tamanho máximo de dados tempDB (GB)|224|256|288|320|512|768|
|Latência IO (aproximada)|1-2 ms (escrever)<br>1-2 ms (ler)|1-2 ms (escrever)<br>1-2 ms (ler)|1-2 ms (escrever)<br>1-2 ms (ler)|1-2 ms (escrever)<br>1-2 ms (ler)|1-2 ms (escrever)<br>1-2 ms (ler)|1-2 ms (escrever)<br>1-2 ms (ler)|
|Dados máximos IOPS |28,000|32,000|36,000|40.000|64,000|76 800|
|Taxa de registo máximo (MBps)|56|64|64|64|64|64|
|Trabalhadores max simultâneos (pedidos)|1400|1600|1800|2000|3200|4800|
|Logins simultâneos max (pedidos)|1400|1600|1800|2000|3200|4800|
|Sessões simultâneas máx.|30,000|30,000|30,000|30,000|30,000|30,000|
|Número de réplicas|4|4|4|4|4|4|
|Multi-AZ|Sim|Sim|Sim|Sim|Sim|Sim|
|Escalamento Horizontal de Leituras|Sim|Sim|Sim|Sim|Sim|Sim|
|Armazenamento de backup incluído|Tamanho 1X DB|Tamanho 1X DB|Tamanho 1X DB|Tamanho 1X DB|Tamanho 1X DB|Tamanho 1X DB|

\* O valor máximo para tamanhos IO varia entre 8 KB e 64 KB. Os IOPS reais dependem da carga de trabalho. Para mais informações, consulte [a Governação do IO de Dados.](resource-limits-logical-server.md#resource-governance)

## <a name="business-critical---provisioned-compute---gen5"></a>Business critical - provisioned compute - Gen5

### <a name="gen5-compute-generation-part-1"></a>Geração de computação Gen5 (parte 1)

|Tamanho do cálculo (objetivo de serviço)|BC_Gen5_2|BC_Gen5_4|BC_Gen5_6|BC_Gen5_8|BC_Gen5_10|BC_Gen5_12|BC_Gen5_14|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|Geração computacional|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|
|vCores|2|4|6|8|10|12|14|
|Memória (GB)|10.4|20.8|31.1|41.5|51.9|62.3|72.7|
|Suporte de loja de colunas|Sim|Sim|Sim|Sim|Sim|Sim|Sim|
|Armazenamento OLTP na memória (GB)|1.57|3.14|4.71|6.28|8.65|11.02|13.39|
|Tamanho máximo dos dados (GB)|1024|1024|1536|1536|1536|3072|3072|
|Tamanho do tronco máximo (GB)|307|307|461|461|461|922|922|
|Tamanho máximo de dados tempDB (GB)|64|128|192|256|320|384|448|
|Tipo de armazenamento|Local SSD|Local SSD|Local SSD|Local SSD|Local SSD|Local SSD|Local SSD|
|Latência IO (aproximada)|1-2 ms (escrever)<br>1-2 ms (ler)|1-2 ms (escrever)<br>1-2 ms (ler)|1-2 ms (escrever)<br>1-2 ms (ler)|1-2 ms (escrever)<br>1-2 ms (ler)|1-2 ms (escrever)<br>1-2 ms (ler)|1-2 ms (escrever)<br>1-2 ms (ler)|1-2 ms (escrever)<br>1-2 ms (ler)|
|Dados max IOPS *|8000|16 000|24,000|32,000|40.000|48,000|56,000|
|Taxa de registo máximo (MBps)|24|48|72|96|96|96|96|
|Trabalhadores max simultâneos (pedidos)|200|400|600|800|1000|1200|1400|
|Inícios de sessão simultâneos máx.|200|400|600|800|1000|1200|1400|
|Sessões simultâneas máx.|30,000|30,000|30,000|30,000|30,000|30,000|30,000|
|Número de réplicas|4|4|4|4|4|4|4|
|Multi-AZ|Sim|Sim|Sim|Sim|Sim|Sim|Sim|
|Escalamento Horizontal de Leituras|Sim|Sim|Sim|Sim|Sim|Sim|Sim|
|Armazenamento de backup incluído|Tamanho 1X DB|Tamanho 1X DB|Tamanho 1X DB|Tamanho 1X DB|Tamanho 1X DB|Tamanho 1X DB|Tamanho 1X DB|

\* O valor máximo para tamanhos IO varia entre 8 KB e 64 KB. Os IOPS reais dependem da carga de trabalho. Para mais informações, consulte [a Governação do IO de Dados.](resource-limits-logical-server.md#resource-governance)

### <a name="gen5-compute-generation-part-2"></a>Geração de computação Gen5 (parte 2)

|Tamanho do cálculo (objetivo de serviço)|BC_Gen5_16|BC_Gen5_18|BC_Gen5_20|BC_Gen5_24|BC_Gen5_32|BC_Gen5_40|BC_Gen5_80|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|Geração computacional|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|
|vCores|16|18|20|24|32|40|80|
|Memória (GB)|83|93,4|103,8|124.6|166.1|207.6|415.2|
|Suporte de loja de colunas|Sim|Sim|Sim|Sim|Sim|Sim|Sim|
|Armazenamento OLTP na memória (GB)|15,77|18.14|20.51|25.25|37.94|52.23|131.64|
|Tamanho máximo dos dados (GB)|3072|3072|3072|4096|4096|4096|4096|
|Tamanho do tronco máximo (GB)|922|922|922|1024|1024|1024|1024|
|Tamanho máximo de dados tempDB (GB)|512|576|640|768|1024|1280|2560|
|Tipo de armazenamento|Local SSD|Local SSD|Local SSD|Local SSD|Local SSD|Local SSD|Local SSD|
|Latência IO (aproximada)|1-2 ms (escrever)<br>1-2 ms (ler)|1-2 ms (escrever)<br>1-2 ms (ler)|1-2 ms (escrever)<br>1-2 ms (ler)|1-2 ms (escrever)<br>1-2 ms (ler)|1-2 ms (escrever)<br>1-2 ms (ler)|1-2 ms (escrever)<br>1-2 ms (ler)|1-2 ms (escrever)<br>1-2 ms (ler)|
|Dados max IOPS *|64,000|72,000|80.000|96,000|128,000|160 000|204,800|
|Taxa de registo máximo (MBps)|96|96|96|96|96|96|96|
|Trabalhadores max simultâneos (pedidos)|1600|1800|2000|2400|3200|4000|8000|
|Inícios de sessão simultâneos máx.|1600|1800|2000|2400|3200|4000|8000|
|Sessões simultâneas máx.|30,000|30,000|30,000|30,000|30,000|30,000|30,000|
|Número de réplicas|4|4|4|4|4|4|4|
|Multi-AZ|Sim|Sim|Sim|Sim|Sim|Sim|Sim|
|Escalamento Horizontal de Leituras|Sim|Sim|Sim|Sim|Sim|Sim|Sim|
|Armazenamento de backup incluído|Tamanho 1X DB|Tamanho 1X DB|Tamanho 1X DB|Tamanho 1X DB|Tamanho 1X DB|Tamanho 1X DB|Tamanho 1X DB|

\* O valor máximo para tamanhos IO varia entre 8 KB e 64 KB. Os IOPS reais dependem da carga de trabalho. Para mais informações, consulte [a Governação do IO de Dados.](resource-limits-logical-server.md#resource-governance)

## <a name="business-critical---provisioned-compute---m-series"></a>Business critical - provisioned compute - M-series

### <a name="m-series-compute-generation-part-1"></a>Geração de computação da série M (parte 1)

|Tamanho do cálculo (objetivo de serviço)|BC_M_8|BC_M_10|BC_M_12|BC_M_14|BC_M_16|BC_M_18|
|:---| ---:|---:|---:|---:|---:|---:|
|Geração computacional|Série M|Série M|Série M|Série M|Série M|Série M|
|vCores|8|10|12|14|16|18|
|Memória (GB)|235.4|294.3|353.2|412.0|470.9|529.7|
|Suporte de loja de colunas|Sim|Sim|Sim|Sim|Sim|Sim|
|Armazenamento OLTP na memória (GB)|64|80|96|112|128|150|
|Tamanho máximo dos dados (GB)|512|640|768|896|1024|1152|
|Tamanho do tronco máximo (GB)|171|213|256|299|341|384|
|Tamanho máximo de dados tempDB (GB)|256|320|384|448|512|576|
|Tipo de armazenamento|Local SSD|Local SSD|Local SSD|Local SSD|Local SSD|Local SSD|
|Latência IO (aproximada)|1-2 ms (escrever)<br>1-2 ms (ler)|1-2 ms (escrever)<br>1-2 ms (ler)|1-2 ms (escrever)<br>1-2 ms (ler)|1-2 ms (escrever)<br>1-2 ms (ler)|1-2 ms (escrever)<br>1-2 ms (ler)|1-2 ms (escrever)<br>1-2 ms (ler)|
|Dados max IOPS *|12,499|15,624|18,748|21,873|24,998|28,123|
|Taxa de registo máximo (MBps)|48|60|72|84|96|108|
|Trabalhadores max simultâneos (pedidos)|800|1,000|1200|1400|1.600|1.800|
|Inícios de sessão simultâneos máx.|800|1,000|1200|1400|1.600|1.800|
|Sessões simultâneas máx.|30000|30000|30000|30000|30000|30000|
|Número de réplicas|4|4|4|4|4|4|
|Multi-AZ|Não|Não|Não|Não|Não|Não|
|Escalamento Horizontal de Leituras|Sim|Sim|Sim|Sim|Sim|Sim|
|Armazenamento de backup incluído|Tamanho 1X DB|Tamanho 1X DB|Tamanho 1X DB|Tamanho 1X DB|Tamanho 1X DB|Tamanho 1X DB|

\* O valor máximo para tamanhos IO varia entre 8 KB e 64 KB. Os IOPS reais dependem da carga de trabalho. Para mais informações, consulte [a Governação do IO de Dados.](resource-limits-logical-server.md#resource-governance)

> [!IMPORTANT]
> Em algumas circunstâncias, pode precisar de encolher uma base de dados para recuperar o espaço não usused. Para obter mais informações, consulte [Gerir o espaço de ficheiros na Base de Dados Azure SQL](file-space-manage.md).

### <a name="m-series-compute-generation-part-2"></a>Geração de computação série M (parte 2)

|Tamanho do cálculo (objetivo de serviço)|BC_M_20|BC_M_24|BC_M_32|BC_M_64|BC_M_128|
|:---| ---:|---:|---:|---:|---:|
|Geração computacional|Série M|Série M|Série M|Série M|Série M|
|vCores|20|24|32|64|128|
|Memória (GB)|588.6|706.3|941.8|1883.5|3767.0|
|Suporte de loja de colunas|Sim|Sim|Sim|Sim|Sim|
|Armazenamento OLTP na memória (GB)|172|216|304|704|1768|
|Tamanho máximo dos dados (GB)|1280|1536|2048|4096|4096|
|Tamanho do tronco máximo (GB)|427|512|683|1024|1024|
|Tamanho máximo de dados tempDB (GB)|4096|2048|1024|768|640|
|Tipo de armazenamento|Local SSD|Local SSD|Local SSD|Local SSD|Local SSD|
|Latência IO (aproximada)|1-2 ms (escrever)<br>1-2 ms (ler)|1-2 ms (escrever)<br>1-2 ms (ler)|1-2 ms (escrever)<br>1-2 ms (ler)|1-2 ms (escrever)<br>1-2 ms (ler)|1-2 ms (escrever)<br>1-2 ms (ler)|
|Dados max IOPS *|31,248|37,497|49,996|99,993|160 000|
|Taxa de registo máximo (MBps)|120|144|192|264|264|
|Trabalhadores max simultâneos (pedidos)|2.000|2,400|3,200|6,400|12,800|
|Inícios de sessão simultâneos máx.|2.000|2,400|3,200|6,400|12,800|
|Sessões simultâneas máx.|30000|30000|30000|30000|30000|
|Número de réplicas|4|4|4|4|4|
|Multi-AZ|Não|Não|Não|Não|Não|
|Escalamento Horizontal de Leituras|Sim|Sim|Sim|Sim|Sim|
|Armazenamento de backup incluído|Tamanho 1X DB|Tamanho 1X DB|Tamanho 1X DB|Tamanho 1X DB|Tamanho 1X DB|

\* O valor máximo para tamanhos IO varia entre 8 KB e 64 KB. Os IOPS reais dependem da carga de trabalho. Para mais informações, consulte [a Governação do IO de Dados.](resource-limits-logical-server.md#resource-governance)

> [!IMPORTANT]
> Em algumas circunstâncias, pode precisar de encolher uma base de dados para recuperar o espaço não usused. Para obter mais informações, consulte [Gerir o espaço de ficheiros na Base de Dados Azure SQL](file-space-manage.md).

## <a name="business-critical---provisioned-compute---dc-series"></a>Business critical - provisioned compute - DC-series

|Tamanho do cálculo (objetivo de serviço)|BC_DC_2|BC_DC_4|BC_DC_6|BC_DC_8|
|:--- | --: |--: |--: |--: |
|Geração computacional|Série DC|Série DC|Série DC|Série DC|
|vCores|2|4|6|8|
|Memória (GB)|9|18|27|36|
|Suporte de loja de colunas|Sim|Sim|Sim|Sim|
|Armazenamento OLTP na memória (GB)|1.7|3.7|5.9|8.2|
|Tamanho máximo dos dados (GB)|768|768|768|768|
|Tamanho do tronco máximo (GB)|230|230|230|230|
|Tamanho máximo de dados tempDB (GB)|64|128|192|256|
|Tipo de armazenamento|Local SSD|Local SSD|Local SSD|Local SSD|
|Latência IO (aproximada)|1-2 ms (escrever)<br>1-2 ms (ler)|1-2 ms (escrever)<br>1-2 ms (ler)|1-2 ms (escrever)<br>1-2 ms (ler)|1-2 ms (escrever)<br>1-2 ms (ler)|
|Dados max IOPS *|14000|28000|42000|56000|
|Taxa de registo máximo (MBps)|24|48|72|96|
|Trabalhadores max simultâneos (pedidos)|200|400|600|800|
|Inícios de sessão simultâneos máx.|200|400|600|800|
|Sessões simultâneas máx.|30,000|30,000|30,000|30,000|
|Número de réplicas|4|4|4|4|
|Multi-AZ|Não|Não|Não|Não|
|Escalamento Horizontal de Leituras|Não|Não|Não|Não|
|Armazenamento de backup incluído|Tamanho 1X DB|Tamanho 1X DB|Tamanho 1X DB|Tamanho 1X DB|

\* O valor máximo para tamanhos IO varia entre 8 KB e 64 KB. Os IOPS reais dependem da carga de trabalho. Para mais informações, consulte [a Governação do IO de Dados.](resource-limits-logical-server.md#resource-governance)


## <a name="next-steps"></a>Próximos passos

- Para limites de recursos DTU para uma única base de [dados, consulte os limites de recursos para bases de dados únicas utilizando o modelo de compra do DTU](resource-limits-dtu-single-databases.md)
- Para limites de recursos vCore para piscinas elásticas, consulte [os limites de recursos para piscinas elásticas utilizando o modelo de compra vCore](resource-limits-vcore-elastic-pools.md)
- Para limites de recursos DTU para piscinas elásticas, consulte [limites de recursos para piscinas elásticas utilizando o modelo de compra DTU](resource-limits-dtu-elastic-pools.md)
- Para limites de recursos para sql Managed Instance, consulte [os limites de recursos de instância gerida SQL](../managed-instance/resource-limits.md).
- Para obter informações sobre os limites gerais do Azure, consulte [os limites de subscrição e serviços, quotas e restrições da Azure.](../../azure-resource-manager/management/azure-subscription-service-limits.md)
- Para obter informações sobre os limites de recursos num servidor, consulte [a visão geral dos limites de recursos num servidor](resource-limits-logical-server.md) para obter informações sobre limites nos níveis de servidor e subscrição.
