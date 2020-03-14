---
title: limites de recursos vCore - piscinas elásticas
description: Esta página descreve alguns limites comuns de recursos vCore para piscinas elásticas na Base de Dados Azure SQL.
services: sql-database
ms.service: sql-database
ms.subservice: elastic-pools
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: oslake
ms.author: moslake
ms.reviewer: carlrab, sstein
ms.date: 03/03/2020
ms.openlocfilehash: a6186753c845070ff2a5b3a3f8c6ff0de51e52f0
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79255707"
---
# <a name="resource-limits-for-elastic-pools-using-the-vcore-purchasing-model"></a>Limites de recursos para piscinas elásticas utilizando o modelo de compra vCore

Este artigo fornece os limites de recursos detalhados para piscinas elásticas de base de dados Azure SQL e bases de dados reunidas utilizando o modelo de compra vCore.

Para os limites do modelo de compra da DTU, consulte os limites de recursos DTU da Base de [Dados SQL - piscinas elásticas](sql-database-dtu-resource-limits-elastic-pools.md).

> [!IMPORTANT]
> Em algumas circunstâncias, poderá ter reduzir uma base de dados para recuperar espaço não utilizado. Para mais informações, consulte Gerir o espaço de ficheiros na Base de [Dados Azure SQL](sql-database-file-space-management.md).

