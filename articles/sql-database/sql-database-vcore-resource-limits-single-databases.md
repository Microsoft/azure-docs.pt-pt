---
title: limites de recursos vCore - base de dados única
description: Esta página descreve alguns limites comuns de recursos vCore para uma única base de dados na Base de Dados Azure SQL.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 03/11/2020
ms.openlocfilehash: a8f62a24ff2c6571b5267fdbf4f23bd9e05ee499
ms.sourcegitcommit: f97d3d1faf56fb80e5f901cd82c02189f95b3486
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/11/2020
ms.locfileid: "79128514"
---
# <a name="resource-limits-for-single-databases-using-the-vcore-purchasing-model"></a>Limites de recursos para bases de dados únicas utilizando o modelo de compra vCore

Este artigo fornece os limites de recursos detalhados para as bases de dados únicas da Azure SQL utilizando o modelo de compra vCore.

Para obter limites de modelos de compra dTU para bases de dados individuais num servidor de base de dados SQL, consulte a visão geral dos limites de recursos num servidor de base de [dados SQL](sql-database-resource-limits-database-server.md).

Pode definir o nível de serviço, o tamanho da computação e o valor de armazenamento para uma única base de dados utilizando o [portal Azure,](sql-database-single-databases-manage.md#manage-an-existing-sql-database-server) [Transact-SQL,](sql-database-single-databases-manage.md#transact-sql-manage-sql-database-servers-and-single-databases) [PowerShell,](sql-database-single-databases-manage.md#powershell-manage-sql-database-servers-and-single-databases)o [Azure CLI](sql-database-single-databases-manage.md#azure-cli-manage-sql-database-servers-and-single-databases)ou o [REST API](sql-database-single-databases-manage.md#rest-api-manage-sql-database-servers-and-single-databases).

> [!IMPORTANT]
> Para obter orientações e considerações de escala, consulte [a Escala de uma única base de dados](sql-database-single-database-scale.md).

## <a name="general-purpose---serverless-compute---gen5"></a>Propósito geral - computação sem servidor - Gen5

O nível de [computação sem servidor](sql-database-serverless.md) está atualmente disponível apenas no hardware gen5.

### <a name="gen5-compute-generation-part-1"></a>Geração de cálculo gen5 (parte 1)

|Tamanho da computação|GP_S_Gen5_1|GP_S_Gen5_2|GP_S_Gen5_4|GP_S_Gen5_6|GP_S_Gen5_8|
|:--- | --: |--: |--: |--: |--: |
|Geração computacional|Gen5|Gen5|Gen5|Gen5|Gen5|
|Min-max vCores|0.5-1|0.5-2|0.5-4|0.75-6|1.0-8|
|Memória min-max (GB)|2.02-3|2.05-6|2.10-12|2.25-18|3.00-24|
|Atraso de pausa automática min (minutos)|60|60|60|60|60|
|Suporte de colunas|Sim|Sim|Sim|Sim|Sim|
|Armazenamento OLTP em memória (GB)|N/D|N/D|N/D|N/D|N/D|
|Tamanho máximo de dados (GB)|512|1024|1024|1024|1536|
|Tamanho máximo do registo (GB)|154|307|307|307|461|
|Tamanho dos dados máximos tempDB (GB)|32|64|128|192|256|
|Tipo de armazenamento|SSD remoto|SSD remoto|SSD remoto|SSD remoto|SSD remoto|
|Latência de e/s (aproximada)|5-7 ms (escrever)<br>5-10 ms (ler)|5-7 ms (escrever)<br>5-10 ms (ler)|5-7 ms (escrever)<br>5-10 ms (ler)|5-7 ms (escrever)<br>5-10 ms (ler)|5-7 ms (escrever)<br>5-10 ms (ler)|
|IOPS de dados max *|320|640|1280|1920|2560|
|Taxa de registo máxima (MBps)|3.8|7.5|15|22.5|30|
|Trabalhadores simultâneos max (pedidos)|75|150|300|450|600|
|Sessões simultâneas máx.|30,000|30,000|30,000|30,000|30,000|
|Número de réplicas|1|1|1|1|1|
|Multi-AZ|N/D|N/D|N/D|N/D|N/D|
|Escalamento Horizontal de Leituras|N/D|N/D|N/D|N/D|N/D|
|Incluído armazenamento de backup|Tamanho 1X DB|Tamanho 1X DB|Tamanho 1X DB|Tamanho 1X DB|Tamanho 1X DB|

\* O valor máximo para tamanhos io que variam entre 8 KB e 64 KB. Os IOPS reais são dependentes da carga de trabalho. Para mais detalhes, consulte [Data IO Governance](sql-database-resource-limits-database-server.md#resource-governance).

### <a name="gen5-compute-generation-part-2"></a>Geração de cálculo gen5 (parte 2)

|Tamanho da computação|GP_S_Gen5_10|GP_S_Gen5_12|GP_S_Gen5_14|GP_S_Gen5_16|
|:--- | --: |--: |--: |--: |
|Geração computacional|Gen5|Gen5|Gen5|Gen5|
|Min-max vCores|1.25-10|1.50-12|1.75-14|2.00-16|
|Memória min-max (GB)|3.75-30|4.50-36|5.25-42|6.00-48|
|Atraso de pausa automática min (minutos)|60|60|60|60|
|Suporte de colunas|Sim|Sim|Sim|Sim|
|Armazenamento OLTP em memória (GB)|N/D|N/D|N/D|N/D|
|Tamanho máximo de dados (GB)|1536|3072|3072|3072|
|Tamanho máximo do registo (GB)|461|461|461|922|
|Tamanho dos dados máximos tempDB (GB)|320|384|448|512|
|Tipo de armazenamento|SSD remoto|SSD remoto|SSD remoto|SSD remoto|
|Latência de e/s (aproximada)|5-7 ms (escrever)<br>5-10 ms (ler)|5-7 ms (escrever)<br>5-10 ms (ler)|5-7 ms (escrever)<br>5-10 ms (ler)|5-7 ms (escrever)<br>5-10 ms (ler)|
|IOPS de dados max *|3200|3840|4480|5120|
|Taxa de registo máxima (MBps)|30|30|30|30|
|Trabalhadores simultâneos max (pedidos)|750|900|1050|1200|
|Sessões simultâneas máx.|30,000|30,000|30,000|30,000|
|Número de réplicas|1|1|1|1|
|Multi-AZ|N/D|N/D|N/D|N/D|
|Escalamento Horizontal de Leituras|N/D|N/D|N/D|N/D|
|Incluído armazenamento de backup|Tamanho 1X DB|Tamanho 1X DB|Tamanho 1X DB|Tamanho 1X DB|

\* O valor máximo para tamanhos io que variam entre 8 KB e 64 KB. Os IOPS reais são dependentes da carga de trabalho. Para mais detalhes, consulte [Data IO Governance](sql-database-resource-limits-database-server.md#resource-governance).

## <a name="hyperscale---provisioned-compute---gen4"></a>Hiperescala - calculador - Gen4

### <a name="gen4-compute-generation-part-1"></a>Geração de cálculo Gen4 (parte 1)

|Nível de desempenho|HS_Gen4_1|HS_Gen4_2|HS_Gen4_3|HS_Gen4_4|HS_Gen4_5|HS_Gen4_6|
|:--- | --: |--: |--: |---: | --: |--: |
|Geração computacional|Gen4|Gen4|Gen4|Gen4|Gen4|Gen4|
|vCores|1|2|3|4|5|6|
|Memória (GB)|7|14|21|28|35|42|
|[RBPEX](sql-database-service-tier-hyperscale.md#compute) Tamanho|Memória 3X|Memória 3X|Memória 3X|Memória 3X|Memória 3X|Memória 3X|
|Suporte de colunas|Sim|Sim|Sim|Sim|Sim|Sim|
|Armazenamento OLTP em memória (GB)|N/D|N/D|N/D|N/D|N/D|N/D|
|Tamanho máximo de dados (TB)|100 |100 |100 |100 |100 |100|
|Tamanho máximo do diário de bordo (TB)|1 |1 |1 |1 |1 |1 |
|Tamanho dos dados máximos tempDB (GB)|32|64|96|128|160|192|
|Tipo de armazenamento| [Nota 1](#notes) |[Nota 1](#notes)|[Nota 1](#notes) |[Nota 1](#notes) |[Nota 1](#notes) |[Nota 1](#notes) |
|IOPS de dados max *|[Nota 2](#notes)|[Nota 2](#notes)|[Nota 2](#notes)|[Nota 2](#notes)|[Nota 2](#notes)|[Nota 2](#notes)|
|Taxa de registo máxima (MBps)|105 |105 |105 |105 |105 |105 |
|Latência de e/s (aproximada)|[Nota 3](#notes)|[Nota 3](#notes)|[Nota 3](#notes)|[Nota 3](#notes)|[Nota 3](#notes)|[Nota 3](#notes)|
|Trabalhadores simultâneos max (pedidos)|200|400|600|800|1000|1200|
|Sessões simultâneas máx.|30,000|30,000|30,000|30,000|30,000|30,000|
|Réplicas secundárias|0-4|0-4|0-4|0-4|0-4|0-4|
|Multi-AZ|N/D|N/D|N/D|N/D|N/D|N/D|
|Escalamento Horizontal de Leituras|Sim|Sim|Sim|Sim|Sim|Sim|
|Retenção de armazenamento de reserva|7 dias|7 dias|7 dias|7 dias|7 dias|7 dias|
|||

### <a name="gen4-compute-generation-part-2"></a>Geração de cálculo gen4 (parte 2)

|Nível de desempenho|HS_Gen4_7|HS_Gen4_8|HS_Gen4_9|HS_Gen4_10|HS_Gen4_16|HS_Gen4_24|
|:--- | ---: |--: |--: | --: |--: |--: |
|Geração computacional|Gen4|Gen4|Gen4|Gen4|Gen4|Gen4|
|vCores|7|8|9|10|16|24|
|Memória (GB)|49|56|63|70|112|159.5|
|[RBPEX](sql-database-service-tier-hyperscale.md#compute) Tamanho|Memória 3X|Memória 3X|Memória 3X|Memória 3X|Memória 3X|Memória 3X|
|Suporte de colunas|Sim|Sim|Sim|Sim|Sim|Sim|
|Armazenamento OLTP em memória (GB)|N/D|N/D|N/D|N/D|N/D|N/D|
|Tamanho máximo de dados (TB)|100 |100 |100 |100 |100 |100 |
|Tamanho máximo do diário de bordo (TB)|1 |1 |1 |1 |1 |1 |
|Tamanho dos dados máximos tempDB (GB)|224|256|288|320|512|768|
|Tipo de armazenamento| [Nota 1](#notes) |[Nota 1](#notes) |[Nota 1](#notes) |[Nota 1](#notes) |[Nota 1](#notes) |[Nota 1](#notes) |
|IOPS de dados max *|[Nota 2](#notes)|[Nota 2](#notes)|[Nota 2](#notes)|[Nota 2](#notes)|[Nota 2](#notes)|[Nota 2](#notes)|
|Taxa de registo máxima (MBps)|105 |105 |105 |105 |105 |105 |
|Latência de e/s (aproximada)|[Nota 3](#notes)|[Nota 3](#notes)|[Nota 3](#notes)|[Nota 3](#notes)|[Nota 3](#notes)|[Nota 3](#notes)|
|Trabalhadores simultâneos max (pedidos)|1400|1600|1800|2000|3200|4800|
|Sessões simultâneas máx.|30,000|30,000|30,000|30,000|30,000|30,000|
|Réplicas secundárias|0-4|0-4|0-4|0-4|0-4|0-4|
|Multi-AZ|N/D|N/D|N/D|N/D|N/D|N/D|
|Escalamento Horizontal de Leituras|Sim|Sim|Sim|Sim|Sim|Sim|
|Retenção de armazenamento de reserva|7 dias|7 dias|7 dias|7 dias|7 dias|7 dias|
|||

\* O valor máximo para tamanhos io que variam entre 8 KB e 64 KB. Os IOPS reais são dependentes da carga de trabalho. Para mais detalhes, consulte [Data IO Governance](sql-database-resource-limits-database-server.md#resource-governance).

## <a name="hyperscale---provisioned-compute---gen5"></a>Hiperescala - calculador - Gen5

### <a name="gen5-compute-generation-part-1"></a>Geração de cálculo gen5 (parte 1)

|Nível de desempenho|HS_Gen5_2|HS_Gen5_4|HS_Gen5_6|HS_Gen_8|HS_Gen5_10|HS_Gen5_12|HS_Gen5_14|
|:--- | --: |--: |--: |--: |---: | --: |--: |--: |
|Geração computacional|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|
|vCores|2|4|6|8|10|12|14|
|Memória (GB)|10.4|20.8|31.1|41.5|51.9|62.3|72.7|
|[RBPEX](sql-database-service-tier-hyperscale.md#compute) Tamanho|Memória 3X|Memória 3X|Memória 3X|Memória 3X|Memória 3X|Memória 3X|Memória 3X|
|Suporte de colunas|Sim|Sim|Sim|Sim|Sim|Sim|Sim|
|Armazenamento OLTP em memória (GB)|N/D|N/D|N/D|N/D|N/D|N/D|N/D|
|Tamanho máximo de dados (TB)|100 |100 |100 |100 |100 |100 |100|
|Tamanho máximo do diário de bordo (TB)|1 |1 |1 |1 |1 |1 |1 |
|Tamanho dos dados máximos tempDB (GB)|64|128|192|256|320|384|448|
|Tipo de armazenamento| [Nota 1](#notes) |[Nota 1](#notes)|[Nota 1](#notes) |[Nota 1](#notes) |[Nota 1](#notes) |[Nota 1](#notes) |[Nota 1](#notes) |
|IOPS de dados max *|[Nota 2](#notes)|[Nota 2](#notes)|[Nota 2](#notes)|[Nota 2](#notes)|[Nota 2](#notes)|[Nota 2](#notes)|[Nota 2](#notes)|
|Taxa de registo máxima (MBps)|105 |105 |105 |105 |105 |105 |105 |
|Latência de e/s (aproximada)|[Nota 3](#notes)|[Nota 3](#notes)|[Nota 3](#notes)|[Nota 3](#notes)|[Nota 3](#notes)|[Nota 3](#notes)|[Nota 3](#notes)|
|Trabalhadores simultâneos max (pedidos)|200|400|600|800|1000|1200|1400|
|Sessões simultâneas máx.|30,000|30,000|30,000|30,000|30,000|30,000|30,000|
|Réplicas secundárias|0-4|0-4|0-4|0-4|0-4|0-4|0-4|
|Multi-AZ|N/D|N/D|N/D|N/D|N/D|N/D|N/D|
|Escalamento Horizontal de Leituras|Sim|Sim|Sim|Sim|Sim|Sim|Sim|
|Retenção de armazenamento de reserva|7 dias|7 dias|7 dias|7 dias|7 dias|7 dias|7 dias|
|||

\* O valor máximo para tamanhos io que variam entre 8 KB e 64 KB. Os IOPS reais são dependentes da carga de trabalho. Para mais detalhes, consulte [Data IO Governance](sql-database-resource-limits-database-server.md#resource-governance).

### <a name="gen5-compute-generation-part-2"></a>Geração de cálculo gen5 (parte 2)

|Nível de desempenho|HS_Gen5_16|HS_Gen5_18|HS_Gen5_20|HS_Gen_24|HS_Gen5_32|HS_Gen5_40|HS_Gen5_80|
|:--- | --: |--: |--: |--: |---: |--: |--: |
|Geração computacional|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|
|vCores|16|18|20|24|32|40|80|
|Memória (GB)|83|93.4|103.8|124.6|166.1|207.6|415.2|
|[RBPEX](sql-database-service-tier-hyperscale.md#compute) Tamanho|Memória 3X|Memória 3X|Memória 3X|Memória 3X|Memória 3X|Memória 3X|Memória 3X|
|Suporte de colunas|Sim|Sim|Sim|Sim|Sim|Sim|Sim|
|Armazenamento OLTP em memória (GB)|N/D|N/D|N/D|N/D|N/D|N/D|N/D|
|Tamanho máximo de dados (TB)|100 |100 |100 |100 |100 |100 |100 |
|Tamanho máximo do diário de bordo (TB)|1 |1 |1 |1 |1 |1 |1 |
|Tamanho dos dados máximos tempDB (GB)|512|576|640|768|1024|1280|2560|
|Tipo de armazenamento| [Nota 1](#notes) |[Nota 1](#notes)|[Nota 1](#notes)|[Nota 1](#notes) |[Nota 1](#notes) |[Nota 1](#notes) |[Nota 1](#notes) |
|IOPS de dados max *|[Nota 2](#notes)|[Nota 2](#notes)|[Nota 2](#notes)|[Nota 2](#notes)|[Nota 2](#notes)|[Nota 2](#notes)|[Nota 2](#notes)|
|Taxa de registo máxima (MBps)|105 |105 |105 |105 |105 |105 |105 |
|Latência de e/s (aproximada)|[Nota 3](#notes)|[Nota 3](#notes)|[Nota 3](#notes)|[Nota 3](#notes)|[Nota 3](#notes)|[Nota 3](#notes)|[Nota 3](#notes)|
|Trabalhadores simultâneos max (pedidos)|1600|1800|2000|2400|3200|4000|8000|
|Sessões simultâneas máx.|30,000|30,000|30,000|30,000|30,000|30,000|30,000|
|Réplicas secundárias|0-4|0-4|0-4|0-4|0-4|0-4|0-4|
|Multi-AZ|N/D|N/D|N/D|N/D|N/D|N/D|N/D|
|Escalamento Horizontal de Leituras|Sim|Sim|Sim|Sim|Sim|Sim|Sim|
|Retenção de armazenamento de reserva|7 dias|7 dias|7 dias|7 dias|7 dias|7 dias|7 dias|
|||

\* O valor máximo para tamanhos io que variam entre 8 KB e 64 KB. Os IOPS reais são dependentes da carga de trabalho. Para mais detalhes, consulte [Data IO Governance](sql-database-resource-limits-database-server.md#resource-governance).

#### <a name="notes"></a>Notas

**Nota 1:** Hyperscale é uma arquitetura multi-camadas com componentes de computação e armazenamento separados: [Hyperscale Service Tier Architecture](sql-database-service-tier-hyperscale.md#distributed-functions-architecture)

**Nota 2:** A arquitetura multi-camadas de hiperescala tem cache a vários níveis. IOPS eficaz dependerá da carga de trabalho.

**Nota 3:** A latência é de 1-2 ms para dados na cache baseada em SSD RBPEX em réplicas de cálculo, que caches as páginas de dados mais utilizadas. Maior latência para dados recuperados a partir de servidores de página.

## <a name="general-purpose---provisioned-compute---gen4"></a>Finalidade geral - calculado - Gen4

> [!IMPORTANT]
> As novas bases de dados gen4 já não são suportadas nas regiões do Leste da Austrália ou do Brasil.

### <a name="gen4-compute-generation-part-1"></a>Geração de cálculo Gen4 (parte 1)

|Tamanho da computação|GP_Gen4_1|GP_Gen4_2|GP_Gen4_3|GP_Gen4_4|GP_Gen4_5|GP_Gen4_6
|:--- | --: |--: |--: |--: |--: |--: |
|Geração computacional|Gen4|Gen4|Gen4|Gen4|Gen4|Gen4|
|vCores|1|2|3|4|5|6|
|Memória (GB)|7|14|21|28|35|42|
|Suporte de colunas|Sim|Sim|Sim|Sim|Sim|Sim|
|Armazenamento OLTP em memória (GB)|N/D|N/D|N/D|N/D|N/D|N/D|
|Tamanho máximo de dados (GB)|1024|1024|1536|1536|1536|3072|
|Tamanho máximo do registo (GB)|307|307|461|461|461|922|
|Tamanho dos dados máximos tempDB (GB)|32|64|96|128|160|192|
|Tipo de armazenamento|SSD remoto|SSD remoto|SSD remoto|SSD remoto|SSD remoto|SSD remoto|
|Latência de e/s (aproximada)|5-7 ms (escrever)<br>5-10 ms (ler)|5-7 ms (escrever)<br>5-10 ms (ler)|5-7 ms (escrever)<br>5-10 ms (ler)|5-7 ms (escrever)<br>5-10 ms (ler)|5-7 ms (escrever)<br>5-10 ms (ler)|5-7 ms (escrever)<br>5-10 ms (ler)|
|IOPS de dados max *|320|640|960|1280|1600|1920|
|Taxa de registo máxima (MBps)|3.75|7.5|11.25|15|18.75|22.5|
|Trabalhadores simultâneos max (pedidos)|200|400|600|800|1000|1200|
|Sessões simultâneas máx.|30,000|30,000|30,000|30,000|30,000|30,000|
|Número de réplicas|1|1|1|1|1|1|
|Multi-AZ|N/D|N/D|N/D|N/D|N/D|N/D|
|Escalamento Horizontal de Leituras|N/D|N/D|N/D|N/D|N/D|N/D|
|Incluído armazenamento de backup|Tamanho 1X DB|Tamanho 1X DB|Tamanho 1X DB|Tamanho 1X DB|Tamanho 1X DB|Tamanho 1X DB|

\* O valor máximo para tamanhos io que variam entre 8 KB e 64 KB. Os IOPS reais são dependentes da carga de trabalho. Para mais detalhes, consulte [Data IO Governance](sql-database-resource-limits-database-server.md#resource-governance).

### <a name="gen4-compute-generation-part-2"></a>Geração de cálculo gen4 (parte 2)

|Tamanho da computação|GP_Gen4_7|GP_Gen4_8|GP_Gen4_9|GP_Gen4_10|GP_Gen4_16|GP_Gen4_24
|:--- | --: |--: |--: |--: |--: |--: |
|Geração computacional|Gen4|Gen4|Gen4|Gen4|Gen4|Gen4|
|vCores|7|8|9|10|16|24|
|Memória (GB)|49|56|63|70|112|159.5|
|Suporte de colunas|Sim|Sim|Sim|Sim|Sim|Sim|
|Armazenamento OLTP em memória (GB)|N/D|N/D|N/D|N/D|N/D|N/D|
|Tamanho máximo de dados (GB)|3072|3072|3072|3072|4096|4096|
|Tamanho máximo do registo (GB)|922|922|922|922|1229|1229|
|Tamanho dos dados máximos tempDB (GB)|224|256|288|320|512|768|
|Tipo de armazenamento|SSD remoto|SSD remoto|SSD remoto|SSD remoto|SSD remoto|SSD remoto|
|Latência de e/s (aproximada)|5-7 ms (escrever)<br>5-10 ms (ler)|5-7 ms (escrever)<br>5-10 ms (ler)|5-7 ms (escrever)<br>5-10 ms (ler)|5-7 ms (escrever)<br>5-10 ms (ler)|5-7 ms (escrever)<br>5-10 ms (ler)|5-7 ms (escrever)<br>5-10 ms (ler)
|IOPS de dados max *|2240|2560|2880|3200|5120|7680|
|Taxa de registo máxima (MBps)|26.3|30|30|30|30|30|
|Trabalhadores simultâneos max (pedidos)|1400|1600|1800|2000|3200|4800|
|Sessões simultâneas máx.|30,000|30,000|30,000|30,000|30,000|30,000|
|Número de réplicas|1|1|1|1|1|1|
|Multi-AZ|N/D|N/D|N/D|N/D|N/D|N/D|
|Escalamento Horizontal de Leituras|N/D|N/D|N/D|N/D|N/D|N/D|
|Incluído armazenamento de backup|Tamanho 1X DB|Tamanho 1X DB|Tamanho 1X DB|Tamanho 1X DB|Tamanho 1X DB|Tamanho 1X DB|

\* O valor máximo para tamanhos io que variam entre 8 KB e 64 KB. Os IOPS reais são dependentes da carga de trabalho. Para mais detalhes, consulte [Data IO Governance](sql-database-resource-limits-database-server.md#resource-governance).

## <a name="general-purpose---provisioned-compute---gen5"></a>Finalidade geral - calculado - Gen5

### <a name="gen5-compute-generation-part-1"></a>Geração de cálculo gen5 (parte 1)

|Tamanho da computação|GP_Gen5_2|GP_Gen5_4|GP_Gen5_6|GP_Gen5_8|GP_Gen5_10|GP_Gen5_12|GP_Gen5_14|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|Geração computacional|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|
|vCores|2|4|6|8|10|12|14|
|Memória (GB)|10.4|20.8|31.1|41.5|51.9|62.3|72.7|
|Suporte de colunas|Sim|Sim|Sim|Sim|Sim|Sim|Sim|
|Armazenamento OLTP em memória (GB)|N/D|N/D|N/D|N/D|N/D|N/D|N/D|
|Tamanho máximo de dados (GB)|1024|1024|1536|1536|1536|3072|3072|
|Tamanho máximo do registo (GB)|307|307|461|461|461|922|922|
|Tamanho dos dados máximos tempDB (GB)|64|128|192|256|320|384|384|
|Tipo de armazenamento|SSD remoto|SSD remoto|SSD remoto|SSD remoto|SSD remoto|SSD remoto|SSD remoto|
|Latência de e/s (aproximada)|5-7 ms (escrever)<br>5-10 ms (ler)|5-7 ms (escrever)<br>5-10 ms (ler)|5-7 ms (escrever)<br>5-10 ms (ler)|5-7 ms (escrever)<br>5-10 ms (ler)|5-7 ms (escrever)<br>5-10 ms (ler)|5-7 ms (escrever)<br>5-10 ms (ler)|5-7 ms (escrever)<br>5-10 ms (ler)|
|IOPS de dados max *|640|1280|1920|2560|3200|3840|4480|
|Taxa de registo máxima (MBps)|7.5|15|22.5|30|30|30|30|
|Trabalhadores simultâneos max (pedidos)|200|400|600|800|1000|1200|1400|
|Sessões simultâneas máx.|30,000|30,000|30,000|30,000|30,000|30,000|30,000|
|Número de réplicas|1|1|1|1|1|1|1|
|Multi-AZ|N/D|N/D|N/D|N/D|N/D|N/D|N/D|
|Escalamento Horizontal de Leituras|N/D|N/D|N/D|N/D|N/D|N/D|N/D|
|Incluído armazenamento de backup|Tamanho 1X DB|Tamanho 1X DB|Tamanho 1X DB|Tamanho 1X DB|Tamanho 1X DB|Tamanho 1X DB|Tamanho 1X DB|

\* O valor máximo para tamanhos io que variam entre 8 KB e 64 KB. Os IOPS reais são dependentes da carga de trabalho. Para mais detalhes, consulte [Data IO Governance](sql-database-resource-limits-database-server.md#resource-governance).

### <a name="gen5-compute-generation-part-2"></a>Geração de cálculo gen5 (parte 2)

|Tamanho da computação|GP_Gen5_16|GP_Gen5_18|GP_Gen5_20|GP_Gen5_24|GP_Gen5_32|GP_Gen5_40|GP_Gen5_80|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|Geração computacional|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|
|vCores|16|18|20|24|32|40|80|
|Memória (GB)|83|93.4|103.8|124.6|166.1|207.6|415.2|
|Suporte de colunas|Sim|Sim|Sim|Sim|Sim|Sim|Sim|
|Armazenamento OLTP em memória (GB)|N/D|N/D|N/D|N/D|N/D|N/D|N/D|
|Tamanho máximo de dados (GB)|3072|3072|3072|4096|4096|4096|4096|
|Tamanho máximo do registo (GB)|922|922|922|1229|1229|1229|1229|
|Tamanho dos dados máximos tempDB (GB)|512|576|640|768|1024|1280|2560|
|Tipo de armazenamento|SSD remoto|SSD remoto|SSD remoto|SSD remoto|SSD remoto|SSD remoto|SSD remoto|
|Latência de e/s (aproximada)|5-7 ms (escrever)<br>5-10 ms (ler)|5-7 ms (escrever)<br>5-10 ms (ler)|5-7 ms (escrever)<br>5-10 ms (ler)|5-7 ms (escrever)<br>5-10 ms (ler)|5-7 ms (escrever)<br>5-10 ms (ler)|5-7 ms (escrever)<br>5-10 ms (ler)|5-7 ms (escrever)<br>5-10 ms (ler)|
|IOPS de dados max *|5120|5760|6400|7680|10240|12800|25600|
|Taxa de registo máxima (MBps)|30|30|30|30|30|30|30|
|Trabalhadores simultâneos max (pedidos)|1600|1800|2000|2400|3200|4000|8000|
|Sessões simultâneas máx.|30,000|30,000|30,000|30,000|30,000|30,000|30,000|
|Número de réplicas|1|1|1|1|1|1|1|
|Multi-AZ|N/D|N/D|N/D|N/D|N/D|N/D|N/D|
|Escalamento Horizontal de Leituras|N/D|N/D|N/D|N/D|N/D|N/D|N/D|
|Incluído armazenamento de backup|Tamanho 1X DB|Tamanho 1X DB|Tamanho 1X DB|Tamanho 1X DB|Tamanho 1X DB|Tamanho 1X DB|Tamanho 1X DB|

\* O valor máximo para tamanhos io que variam entre 8 KB e 64 KB. Os IOPS reais são dependentes da carga de trabalho. Para mais detalhes, consulte [Data IO Governance](sql-database-resource-limits-database-server.md#resource-governance).

## <a name="general-purpose---provisioned-compute---fsv2-series"></a>Finalidade geral - calculado - Série Fsv2

### <a name="fsv2-series-compute-generation-preview"></a>Geração de computação da série Fsv2 (pré-visualização)

|Tamanho da computação|GP_Fsv2_72|
|:--- | --: |
|Geração computacional|Série Fsv2|
|vCores|72|
|Memória (GB)|136.2|
|Suporte de colunas|Sim|
|Armazenamento OLTP em memória (GB)|N/D|
|Tamanho máximo de dados (GB)|4096|
|Tamanho máximo do registo (GB)|1024|
|Tamanho dos dados máximos tempDB (GB)|333|
|Tipo de armazenamento|SSD remoto|
|Latência de e/s (aproximada)|5-7 ms (escrever)<br>5-10 ms (ler)|
|IOPS de dados max *|12,800|
|Taxa de registo máxima (MBps)|30|
|Trabalhadores simultâneos max (pedidos)|3600|
|Inícios de sessão simultâneos máx.|3600|
|Sessões simultâneas máx.|30,000|
|Número de réplicas|1|
|Multi-AZ|N/D|
|Escalamento Horizontal de Leituras|N/D|
|Incluído armazenamento de backup|Tamanho 1X DB|

\* O valor máximo para tamanhos io que variam entre 8 KB e 64 KB. Os IOPS reais são dependentes da carga de trabalho. Para mais detalhes, consulte [Data IO Governance](sql-database-resource-limits-database-server.md#resource-governance).

## <a name="business-critical---provisioned-compute---gen4"></a>Business critical - calculado - Gen4

> [!IMPORTANT]
> As novas bases de dados gen4 já não são suportadas nas regiões do Leste da Austrália ou do Brasil.

### <a name="gen4-compute-generation-part-1"></a>Geração de cálculo Gen4 (parte 1)

|Tamanho da computação|BC_Gen4_1|BC_Gen4_2|BC_Gen4_3|BC_Gen4_4|BC_Gen4_5|BC_Gen4_6|
|:--- | --: |--: |--: |--: |--: |--: |
|Geração computacional|Gen4|Gen4|Gen4|Gen4|Gen4|Gen4|
|vCores|1|2|3|4|5|6|
|Memória (GB)|7|14|21|28|35|42|
|Suporte de colunas|Sim|Sim|Sim|Sim|Sim|Sim|
|Armazenamento OLTP em memória (GB)|1|2|3|4|5|6|
|Tipo de armazenamento|Local SSD|Local SSD|Local SSD|Local SSD|Local SSD|Local SSD|
|Tamanho máximo de dados (GB)|1024|1024|1024|1024|1024|1024|
|Tamanho máximo do registo (GB)|307|307|307|307|307|307|
|Tamanho dos dados máximos tempDB (GB)|32|64|96|128|160|192|
|Latência de e/s (aproximada)|1-2 ms (escrever)<br>1-2 ms (ler)|1-2 ms (escrever)<br>1-2 ms (ler)|1-2 ms (escrever)<br>1-2 ms (ler)|1-2 ms (escrever)<br>1-2 ms (ler)|1-2 ms (escrever)<br>1-2 ms (ler)|1-2 ms (escrever)<br>1-2 ms (ler)|
|IOPS de dados max *|4,000|8,000|12.000|16,000|20,000|24,000|
|Taxa de registo máxima (MBps)|8|16|24|32|40|48|
|Trabalhadores simultâneos max (pedidos)|200|400|600|800|1000|1200|
|Inícios de sessão simultâneos máx.|200|400|600|800|1000|1200|
|Sessões simultâneas máx.|30,000|30,000|30,000|30,000|30,000|30,000|
|Número de réplicas|4|4|4|4|4|4|
|Multi-AZ|Sim|Sim|Sim|Sim|Sim|Sim|
|Escalamento Horizontal de Leituras|Sim|Sim|Sim|Sim|Sim|Sim|
|Incluído armazenamento de backup|Tamanho 1X DB|Tamanho 1X DB|Tamanho 1X DB|Tamanho 1X DB|Tamanho 1X DB|Tamanho 1X DB|

\* O valor máximo para tamanhos io que variam entre 8 KB e 64 KB. Os IOPS reais são dependentes da carga de trabalho. Para mais detalhes, consulte [Data IO Governance](sql-database-resource-limits-database-server.md#resource-governance).

### <a name="gen4-compute-generation-part-2"></a>Geração de cálculo gen4 (parte 2)

|Tamanho da computação|BC_Gen4_7|BC_Gen4_8|BC_Gen4_9|BC_Gen4_10|BC_Gen4_16|BC_Gen4_24|
|:--- | --: |--: |--: |--: |--: |--: |
|Geração computacional|Gen4|Gen4|Gen4|Gen4|Gen4|Gen4|
|vCores|7|8|9|10|16|24|
|Memória (GB)|49|56|63|70|112|159.5|
|Suporte de colunas|Sim|Sim|Sim|Sim|Sim|Sim|
|Armazenamento OLTP em memória (GB)|7|8|9.5|11|20|36|
|Tipo de armazenamento|Local SSD|Local SSD|Local SSD|Local SSD|Local SSD|Local SSD|
|Tamanho máximo de dados (GB)|1024|1024|1024|1024|1024|1024|
|Tamanho máximo do registo (GB)|307|307|307|307|307|307|
|Tamanho dos dados máximos tempDB (GB)|224|256|288|320|512|768|
|Latência de e/s (aproximada)|1-2 ms (escrever)<br>1-2 ms (ler)|1-2 ms (escrever)<br>1-2 ms (ler)|1-2 ms (escrever)<br>1-2 ms (ler)|1-2 ms (escrever)<br>1-2 ms (ler)|1-2 ms (escrever)<br>1-2 ms (ler)|1-2 ms (escrever)<br>1-2 ms (ler)|
|IOPS de dados max |28,000|32,000|36,000|40,000|64,000|76,800|
|Taxa de registo máxima (MBps)|56|64|64|64|64|64|
|Trabalhadores simultâneos max (pedidos)|1400|1600|1800|2000|3200|4800|
|Logins simultâneos max (pedidos)|1400|1600|1800|2000|3200|4800|
|Sessões simultâneas máx.|30,000|30,000|30,000|30,000|30,000|30,000|
|Número de réplicas|4|4|4|4|4|4|
|Multi-AZ|Sim|Sim|Sim|Sim|Sim|Sim|
|Escalamento Horizontal de Leituras|Sim|Sim|Sim|Sim|Sim|Sim|
|Incluído armazenamento de backup|Tamanho 1X DB|Tamanho 1X DB|Tamanho 1X DB|Tamanho 1X DB|Tamanho 1X DB|Tamanho 1X DB|

\* O valor máximo para tamanhos io que variam entre 8 KB e 64 KB. Os IOPS reais são dependentes da carga de trabalho. Para mais detalhes, consulte [Data IO Governance](sql-database-resource-limits-database-server.md#resource-governance).

## <a name="business-critical---provisioned-compute---gen5"></a>Business critical - calculado - Gen5

### <a name="gen5-compute-generation-part-1"></a>Geração de cálculo gen5 (parte 1)

|Tamanho da computação|BC_Gen5_2|BC_Gen5_4|BC_Gen5_6|BC_Gen5_8|BC_Gen5_10|BC_Gen5_12|BC_Gen5_14|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|Geração computacional|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|
|vCores|2|4|6|8|10|12|14|
|Memória (GB)|10.4|20.8|31.1|41.5|51.9|62.3|72.7|
|Suporte de colunas|Sim|Sim|Sim|Sim|Sim|Sim|Sim|
|Armazenamento OLTP em memória (GB)|1.57|3.14|4.71|6.28|8.65|11.02|13.39|
|Tamanho máximo de dados (GB)|1024|1024|1536|1536|1536|3072|3072|
|Tamanho máximo do registo (GB)|307|307|461|461|461|922|922|
|Tamanho dos dados máximos tempDB (GB)|64|128|192|256|320|384|448|
|Tipo de armazenamento|Local SSD|Local SSD|Local SSD|Local SSD|Local SSD|Local SSD|Local SSD|
|Latência de e/s (aproximada)|1-2 ms (escrever)<br>1-2 ms (ler)|1-2 ms (escrever)<br>1-2 ms (ler)|1-2 ms (escrever)<br>1-2 ms (ler)|1-2 ms (escrever)<br>1-2 ms (ler)|1-2 ms (escrever)<br>1-2 ms (ler)|1-2 ms (escrever)<br>1-2 ms (ler)|1-2 ms (escrever)<br>1-2 ms (ler)|
|IOPS de dados max *|8000|16,000|24,000|32,000|40,000|48,000|56,000|
|Taxa de registo máxima (MBps)|24|48|72|96|96|96|96|
|Trabalhadores simultâneos max (pedidos)|200|400|600|800|1000|1200|1400|
|Inícios de sessão simultâneos máx.|200|400|600|800|1000|1200|1400|
|Sessões simultâneas máx.|30,000|30,000|30,000|30,000|30,000|30,000|30,000|
|Número de réplicas|4|4|4|4|4|4|4|
|Multi-AZ|Sim|Sim|Sim|Sim|Sim|Sim|Sim|
|Escalamento Horizontal de Leituras|Sim|Sim|Sim|Sim|Sim|Sim|Sim|
|Incluído armazenamento de backup|Tamanho 1X DB|Tamanho 1X DB|Tamanho 1X DB|Tamanho 1X DB|Tamanho 1X DB|Tamanho 1X DB|Tamanho 1X DB|

\* O valor máximo para tamanhos io que variam entre 8 KB e 64 KB. Os IOPS reais são dependentes da carga de trabalho. Para mais detalhes, consulte [Data IO Governance](sql-database-resource-limits-database-server.md#resource-governance).

### <a name="gen5-compute-generation-part-2"></a>Geração de cálculo gen5 (parte 2)

|Tamanho da computação|BC_Gen5_16|BC_Gen5_18|BC_Gen5_20|BC_Gen5_24|BC_Gen5_32|BC_Gen5_40|BC_Gen5_80|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|Geração computacional|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|
|vCores|16|18|20|24|32|40|80|
|Memória (GB)|83|93.4|103.8|124.6|166.1|207.6|415.2|
|Suporte de colunas|Sim|Sim|Sim|Sim|Sim|Sim|Sim|
|Armazenamento OLTP em memória (GB)|15.77|18.14|20.51|25.25|37.94|52.23|131.64|
|Tamanho máximo de dados (GB)|3072|3072|3072|4096|4096|4096|4096|
|Tamanho máximo do registo (GB)|922|922|922|1229|1229|1229|1229|
|Tamanho dos dados máximos tempDB (GB)|512|576|640|768|1024|1280|2560|
|Tipo de armazenamento|Local SSD|Local SSD|Local SSD|Local SSD|Local SSD|Local SSD|Local SSD|
|Latência de e/s (aproximada)|1-2 ms (escrever)<br>1-2 ms (ler)|1-2 ms (escrever)<br>1-2 ms (ler)|1-2 ms (escrever)<br>1-2 ms (ler)|1-2 ms (escrever)<br>1-2 ms (ler)|1-2 ms (escrever)<br>1-2 ms (ler)|1-2 ms (escrever)<br>1-2 ms (ler)|1-2 ms (escrever)<br>1-2 ms (ler)|
|IOPS de dados max *|64,000|72,000|80,000|96,000|128,000|160,000|204,800|
|Taxa de registo máxima (MBps)|96|96|96|96|96|96|96|
|Trabalhadores simultâneos max (pedidos)|1600|1800|2000|2400|3200|4000|8000|
|Inícios de sessão simultâneos máx.|1600|1800|2000|2400|3200|4000|8000|
|Sessões simultâneas máx.|30,000|30,000|30,000|30,000|30,000|30,000|30,000|
|Número de réplicas|4|4|4|4|4|4|4|
|Multi-AZ|Sim|Sim|Sim|Sim|Sim|Sim|Sim|
|Escalamento Horizontal de Leituras|Sim|Sim|Sim|Sim|Sim|Sim|Sim|
|Incluído armazenamento de backup|Tamanho 1X DB|Tamanho 1X DB|Tamanho 1X DB|Tamanho 1X DB|Tamanho 1X DB|Tamanho 1X DB|Tamanho 1X DB|

\* O valor máximo para tamanhos io que variam entre 8 KB e 64 KB. Os IOPS reais são dependentes da carga de trabalho. Para mais detalhes, consulte [Data IO Governance](sql-database-resource-limits-database-server.md#resource-governance).

## <a name="business-critical---provisioned-compute---m-series"></a>Business critical - calculado - Série M

### <a name="m-series-compute-generation-preview"></a>Geração de computação da série M (pré-visualização)

|Tamanho da computação|BC_M_128|
|:--- | --: |
|Geração computacional|Série M|
|vCores|128|
|Memória (GB)|3767.1|
|Suporte de colunas|Sim|
|Armazenamento OLTP em memória (GB)|1768|
|Tamanho máximo de dados (GB)|4096|
|Tamanho máximo do registo (GB)|2048|
|Tamanho dos dados máximos tempDB (GB)|4096|
|Tipo de armazenamento|Local SSD|
|Latência de e/s (aproximada)|1-2 ms (escrever)<br>1-2 ms (ler)|
|IOPS de dados max *|160,000|
|Taxa de registo máxima (MBps)|264|
|Trabalhadores simultâneos max (pedidos)|12,800|
|Inícios de sessão simultâneos máx.|12,800|
|Sessões simultâneas máx.|30000|
|Número de réplicas|4|
|Multi-AZ|Sim|
|Escalamento Horizontal de Leituras|Sim|
|Incluído armazenamento de backup|Tamanho 1X DB|

\* O valor máximo para tamanhos io que variam entre 8 KB e 64 KB. Os IOPS reais são dependentes da carga de trabalho. Para mais detalhes, consulte [Data IO Governance](sql-database-resource-limits-database-server.md#resource-governance).

> [!IMPORTANT]
> Em algumas circunstâncias, poderá ter reduzir uma base de dados para recuperar espaço não utilizado. Para mais informações, consulte Gerir o espaço de ficheiros na Base de [Dados Azure SQL](sql-database-file-space-management.md).

## <a name="next-steps"></a>Passos seguintes

- Para os limites de recursos dTU para uma única base de dados, consulte [os limites de recursos para bases de dados únicas utilizando o modelo](sql-database-dtu-resource-limits-single-databases.md) de compra dTU
- Para os limites de recursos vCore para piscinas elásticas, consulte [os limites de recursos para piscinas elásticas utilizando o modelo](sql-database-vcore-resource-limits-elastic-pools.md) de compra vCore
- Para os limites de recursos dTU para piscinas elásticas, consulte [os limites de recursos para piscinas elásticas utilizando o modelo](sql-database-dtu-resource-limits-elastic-pools.md) de compra dTU
- Para os limites de recursos para casos geridos, consulte [os limites de recursos geridos](sql-database-managed-instance-resource-limits.md)por exemplo .
- Para obter informações sobre os limites gerais do Azure, consulte [os limites de subscrição e serviço do Azure, quotas e restrições.](../azure-resource-manager/management/azure-subscription-service-limits.md)
- Para obter informações sobre os limites de recursos num servidor de base de dados, consulte a [visão geral dos limites de recursos num servidor de base de dados SQL](sql-database-resource-limits-database-server.md) para obter informações sobre limites nos níveis de servidor e subscrição.