Pode definir o nível de serviço, o tamanho da computação e o valor de armazenamento utilizando o [portal Azure,](sql-database-elastic-pool-manage.md#azure-portal-manage-elastic-pools-and-pooled-databases) [PowerShell,](sql-database-elastic-pool-manage.md#powershell-manage-elastic-pools-and-pooled-databases)o [Azure CLI](sql-database-elastic-pool-manage.md#azure-cli-manage-elastic-pools-and-pooled-databases)ou o [REST API](sql-database-elastic-pool-manage.md#rest-api-manage-elastic-pools-and-pooled-databases).

> [!IMPORTANT]
> Para orientação e considerações de escala, consulte [Escala de uma piscina elástica](sql-database-elastic-pool-scale.md)

## <a name="general-purpose---provisioned-compute---gen4"></a>Finalidade geral - calculado - Gen4

> [!IMPORTANT]
> As novas bases de dados gen4 já não são suportadas nas regiões do Leste da Austrália ou do Brasil.

### <a name="general-purpose-service-tier-generation-4-compute-platform-part-1"></a>Nível de serviço para fins gerais: Plataforma computacional geração 4 (parte 1)

|Tamanho da computação|GP_Gen4_1|GP_Gen4_2|GP_Gen4_3|GP_Gen4_4|GP_Gen4_5|GP_Gen4_6
|:--- | --: |--: |--: |--: |--: |--: |
|Geração computacional|Gen4|Gen4|Gen4|Gen4|Gen4|Gen4|
|vCores|1|2|3|4|5|6|
|Memória (GB)|7|14|21|28|35|42|
|Número de DBs máximo por conjunto|100|200|500|500|500|500|
|Suporte de colunas|Sim|Sim|Sim|Sim|Sim|Sim|
|Armazenamento OLTP em memória (GB)|N/D|N/D|N/D|N/D|N/D|N/D|
|Tamanho máximo de dados (GB)|512|756|1536|1536|1536|2048|
|Tamanho máximo do diário de bordo|154|227|461|461|461|614|
|Tamanho dos dados máximos tempDB (GB)|32|64|96|128|160|192|
|Tipo de armazenamento|Armazenamento Premium (Remoto)|Armazenamento Premium (Remoto)|Armazenamento Premium (Remoto)|Armazenamento Premium (Remoto)|Armazenamento Premium (Remoto)|Armazenamento Premium (Remoto)|
|Latência de e/s (aproximada)|5-7 ms (escrever)<br>5-10 ms (ler)|5-7 ms (escrever)<br>5-10 ms (ler)|5-7 ms (escrever)<br>5-10 ms (ler)|5-7 ms (escrever)<br>5-10 ms (ler)|5-7 ms (escrever)<br>5-10 ms (ler)|5-7 ms (escrever)<br>5-10 ms (ler)|
|IOPS de dados max por piscina *|400|800|1200|1600|2000|2400|
|Taxa de registo máxima por piscina (MBps)|4.7|9.4|14.1|18.8|23.4|28.1|
|Trabalhadores simultâneos max por piscina (pedidos) ** |210|420|630|840|1050|1260|
|Logins simultâneos max por piscina ** |210|420|630|840|1050|1260|
|Sessões simultâneas máx.|30,000|30,000|30,000|30,000|30,000|30,000|
|Min/max piscina elástica vEscolhas de núcleo por base de dados|0, 0.25, 0.5, 1|0, 0.25, 0.5, 1, 2|0, 0.25, 0.5, 1...3|0, 0.25, 0.5, 1...4|0, 0.25, 0.5, 1...5|0, 0.25, 0.5, 1...6|
|Número de réplicas|1|1|1|1|1|1|
|Multi-AZ|N/D|N/D|N/D|N/D|N/D|N/D|
|Escalamento Horizontal de Leituras|N/D|N/D|N/D|N/D|N/D|N/D|
|Incluído armazenamento de backup|Tamanho 1X DB|Tamanho 1X DB|Tamanho 1X DB|Tamanho 1X DB|Tamanho 1X DB|Tamanho 1X DB|

\* O valor máximo para tamanhos io que variam entre 8 KB e 64 KB. Os IOPS reais são dependentes da carga de trabalho. Para mais detalhes, consulte [Data IO Governance](sql-database-resource-limits-database-server.md#resource-governance).

\*\* Para os trabalhadores simultâneos max (solicitações) para qualquer base de dados individual, consulte os limites de [recursos de base de dados individuais](sql-database-vcore-resource-limits-single-databases.md). Por exemplo, se o pool elástico estiver a utilizar a Gen5 e o vCore max por base de dados for fixado em 2, então o valor máximo dos trabalhadores simultâneos é de 200.  Se o vCore max por base de dados estiver definido para 0,5, então o valor máximo dos trabalhadores simultâneos é de 50, uma vez que na Gen5 existem um máximo de 100 trabalhadores simultâneos por vCore. Para outras definições vCore max por base de dados que sejam menos 1 vCore ou menos, o número de trabalhadores max simultâneos é igualmente redimensionado.

### <a name="general-purpose-service-tier-generation-4-compute-platform-part-2"></a>Nível de serviço para fins gerais: Plataforma computacional geração 4 (parte 2)

|Tamanho da computação|GP_Gen4_7|GP_Gen4_8|GP_Gen4_9|GP_Gen4_10|GP_Gen4_16|GP_Gen4_24|
|:--- | --: |--: |--: |--: |--: |--: |
|Geração computacional|Gen4|Gen4|Gen4|Gen4|Gen4|Gen4|
|vCores|7|8|9|10|16|24|
|Memória (GB)|49|56|63|70|112|159.5|
|Número de DBs máximo por conjunto|500|500|500|500|500|500|
|Suporte de colunas|Sim|Sim|Sim|Sim|Sim|Sim|
|Armazenamento OLTP em memória (GB)|N/D|N/D|N/D|N/D|N/D|N/D|
|Tamanho máximo de dados (GB)|2048|2048|2048|2048|3584|4096|
|Tamanho máximo do registo (GB)|614|614|614|614|1075|1229|
|Tamanho dos dados máximos tempDB (GB)|224|256|288|320|512|768|
|Tipo de armazenamento|Armazenamento Premium (Remoto)|Armazenamento Premium (Remoto)|Armazenamento Premium (Remoto)|Armazenamento Premium (Remoto)|Armazenamento Premium (Remoto)|Armazenamento Premium (Remoto)|
|Latência de e/s (aproximada)|5-7 ms (escrever)<br>5-10 ms (ler)|5-7 ms (escrever)<br>5-10 ms (ler)|5-7 ms (escrever)<br>5-10 ms (ler)|5-7 ms (escrever)<br>5-10 ms (ler)|5-7 ms (escrever)<br>5-10 ms (ler)|5-7 ms (escrever)<br>5-10 ms (ler)|
|IOPS de dados max por piscina *|2800|3200|3600|4000|6400|9600|
|Taxa de registo máxima por piscina (MBps)|32.8|37.5|37.5|37.5|37.5|37.5|
|Trabalhadores simultâneos max por piscina (pedidos) *|1470|1680|1890|2100|3360|5040|
|Piscina de logins simultânea max (pedidos) *|1470|1680|1890|2100|3360|5040|
|Sessões simultâneas máx.|30,000|30,000|30,000|30,000|30,000|30,000|
|Min/max piscina elástica vEscolhas de núcleo por base de dados|0, 0.25, 0.5, 1...7|0, 0.25, 0.5, 1...8|0, 0.25, 0.5, 1...9|0, 0.25, 0.5, 1...10|0, 0.25, 0.5, 1...10, 16|0, 0.25, 0.5, 1...10, 16, 24|
|Número de réplicas|1|1|1|1|1|1|
|Multi-AZ|N/D|N/D|N/D|N/D|N/D|N/D|
|Escalamento Horizontal de Leituras|N/D|N/D|N/D|N/D|N/D|N/D|
|Incluído armazenamento de backup|Tamanho 1X DB|Tamanho 1X DB|Tamanho 1X DB|Tamanho 1X DB|Tamanho 1X DB|Tamanho 1X DB|

\* Para os trabalhadores simultâneos max (pedidos) de qualquer base de dados individual, consulte os limites de [recursos de base de dados individuais](sql-database-vcore-resource-limits-single-databases.md). Por exemplo, se o pool elástico estiver a utilizar a Gen5 e o vCore max por base de dados for fixado em 2, então o valor máximo dos trabalhadores simultâneos é de 200.  Se o vCore max por base de dados estiver definido para 0,5, então o valor máximo dos trabalhadores simultâneos é de 50, uma vez que na Gen5 existem um máximo de 100 trabalhadores simultâneos por vCore. Para outras definições vCore max por base de dados que sejam menos 1 vCore ou menos, o número de trabalhadores max simultâneos é igualmente redimensionado.

## <a name="general-purpose---provisioned-compute---gen5"></a>Finalidade geral - calculado - Gen5

### <a name="general-purpose-service-tier-generation-5-compute-platform-part-1"></a>Nível de serviço para fins gerais: Plataforma computacional geração 5 (parte 1)

|Tamanho da computação|GP_Gen5_2|GP_Gen5_4|GP_Gen5_6|GP_Gen5_8|GP_Gen5_10|GP_Gen5_12|GP_Gen5_14|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|Geração computacional|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|
|vCores|2|4|6|8|10|12|14|
|Memória (GB)|10.4|20.8|31.1|41.5|51.9|62.3|72.7|
|Número de DBs máximo por conjunto|100|200|500|500|500|500|500|
|Suporte de colunas|Sim|Sim|Sim|Sim|Sim|Sim|Sim|
|Armazenamento OLTP em memória (GB)|N/D|N/D|N/D|N/D|N/D|N/D|N/D|
|Tamanho máximo de dados (GB)|512|756|1536|1536|1536|2048|2048|
|Tamanho máximo do registo (GB)|154|227|461|461|461|614|614|
|Tamanho dos dados máximos tempDB (GB)|64|128|192|256|320|384|448|
|Tipo de armazenamento|Armazenamento Premium (Remoto)|Armazenamento Premium (Remoto)|Armazenamento Premium (Remoto)|Armazenamento Premium (Remoto)|Armazenamento Premium (Remoto)|Armazenamento Premium (Remoto)|Armazenamento Premium (Remoto)|
|Latência de e/s (aproximada)|5-7 ms (escrever)<br>5-10 ms (ler)|5-7 ms (escrever)<br>5-10 ms (ler)|5-7 ms (escrever)<br>5-10 ms (ler)|5-7 ms (escrever)<br>5-10 ms (ler)|5-7 ms (escrever)<br>5-10 ms (ler)|5-7 ms (escrever)<br>5-10 ms (ler)|5-7 ms (escrever)<br>5-10 ms (ler)|
|IOPS de dados max por piscina *|800|1600|2400|3200|4000|4800|5600|
|Taxa de registo máxima por piscina (MBps)|9.4|18.8|28.1|37.5|37.5|37.5|37.5|
|Trabalhadores simultâneos max por piscina (pedidos) **|210|420|630|840|1050|1260|1470|
|Logins simultâneos max por piscina (pedidos) **|210|420|630|840|1050|1260|1470|
|Sessões simultâneas máx.|30,000|30,000|30,000|30,000|30,000|30,000|30,000|
|Min/max piscina elástica vEscolhas de núcleo por base de dados|0, 0.25, 0.5, 1, 2|0, 0.25, 0.5, 1...4|0, 0.25, 0.5, 1...6|0, 0.25, 0.5, 1...8|0, 0.25, 0.5, 1...10|0, 0.25, 0.5, 1...12|0, 0.25, 0.5, 1...14|
|Número de réplicas|1|1|1|1|1|1|1|
|Multi-AZ|N/D|N/D|N/D|N/D|N/D|N/D|N/D|
|Escalamento Horizontal de Leituras|N/D|N/D|N/D|N/D|N/D|N/D|N/D|
|Incluído armazenamento de backup|Tamanho 1X DB|Tamanho 1X DB|Tamanho 1X DB|Tamanho 1X DB|Tamanho 1X DB|Tamanho 1X DB|Tamanho 1X DB|

\* O valor máximo para tamanhos io que variam entre 8 KB e 64 KB. Os IOPS reais são dependentes da carga de trabalho. Para mais detalhes, consulte [Data IO Governance](sql-database-resource-limits-database-server.md#resource-governance).

\*\* Para os trabalhadores simultâneos max (solicitações) para qualquer base de dados individual, consulte os limites de [recursos de base de dados individuais](sql-database-vcore-resource-limits-single-databases.md). Por exemplo, se o pool elástico estiver a utilizar a Gen5 e o vCore max por base de dados for fixado em 2, então o valor máximo dos trabalhadores simultâneos é de 200.  Se o vCore max por base de dados estiver definido para 0,5, então o valor máximo dos trabalhadores simultâneos é de 50, uma vez que na Gen5 existem um máximo de 100 trabalhadores simultâneos por vCore. Para outras definições vCore max por base de dados que sejam menos 1 vCore ou menos, o número de trabalhadores max simultâneos é igualmente redimensionado.

### <a name="general-purpose-service-tier-generation-5-compute-platform-part-2"></a>Nível de serviço para fins gerais: Plataforma computacional geração 5 (parte 2)

|Tamanho da computação|GP_Gen5_16|GP_Gen5_18|GP_Gen5_20|GP_Gen5_24|GP_Gen5_32|GP_Gen5_40|GP_Gen5_80|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|Geração computacional|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|
|vCores|16|18|20|24|32|40|80|
|Memória (GB)|83|93.4|103.8|124.6|166.1|207.6|415.2|
|Número de DBs máximo por conjunto|500|500|500|500|500|500|500|
|Suporte de colunas|Sim|Sim|Sim|Sim|Sim|Sim|Sim|
|Armazenamento OLTP em memória (GB)|N/D|N/D|N/D|N/D|N/D|N/D|N/D|
|Tamanho máximo de dados (GB)|2048|3072|3072|3072|4096|4096|4096|
|Tamanho máximo do registo (GB)|614|922|922|922|1229|1229|1229|
|Tamanho dos dados máximos tempDB (GB)|512|576|640|768|1024|1280|2560|
|Tipo de armazenamento|Armazenamento Premium (Remoto)|Armazenamento Premium (Remoto)|Armazenamento Premium (Remoto)|Armazenamento Premium (Remoto)|Armazenamento Premium (Remoto)|Armazenamento Premium (Remoto)|Armazenamento Premium (Remoto)|
|Latência de e/s (aproximada)|5-7 ms (escrever)<br>5-10 ms (ler)|5-7 ms (escrever)<br>5-10 ms (ler)|5-7 ms (escrever)<br>5-10 ms (ler)|5-7 ms (escrever)<br>5-10 ms (ler)|5-7 ms (escrever)<br>5-10 ms (ler)|5-7 ms (escrever)<br>5-10 ms (ler)|5-7 ms (escrever)<br>5-10 ms (ler)|
|IOPS de dados max por piscina * |6,400|7,200|8,000|9,600|12,800|16,000|32,000|
|Taxa de registo máxima por piscina (MBps)|37.5|37.5|37.5|37.5|37.5|37.5|37.5|
|Trabalhadores simultâneos max por piscina (pedidos) **|1680|1890|2100|2520|3360|4200|8400|
|Logins simultâneos max por piscina (pedidos) **|1680|1890|2100|2520|3360|4200|8400|
|Sessões simultâneas máx.|30,000|30,000|30,000|30,000|30,000|30,000|30,000|
|Min/max piscina elástica vEscolhas de núcleo por base de dados|0, 0.25, 0.5, 1...16|0, 0.25, 0.5, 1...18|0, 0.25, 0.5, 1...20|0, 0.25, 0.5, 1...20, 24|0, 0.25, 0.5, 1...20, 24, 32|0, 0.25, 0.5, 1...16, 24, 32, 40|0, 0.25, 0.5, 1...16, 24, 32, 40, 80|
|Número de réplicas|1|1|1|1|1|1|1|
|Multi-AZ|N/D|N/D|N/D|N/D|N/D|N/D|N/D|
|Escalamento Horizontal de Leituras|N/D|N/D|N/D|N/D|N/D|N/D|N/D|
|Incluído armazenamento de backup|Tamanho 1X DB|Tamanho 1X DB|Tamanho 1X DB|Tamanho 1X DB|Tamanho 1X DB|Tamanho 1X DB|Tamanho 1X DB|

\* O valor máximo para tamanhos io que variam entre 8 KB e 64 KB. Os IOPS reais são dependentes da carga de trabalho. Para mais detalhes, consulte [Data IO Governance](sql-database-resource-limits-database-server.md#resource-governance).

\*\* Para os trabalhadores simultâneos max (solicitações) para qualquer base de dados individual, consulte os limites de [recursos de base de dados individuais](sql-database-vcore-resource-limits-single-databases.md). Por exemplo, se o pool elástico estiver a utilizar a Gen5 e o vCore max por base de dados for fixado em 2, então o valor máximo dos trabalhadores simultâneos é de 200.  Se o vCore max por base de dados estiver definido para 0,5, então o valor máximo dos trabalhadores simultâneos é de 50, uma vez que na Gen5 existem um máximo de 100 trabalhadores simultâneos por vCore. Para outras definições vCore max por base de dados que sejam menos 1 vCore ou menos, o número de trabalhadores max simultâneos é igualmente redimensionado.

## <a name="general-purpose---provisioned-compute---fsv2-series"></a>Finalidade geral - calculado - Série Fsv2

### <a name="fsv2-series-compute-generation-preview"></a>Geração de computação da série Fsv2 (pré-visualização)

|Tamanho da computação|GP_Fsv2_72|
|:--- | --: |
|Geração computacional|Série Fsv2|
|vCores|72|
|Memória (GB)|136.2|
|Número de DBs máximo por conjunto|500|
|Suporte de colunas|Sim|
|Armazenamento OLTP em memória (GB)|N/D|
|Tamanho máximo de dados (GB)|4096|
|Tamanho máximo do registo (GB)|1024|
|Tamanho dos dados máximos tempDB (GB)|333|
|Tipo de armazenamento|Armazenamento Premium (Remoto)|
|Latência de e/s (aproximada)|5-7 ms (escrever)<br>5-10 ms (ler)|
|IOPS de dados max por piscina *|16,000|
|Taxa de registo máxima por piscina (MBps)|37.5|
|Trabalhadores simultâneos max por piscina (pedidos) **|3780|
|Logins simultâneos max por piscina (pedidos) **|3780|
|Sessões simultâneas máx.|30,000|
|Min/max piscina elástica vEscolhas de núcleo por base de dados|0-72|
|Número de réplicas|1|
|Multi-AZ|N/D|
|Escalamento Horizontal de Leituras|N/D|
|Incluído armazenamento de backup|Tamanho 1X DB|

\* O valor máximo para tamanhos io que variam entre 8 KB e 64 KB. Os IOPS reais são dependentes da carga de trabalho. Para mais detalhes, consulte [Data IO Governance](sql-database-resource-limits-database-server.md#resource-governance).

\*\* Para os trabalhadores simultâneos max (solicitações) para qualquer base de dados individual, consulte os limites de [recursos de base de dados individuais](sql-database-vcore-resource-limits-single-databases.md). Por exemplo, se o pool elástico estiver a utilizar a Gen5 e o vCore max por base de dados for fixado em 2, então o valor máximo dos trabalhadores simultâneos é de 200.  Se o vCore max por base de dados estiver definido para 0,5, então o valor máximo dos trabalhadores simultâneos é de 50, uma vez que na Gen5 existem um máximo de 100 trabalhadores simultâneos por vCore. Para outras definições vCore max por base de dados que sejam menos 1 vCore ou menos, o número de trabalhadores max simultâneos é igualmente redimensionado.

## <a name="business-critical---provisioned-compute---gen4"></a>Business critical - calculado - Gen4

> [!IMPORTANT]
> As novas bases de dados gen4 já não são suportadas nas regiões do Leste da Austrália ou do Brasil.

### <a name="business-critical-service-tier-generation-4-compute-platform-part-1"></a>Nível de serviço crítico do negócio: Plataforma computacional geração 4 (parte 1)

|Tamanho da computação|BC_Gen4_2|BC_Gen4_3|BC_Gen4_4|BC_Gen4_5|BC_Gen4_6|
|:--- | --: |--: |--: |--: |--: |--: |
|Geração computacional|Gen4|Gen4|Gen4|Gen4|Gen4|
|vCores|2|3|4|5|6|
|Memória (GB)|14|21|28|35|42|
|Número de DBs máximo por conjunto|50|100|100|100|100|
|Suporte de colunas|Sim|Sim|Sim|Sim|Sim|
|Armazenamento OLTP em memória (GB)|2|3|4|5|6|
|Tipo de armazenamento|Local SSD|Local SSD|Local SSD|Local SSD|Local SSD|
|Tamanho máximo de dados (GB)|1024|1024|1024|1024|1024|
|Tamanho máximo do registo (GB)|307|307|307|307|307|
|Tamanho dos dados máximos tempDB (GB)|64|96|128|160|192|
|Latência de e/s (aproximada)|1-2 ms (escrever)<br>1-2 ms (ler)|1-2 ms (escrever)<br>1-2 ms (ler)|1-2 ms (escrever)<br>1-2 ms (ler)|1-2 ms (escrever)<br>1-2 ms (ler)|1-2 ms (escrever)<br>1-2 ms (ler)|
|IOPS de dados max por piscina *|9,000|13,500|18,000|22,500|27,000|
|Taxa de registo máxima por piscina (MBps)|20|30|40|50|60|
|Trabalhadores simultâneos max por piscina (pedidos) **|420|630|840|1050|1260|
|Logins simultâneos max por piscina (pedidos) **|420|630|840|1050|1260|
|Sessões simultâneas máx.|30,000|30,000|30,000|30,000|30,000|
|Min/max piscina elástica vEscolhas de núcleo por base de dados|0, 0.25, 0.5, 1, 2|0, 0.25, 0.5, 1...3|0, 0.25, 0.5, 1...4|0, 0.25, 0.5, 1...5|0, 0.25, 0.5, 1...6|
|Número de réplicas|4|4|4|4|4|
|Multi-AZ|Sim|Sim|Sim|Sim|Sim|
|Escalamento Horizontal de Leituras|Sim|Sim|Sim|Sim|Sim|
|Incluído armazenamento de backup|Tamanho 1X DB|Tamanho 1X DB|Tamanho 1X DB|Tamanho 1X DB|Tamanho 1X DB|

\* O valor máximo para tamanhos io que variam entre 8 KB e 64 KB. Os IOPS reais são dependentes da carga de trabalho. Para mais detalhes, consulte [Data IO Governance](sql-database-resource-limits-database-server.md#resource-governance).

\*\* Para os trabalhadores simultâneos max (solicitações) para qualquer base de dados individual, consulte os limites de [recursos de base de dados individuais](sql-database-vcore-resource-limits-single-databases.md). Por exemplo, se o pool elástico estiver a utilizar a Gen5 e o vCore max por base de dados for fixado em 2, então o valor máximo dos trabalhadores simultâneos é de 200.  Se o vCore max por base de dados estiver definido para 0,5, então o valor máximo dos trabalhadores simultâneos é de 50, uma vez que na Gen5 existem um máximo de 100 trabalhadores simultâneos por vCore. Para outras definições vCore max por base de dados que sejam menos 1 vCore ou menos, o número de trabalhadores max simultâneos é igualmente redimensionado.

### <a name="business-critical-service-tier-generation-4-compute-platform-part-2"></a>Nível de serviço crítico do negócio: Plataforma computacional geração 4 (parte 2)

|Tamanho da computação|BC_Gen4_7|BC_Gen4_8|BC_Gen4_9|BC_Gen4_10|BC_Gen4_16|BC_Gen4_24|
|:--- | --: |--: |--: |--: |--: |--: |
|Geração computacional|Gen4|Gen4|Gen4|Gen4|Gen4|Gen4|
|vCores|7|8|9|10|16|24|
|Memória (GB)|49|56|63|70|112|159.5|
|Número de DBs máximo por conjunto|100|100|100|100|100|100|
|Suporte de colunas|N/D|N/D|N/D|N/D|N/D|N/D|
|Armazenamento OLTP em memória (GB)|7|8|9.5|11|20|36|
|Tipo de armazenamento|Local SSD|Local SSD|Local SSD|Local SSD|Local SSD|Local SSD|
|Tamanho máximo de dados (GB)|1024|1024|1024|1024|1024|1024|
|Tamanho máximo do registo (GB)|307|307|307|307|307|307|
|Tamanho dos dados máximos tempDB (GB)|224|256|288|320|512|768|
|Latência de e/s (aproximada)|1-2 ms (escrever)<br>1-2 ms (ler)|1-2 ms (escrever)<br>1-2 ms (ler)|1-2 ms (escrever)<br>1-2 ms (ler)|1-2 ms (escrever)<br>1-2 ms (ler)|1-2 ms (escrever)<br>1-2 ms (ler)|1-2 ms (escrever)<br>1-2 ms (ler)|
|IOPS de dados max por piscina *|31,500|36,000|40,500|45,000|72,000|96,000|
|Taxa de registo máxima por piscina (MBps)|70|80|80|80|80|80|
|Trabalhadores simultâneos max por piscina (pedidos) **|1470|1680|1890|2100|3360|5040|
|Logins simultâneos max por piscina (pedidos) **|1470|1680|1890|2100|3360|5040|
|Sessões simultâneas máx.|30,000|30,000|30,000|30,000|30,000|30,000|
|Min/max piscina elástica vEscolhas de núcleo por base de dados|0, 0.25, 0.5, 1...7|0, 0.25, 0.5, 1...8|0, 0.25, 0.5, 1...9|0, 0.25, 0.5, 1...10|0, 0.25, 0.5, 1...10, 16|0, 0.25, 0.5, 1...10, 16, 24|
|Número de réplicas|4|4|4|4|4|4|
|Multi-AZ|Sim|Sim|Sim|Sim|Sim|Sim|
|Escalamento Horizontal de Leituras|Sim|Sim|Sim|Sim|Sim|Sim|
|Incluído armazenamento de backup|Tamanho 1X DB|Tamanho 1X DB|Tamanho 1X DB|Tamanho 1X DB|Tamanho 1X DB|Tamanho 1X DB|

\* O valor máximo para tamanhos io que variam entre 8 KB e 64 KB. Os IOPS reais são dependentes da carga de trabalho. Para mais detalhes, consulte [Data IO Governance](sql-database-resource-limits-database-server.md#resource-governance).

\*\* Para os trabalhadores simultâneos max (solicitações) para qualquer base de dados individual, consulte os limites de [recursos de base de dados individuais](sql-database-vcore-resource-limits-single-databases.md). Por exemplo, se o pool elástico estiver a utilizar a Gen5 e o vCore max por base de dados for fixado em 2, então o valor máximo dos trabalhadores simultâneos é de 200.  Se o vCore max por base de dados estiver definido para 0,5, então o valor máximo dos trabalhadores simultâneos é de 50, uma vez que na Gen5 existem um máximo de 100 trabalhadores simultâneos por vCore. Para outras definições vCore max por base de dados que sejam menos 1 vCore ou menos, o número de trabalhadores max simultâneos é igualmente redimensionado.

## <a name="business-critical---provisioned-compute---gen5"></a>Business critical - calculado - Gen5

### <a name="business-critical-service-tier-generation-5-compute-platform-part-1"></a>Nível de serviço crítico do negócio: Plataforma computacional geração 5 (parte 1)

|Tamanho da computação|BC_Gen5_4|BC_Gen5_6|BC_Gen5_8|BC_Gen5_10|BC_Gen5_12|BC_Gen5_14|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|Geração computacional|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|
|vCores|4|6|8|10|12|14|
|Memória (GB)|20.8|31.1|41.5|51.9|62.3|72.7|
|Número de DBs máximo por conjunto|50|100|100|100|100|100|
|Suporte de colunas|Sim|Sim|Sim|Sim|Sim|Sim|
|Armazenamento OLTP em memória (GB)|3.14|4.71|6.28|8.65|11.02|13.39|
|Tamanho máximo de dados (GB)|1024|1536|1536|1536|3072|3072|
|Tamanho máximo do registo (GB)|307|307|461|461|922|922|
|Tamanho dos dados máximos tempDB (GB)|128|192|256|320|384|448|
|Tipo de armazenamento|Local SSD|Local SSD|Local SSD|Local SSD|Local SSD|Local SSD|
|Latência de e/s (aproximada)|1-2 ms (escrever)<br>1-2 ms (ler)|1-2 ms (escrever)<br>1-2 ms (ler)|1-2 ms (escrever)<br>1-2 ms (ler)|1-2 ms (escrever)<br>1-2 ms (ler)|1-2 ms (escrever)<br>1-2 ms (ler)|1-2 ms (escrever)<br>1-2 ms (ler)|
|IOPS de dados max por piscina *|18,000|27,000|36,000|45,000|54,000|63,000|
|Taxa de registo máxima por piscina (MBps)|60|90|120|120|120|120|
|Trabalhadores simultâneos max por piscina (pedidos) **|420|630|840|1050|1260|1470|
|Logins simultâneos max por piscina (pedidos) **|420|630|840|1050|1260|1470|
|Sessões simultâneas máx.|30,000|30,000|30,000|30,000|30,000|30,000|
|Min/max piscina elástica vEscolhas de núcleo por base de dados|0, 0.25, 0.5, 1...4|0, 0.25, 0.5, 1...6|0, 0.25, 0.5, 1...8|0, 0.25, 0.5, 1...10|0, 0.25, 0.5, 1...12|0, 0.25, 0.5, 1...14|
|Número de réplicas|4|4|4|4|4|4|
|Multi-AZ|Sim|Sim|Sim|Sim|Sim|Sim|
|Escalamento Horizontal de Leituras|Sim|Sim|Sim|Sim|Sim|Sim|
|Incluído armazenamento de backup|Tamanho 1X DB|Tamanho 1X DB|Tamanho 1X DB|Tamanho 1X DB|Tamanho 1X DB|Tamanho 1X DB|

\* O valor máximo para tamanhos io que variam entre 8 KB e 64 KB. Os IOPS reais são dependentes da carga de trabalho. Para mais detalhes, consulte [Data IO Governance](sql-database-resource-limits-database-server.md#resource-governance).

\*\* Para os trabalhadores simultâneos max (solicitações) para qualquer base de dados individual, consulte os limites de [recursos de base de dados individuais](sql-database-vcore-resource-limits-single-databases.md). Por exemplo, se o pool elástico estiver a utilizar a Gen5 e o vCore max por base de dados for fixado em 2, então o valor máximo dos trabalhadores simultâneos é de 200.  Se o vCore max por base de dados estiver definido para 0,5, então o valor máximo dos trabalhadores simultâneos é de 50, uma vez que na Gen5 existem um máximo de 100 trabalhadores simultâneos por vCore. Para outras definições vCore max por base de dados que sejam menos 1 vCore ou menos, o número de trabalhadores max simultâneos é igualmente redimensionado.

### <a name="business-critical-service-tier-generation-5-compute-platform-part-2"></a>Nível de serviço crítico do negócio: Plataforma computacional geração 5 (parte 2)

|Tamanho da computação|BC_Gen5_16|BC_Gen5_18|BC_Gen5_20|BC_Gen5_24|BC_Gen5_32|BC_Gen5_40|BC_Gen5_80|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|Geração computacional|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|
|vCores|16|18|20|24|32|40|80|
|Memória (GB)|83|93.4|103.8|124.6|166.1|207.6|415.2|
|Número de DBs máximo por conjunto|100|100|100|100|100|100|100|
|Suporte de colunas|Sim|Sim|Sim|Sim|Sim|Sim|Sim|
|Armazenamento OLTP em memória (GB)|15.77|18.14|20.51|25.25|37.94|52.23|131.68|
|Tamanho máximo de dados (GB)|3072|3072|3072|4096|4096|4096|4096|
|Tamanho máximo do registo (GB)|922|922|922|1229|1229|1229|1229|
|Tamanho dos dados máximos tempDB (GB)|512|576|640|768|1024|1280|2560|
|Tipo de armazenamento|Local SSD|Local SSD|Local SSD|Local SSD|Local SSD|Local SSD|Local SSD|
|Latência de e/s (aproximada)|1-2 ms (escrever)<br>1-2 ms (ler)|1-2 ms (escrever)<br>1-2 ms (ler)|1-2 ms (escrever)<br>1-2 ms (ler)|1-2 ms (escrever)<br>1-2 ms (ler)|1-2 ms (escrever)<br>1-2 ms (ler)|1-2 ms (escrever)<br>1-2 ms (ler)|1-2 ms (escrever)<br>1-2 ms (ler)|
|IOPS de dados max por piscina *|72,000|81,000|90,000|108,000|144,000|180,000|256,000|
|Taxa de registo máxima por piscina (MBps)|120|120|120|120|120|120|120|
|Trabalhadores simultâneos max por piscina (pedidos) **|1680|1890|2100|2520|3360|4200|8400|
|Logins simultâneos max por piscina (pedidos) **|1680|1890|2100|2520|3360|4200|8400|
|Sessões simultâneas máx.|30,000|30,000|30,000|30,000|30,000|30,000|30,000|
|Min/max piscina elástica vEscolhas de núcleo por base de dados|0, 0.25, 0.5, 1...16|0, 0.25, 0.5, 1...18|0, 0.25, 0.5, 1...20|0, 0.25, 0.5, 1...20, 24|0, 0.25, 0.5, 1...20, 24, 32|0, 0.25, 0.5, 1...20, 24, 32, 40|0, 0.25, 0.5, 1...20, 24, 32, 40, 80|
|Número de réplicas|4|4|4|4|4|4|4|
|Multi-AZ|Sim|Sim|Sim|Sim|Sim|Sim|Sim|
|Escalamento Horizontal de Leituras|Sim|Sim|Sim|Sim|Sim|Sim|Sim|
|Incluído armazenamento de backup|Tamanho 1X DB|Tamanho 1X DB|Tamanho 1X DB|Tamanho 1X DB|Tamanho 1X DB|Tamanho 1X DB|Tamanho 1X DB|

\* O valor máximo para tamanhos io que variam entre 8 KB e 64 KB. Os IOPS reais são dependentes da carga de trabalho. Para mais detalhes, consulte [Data IO Governance](sql-database-resource-limits-database-server.md#resource-governance).

\*\* Para os trabalhadores simultâneos max (solicitações) para qualquer base de dados individual, consulte os limites de [recursos de base de dados individuais](sql-database-vcore-resource-limits-single-databases.md). Por exemplo, se o pool elástico estiver a utilizar a Gen5 e o vCore max por base de dados for fixado em 2, então o valor máximo dos trabalhadores simultâneos é de 200.  Se o vCore max por base de dados estiver definido para 0,5, então o valor máximo dos trabalhadores simultâneos é de 50, uma vez que na Gen5 existem um máximo de 100 trabalhadores simultâneos por vCore. Para outras definições vCore max por base de dados que sejam menos 1 vCore ou menos, o número de trabalhadores max simultâneos é igualmente redimensionado.

## <a name="business-critical---provisioned-compute---m-series"></a>Business critical - calculado - Série M

### <a name="m-series-compute-generation-preview"></a>Geração de computação da série M (pré-visualização)

|Tamanho da computação|BC_M_128|
|:--- | --: |
|Geração computacional|Série M|
|vCores|128|
|Memória (GB)|3767.1|
|Número de DBs máximo por conjunto|100|
|Suporte de colunas|Sim|
|Armazenamento OLTP em memória (GB)|1768|
|Tamanho máximo de dados (GB)|4096|
|Tamanho máximo do registo (GB)|2048|
|Tamanho dos dados máximos tempDB (GB)|4096|
|Tipo de armazenamento|Local SSD|
|Latência de e/s (aproximada)|1-2 ms (escrever)<br>1-2 ms (ler)|
|IOPS de dados max por piscina *|200,000|
|Taxa de registo máxima por piscina (MBps)|333|
|Trabalhadores simultâneos max por piscina (pedidos) *|13,440|
|Logins simultâneos max por piscina (pedidos) *|13,440|
|Sessões simultâneas máx.|30,000|
|Min/max piscina elástica vEscolhas de núcleo por base de dados|0-128|
|Número de réplicas|4|
|Multi-AZ|Sim|
|Escalamento Horizontal de Leituras|Sim|
|Incluído armazenamento de backup|Tamanho 1X DB|

\* O valor máximo para tamanhos io que variam entre 8 KB e 64 KB. Os IOPS reais são dependentes da carga de trabalho. Para mais detalhes, consulte [Data IO Governance](sql-database-resource-limits-database-server.md#resource-governance).

\*\* Para os trabalhadores simultâneos max (solicitações) para qualquer base de dados individual, consulte os limites de [recursos de base de dados individuais](sql-database-vcore-resource-limits-single-databases.md). Por exemplo, se o pool elástico estiver a utilizar a Gen5 e o vCore max por base de dados for fixado em 2, então o valor máximo dos trabalhadores simultâneos é de 200.  Se o vCore max por base de dados estiver definido para 0,5, então o valor máximo dos trabalhadores simultâneos é de 50, uma vez que na Gen5 existem um máximo de 100 trabalhadores simultâneos por vCore. Para outras definições vCore max por base de dados que sejam menos 1 vCore ou menos, o número de trabalhadores max simultâneos é igualmente redimensionado.

Se todos os vCores de uma piscina elástica estiverem ocupados, então cada base de dados da piscina recebe uma quantidade igual de recursos computacionais para processar consultas. O serviço Base de Dados SQL fornece equidade de partilha de recursos entre bases de dados, garantindo frações iguais de tempo de computação. A equidade de partilha de recursos de piscina elástica é além de qualquer quantidade de recursos de outra forma garantida a cada base de dados quando o vCore min por base de dados é definido para um valor não zero.

## <a name="database-properties-for-pooled-databases"></a>Propriedades de base de dados para bases de dados reunidas

A tabela seguinte descreve as propriedades para bases de dados reunidas.

> [!NOTE]
> Os limites de recursos das bases de dados individuais em piscinas elásticas são geralmente os mesmos que para bases de dados individuais fora de piscinas que têm o mesmo tamanho de computação. Por exemplo, os trabalhadores simultâneos máximos para uma base de dados GP_Gen4_1 são 200 trabalhadores. Assim, os trabalhadores simultâneos máximos para uma base de dados numa piscina de GP_Gen4_1 são também 200 trabalhadores. Nota, o número total de trabalhadores simultâneos na piscina GP_Gen4_1 é de 210.

| Propriedade | Descrição |
|:--- |:--- |
| Max vCores por base de dados |O número máximo de vCores que qualquer base de dados na piscina pode utilizar, se disponível com base na utilização por outras bases de dados na piscina. Max vCores por base de dados não é uma garantia de recursos para uma base de dados. Esta definição é uma definição global que se aplica a todas as bases de dados no conjunto. Detete max vCores por base de dados alto o suficiente para lidar com picos na utilização da base de dados. Espera-se um certo grau de sobre-commissão, uma vez que a piscina geralmente assume padrões de utilização quente e frio para bases de dados onde todas as bases de dados não estão simultaneamente no pico.|
| Min vCores por base de dados |O número mínimo de vCores que qualquer base de dados na piscina é garantido. Esta definição é uma definição global que se aplica a todas as bases de dados no conjunto. O min vCores por base de dados pode ser definido para 0, e é também o valor padrão. Esta propriedade está definida para qualquer lugar entre 0 e a utilização média de vCores por base de dados. O produto do número de bases de dados na piscina e do min vCores por base de dados não pode exceder os vCores por piscina.|
| Armazenamento máximo por base de dados |O tamanho máximo da base de dados definido pelo utilizador para uma base de dados numa piscina. As bases de dados agrofadas partilham o armazenamento de piscinas, pelo que o tamanho que uma base de dados pode alcançar está limitado ao menor do tamanho restante do armazenamento da piscina e da base de dados. O tamanho da base de dados max refere-se ao tamanho máximo dos ficheiros de dados e não inclui o espaço utilizado pelos ficheiros de registo. |
|||

## <a name="next-steps"></a>Passos seguintes

- Para os limites de recursos vCore para uma única base de dados, consulte [os limites de recursos para bases de dados únicas utilizando o modelo](sql-database-vcore-resource-limits-single-databases.md) de compra vCore
- Para os limites de recursos dTU para uma única base de dados, consulte [os limites de recursos para bases de dados únicas utilizando o modelo](sql-database-dtu-resource-limits-single-databases.md) de compra dTU
- Para os limites de recursos dTU para piscinas elásticas, consulte [os limites de recursos para piscinas elásticas utilizando o modelo](sql-database-dtu-resource-limits-elastic-pools.md) de compra dTU
- Para os limites de recursos para casos geridos, consulte [os limites de recursos geridos](sql-database-managed-instance-resource-limits.md)por exemplo .
- Para obter informações sobre os limites gerais do Azure, consulte [os limites de subscrição e serviço do Azure, quotas e restrições.](../azure-resource-manager/management/azure-subscription-service-limits.md)
- Para obter informações sobre os limites de recursos num servidor de base de dados, consulte a [visão geral dos limites de recursos num servidor de base de dados SQL](sql-database-resource-limits-database-server.md) para obter informações sobre limites nos níveis de servidor e subscrição.
