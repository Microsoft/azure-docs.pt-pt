---
title: Limites de recursos vCore de conjunto elástico
description: Esta página descreve alguns limites de recursos vCore comuns para piscinas elásticas na Base de Dados Azure SQL.
services: sql-database
ms.service: sql-database
ms.subservice: elastic-pools
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: oslake
ms.author: moslake
ms.reviewer: carlrab, sstein
ms.date: 05/29/2020
ms.openlocfilehash: 03884a7d1b834ef8c176434ad4127e1638aabcb1
ms.sourcegitcommit: f1132db5c8ad5a0f2193d751e341e1cd31989854
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/31/2020
ms.locfileid: "84235738"
---
# <a name="resource-limits-for-elastic-pools-using-the-vcore-purchasing-model"></a>Limites de recursos para piscinas elásticas utilizando o modelo de compra vCore
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Este artigo fornece os limites de recursos detalhados para piscinas elásticas Azure SQL Database e bases de dados em conjunto utilizando o modelo de compra vCore.

Para os limites do modelo de compra de DTU, consulte [os limites de recursos DTU da Base de Dados SQL - piscinas elásticas](resource-limits-dtu-elastic-pools.md).

> [!IMPORTANT]
> Em algumas circunstâncias, pode precisar de encolher uma base de dados para recuperar o espaço não usused. Para obter mais informações, consulte [Gerir o espaço de ficheiros na Base de Dados Azure SQL](file-space-manage.md).

Pode definir o nível de serviço, o tamanho do cálculo e a quantidade de armazenamento utilizando o [portal Azure](elastic-pool-manage.md#azure-portal), [PowerShell,](elastic-pool-manage.md#powershell) [O CLI Azure,](elastic-pool-manage.md#azure-cli)ou a [API REST](elastic-pool-manage.md#rest-api).

> [!IMPORTANT]
> Para obter orientação e considerações de escalonamento, consulte [Scale a elastic pool](elastic-pool-scale.md).

## <a name="general-purpose---provisioned-compute---gen4"></a>Finalidade geral - computação a provisionada - Gen4

> [!IMPORTANT]
> As novas bases de dados da Gen4 já não são suportadas nas regiões leste ou sul da Austrália.

### <a name="general-purpose-service-tier-generation-4-compute-platform-part-1"></a>Nível de serviço para fins gerais: Plataforma computacional geração 4 (parte 1)

|Tamanho do cálculo|GP_Gen4_1|GP_Gen4_2|GP_Gen4_3|GP_Gen4_4|GP_Gen4_5|GP_Gen4_6
|:--- | --: |--: |--: |--: |--: |--: |
|Geração computacional|Gen4|Gen4|Gen4|Gen4|Gen4|Gen4|
|vCores|1|2|3|4|5|6|
|Memória (GB)|7|14|21|28|35|42|
|DBs max por piscina <sup>1</sup>|100|200|500|500|500|500|
|Suporte de loja de colunas|Sim|Sim|Sim|Sim|Sim|Sim|
|Armazenamento OLTP na memória (GB)|N/D|N/D|N/D|N/D|N/D|N/D|
|Tamanho máximo dos dados (GB)|512|756|1536|1536|1536|2048|
|Tamanho do tronco máximo|154|227|461|461|461|614|
|Tamanho máximo de dados tempDB (GB)|32|64|96|128|160|192|
|Tipo de armazenamento|Armazenamento Premium (Remoto)|Armazenamento Premium (Remoto)|Armazenamento Premium (Remoto)|Armazenamento Premium (Remoto)|Armazenamento Premium (Remoto)|Armazenamento Premium (Remoto)|
|Latência IO (aproximada)|5-7 ms (escrever)<br>5-10 ms (ler)|5-7 ms (escrever)<br>5-10 ms (ler)|5-7 ms (escrever)<br>5-10 ms (ler)|5-7 ms (escrever)<br>5-10 ms (ler)|5-7 ms (escrever)<br>5-10 ms (ler)|5-7 ms (escrever)<br>5-10 ms (ler)|
|Dados máximos IOPS por pool <sup>2</sup> |400|800|1200|1600|2000|2400|
|Taxa máxima de registo por piscina (MBps)|4.7|9.4|14.1|18.8|23.4|28.1|
|Trabalhadores máximos simultâneos por piscina (pedidos) <sup>3</sup> |210|420|630|840|1050|1260|
|Logins simultâneos max por piscina <sup>3</sup> |210|420|630|840|1050|1260|
|Sessões simultâneas máx.|30,000|30,000|30,000|30,000|30,000|30,000|
|Min/max elástico piscina vCore escolhas por base de dados|0, 0.25, 0.5, 1|0, 0.25, 0.5, 1, 2|0, 0.25, 0.5, 1...3|0, 0.25, 0.5, 1...4|0, 0.25, 0.5, 1...5|0, 0.25, 0.5, 1...6|
|Número de réplicas|1|1|1|1|1|1|
|Multi-AZ|N/D|N/D|N/D|N/D|N/D|N/D|
|Escalamento Horizontal de Leituras|N/D|N/D|N/D|N/D|N/D|N/D|
|Armazenamento de backup incluído|Tamanho 1X DB|Tamanho 1X DB|Tamanho 1X DB|Tamanho 1X DB|Tamanho 1X DB|Tamanho 1X DB|

<sup>1</sup> Consulte [a gestão de recursos em piscinas elásticas densas](elastic-pool-resource-management.md) para obter considerações adicionais.

<sup>2</sup> O valor máximo para os tamanhos de IO que variam entre 8 KB e 64 KB. Os IOPS reais dependem da carga de trabalho. Para mais informações, consulte [a Governação do IO de Dados.](resource-limits-logical-server.md#resource-governance)

<sup>3</sup> Para os trabalhadores máximos simultâneos (pedidos) para qualquer base de dados individual, consulte [os limites de recursos de base de dados individuais](resource-limits-vcore-single-databases.md). Por exemplo, se a piscina elástica estiver a utilizar a Gen5 e o max vCore por base de dados for definido em 2, então o valor máximo simultâneo dos trabalhadores é de 200.  Se o máximo vCore por base de dados for definido para 0,5, então o valor máximo simultâneo dos trabalhadores é de 50, uma vez que na Gen5 existe um máximo de 100 trabalhadores simultâneos por vCore. Para outras definições max vCore por base de dados que sejam inferiores a 1 vCore ou menos, o número de trabalhadores máximos simultâneos é igualmente redimensionado.

### <a name="general-purpose-service-tier-generation-4-compute-platform-part-2"></a>Nível de serviço para fins gerais: Plataforma computacional geração 4 (parte 2)

|Tamanho do cálculo|GP_Gen4_7|GP_Gen4_8|GP_Gen4_9|GP_Gen4_10|GP_Gen4_16|GP_Gen4_24|
|:--- | --: |--: |--: |--: |--: |--: |
|Geração computacional|Gen4|Gen4|Gen4|Gen4|Gen4|Gen4|
|vCores|7|8|9|10|16|24|
|Memória (GB)|49|56|63|70|112|159.5|
|DBs max por piscina <sup>1</sup>|500|500|500|500|500|500|
|Suporte de loja de colunas|Sim|Sim|Sim|Sim|Sim|Sim|
|Armazenamento OLTP na memória (GB)|N/D|N/D|N/D|N/D|N/D|N/D|
|Tamanho máximo dos dados (GB)|2048|2048|2048|2048|3584|4096|
|Tamanho do tronco máximo (GB)|614|614|614|614|1075|1229|
|Tamanho máximo de dados tempDB (GB)|224|256|288|320|512|768|
|Tipo de armazenamento|Armazenamento Premium (Remoto)|Armazenamento Premium (Remoto)|Armazenamento Premium (Remoto)|Armazenamento Premium (Remoto)|Armazenamento Premium (Remoto)|Armazenamento Premium (Remoto)|
|Latência IO (aproximada)|5-7 ms (escrever)<br>5-10 ms (ler)|5-7 ms (escrever)<br>5-10 ms (ler)|5-7 ms (escrever)<br>5-10 ms (ler)|5-7 ms (escrever)<br>5-10 ms (ler)|5-7 ms (escrever)<br>5-10 ms (ler)|5-7 ms (escrever)<br>5-10 ms (ler)|
|Dados máximos IOPS por pool <sup>2</sup>|2800|3200|3600|4000|6400|9600|
|Taxa máxima de registo por piscina (MBps)|32.8|37.5|37.5|37.5|37.5|37.5|
|Trabalhadores máximos simultâneos por piscina (pedidos) <sup>3</sup>|1470|1680|1890|2100|3360|5040|
|Piscina de logins em simultâneo (pedidos) <sup>3</sup>|1470|1680|1890|2100|3360|5040|
|Sessões simultâneas máx.|30,000|30,000|30,000|30,000|30,000|30,000|
|Min/max elástico piscina vCore escolhas por base de dados|0, 0.25, 0.5, 1...7|0, 0.25, 0.5, 1...8|0, 0.25, 0.5, 1...9|0, 0.25, 0.5, 1...10|0, 0.25, 0.5, 1...10, 16|0, 0.25, 0.5, 1...10, 16, 24|
|Número de réplicas|1|1|1|1|1|1|
|Multi-AZ|N/D|N/D|N/D|N/D|N/D|N/D|
|Escalamento Horizontal de Leituras|N/D|N/D|N/D|N/D|N/D|N/D|
|Armazenamento de backup incluído|Tamanho 1X DB|Tamanho 1X DB|Tamanho 1X DB|Tamanho 1X DB|Tamanho 1X DB|Tamanho 1X DB|

<sup>1</sup> Consulte [a gestão de recursos em piscinas elásticas densas](elastic-pool-resource-management.md) para obter considerações adicionais.

<sup>2</sup> O valor máximo para os tamanhos de IO que variam entre 8 KB e 64 KB. Os IOPS reais dependem da carga de trabalho. Para mais informações, consulte [a Governação do IO de Dados.](resource-limits-logical-server.md#resource-governance)    

<sup>3</sup> Para os trabalhadores máximos simultâneos (pedidos) para qualquer base de dados individual, consulte [os limites de recursos de base de dados individuais](resource-limits-vcore-single-databases.md). Por exemplo, se a piscina elástica estiver a utilizar a Gen5 e o max vCore por base de dados for definido em 2, então o valor máximo simultâneo dos trabalhadores é de 200.  Se o máximo vCore por base de dados for definido para 0,5, então o valor máximo simultâneo dos trabalhadores é de 50, uma vez que na Gen5 existe um máximo de 100 trabalhadores simultâneos por vCore. Para outras definições max vCore por base de dados que sejam inferiores a 1 vCore ou menos, o número de trabalhadores máximos simultâneos é igualmente redimensionado.

## <a name="general-purpose---provisioned-compute---gen5"></a>Finalidade geral - computação a provisionada - Gen5

### <a name="general-purpose-service-tier-generation-5-compute-platform-part-1"></a>Nível de serviço para fins gerais: Plataforma computacional geração 5 (parte 1)

|Tamanho do cálculo|GP_Gen5_2|GP_Gen5_4|GP_Gen5_6|GP_Gen5_8|GP_Gen5_10|GP_Gen5_12|GP_Gen5_14|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|Geração computacional|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|
|vCores|2|4|6|8|10|12|14|
|Memória (GB)|10.4|20.8|31.1|41.5|51.9|62.3|72.7|
|DBs max por piscina <sup>1</sup>|100|200|500|500|500|500|500|
|Suporte de loja de colunas|Sim|Sim|Sim|Sim|Sim|Sim|Sim|
|Armazenamento OLTP na memória (GB)|N/D|N/D|N/D|N/D|N/D|N/D|N/D|
|Tamanho máximo dos dados (GB)|512|756|1536|1536|1536|2048|2048|
|Tamanho do tronco máximo (GB)|154|227|461|461|461|614|614|
|Tamanho máximo de dados tempDB (GB)|64|128|192|256|320|384|448|
|Tipo de armazenamento|Armazenamento Premium (Remoto)|Armazenamento Premium (Remoto)|Armazenamento Premium (Remoto)|Armazenamento Premium (Remoto)|Armazenamento Premium (Remoto)|Armazenamento Premium (Remoto)|Armazenamento Premium (Remoto)|
|Latência IO (aproximada)|5-7 ms (escrever)<br>5-10 ms (ler)|5-7 ms (escrever)<br>5-10 ms (ler)|5-7 ms (escrever)<br>5-10 ms (ler)|5-7 ms (escrever)<br>5-10 ms (ler)|5-7 ms (escrever)<br>5-10 ms (ler)|5-7 ms (escrever)<br>5-10 ms (ler)|5-7 ms (escrever)<br>5-10 ms (ler)|
|Dados máximos IOPS por pool <sup>2</sup>|800|1600|2400|3200|4000|4800|5600|
|Taxa máxima de registo por piscina (MBps)|9.4|18.8|28.1|37.5|37.5|37.5|37.5|
|Trabalhadores máximos simultâneos por piscina (pedidos) <sup>3</sup>|210|420|630|840|1050|1260|1470|
|Logins simultâneos máximos por piscina (pedidos) <sup>3</sup>|210|420|630|840|1050|1260|1470|
|Sessões simultâneas máx.|30,000|30,000|30,000|30,000|30,000|30,000|30,000|
|Min/max elástico piscina vCore escolhas por base de dados|0, 0.25, 0.5, 1, 2|0, 0.25, 0.5, 1...4|0, 0.25, 0.5, 1...6|0, 0.25, 0.5, 1...8|0, 0.25, 0.5, 1...10|0, 0.25, 0.5, 1...12|0, 0.25, 0.5, 1...14|
|Número de réplicas|1|1|1|1|1|1|1|
|Multi-AZ|N/D|N/D|N/D|N/D|N/D|N/D|N/D|
|Escalamento Horizontal de Leituras|N/D|N/D|N/D|N/D|N/D|N/D|N/D|
|Armazenamento de backup incluído|Tamanho 1X DB|Tamanho 1X DB|Tamanho 1X DB|Tamanho 1X DB|Tamanho 1X DB|Tamanho 1X DB|Tamanho 1X DB|

<sup>1</sup> Consulte [a gestão de recursos em piscinas elásticas densas](elastic-pool-resource-management.md) para obter considerações adicionais.

<sup>2</sup> O valor máximo para os tamanhos de IO que variam entre 8 KB e 64 KB. Os IOPS reais dependem da carga de trabalho. Para mais informações, consulte [a Governação do IO de Dados.](resource-limits-logical-server.md#resource-governance)

<sup>3</sup> Para os trabalhadores máximos simultâneos (pedidos) para qualquer base de dados individual, consulte [os limites de recursos de base de dados individuais](resource-limits-vcore-single-databases.md). Por exemplo, se a piscina elástica estiver a utilizar a Gen5 e o max vCore por base de dados for definido em 2, então o valor máximo simultâneo dos trabalhadores é de 200.  Se o máximo vCore por base de dados for definido para 0,5, então o valor máximo simultâneo dos trabalhadores é de 50, uma vez que na Gen5 existe um máximo de 100 trabalhadores simultâneos por vCore. Para outras definições max vCore por base de dados que sejam inferiores a 1 vCore ou menos, o número de trabalhadores máximos simultâneos é igualmente redimensionado.

### <a name="general-purpose-service-tier-generation-5-compute-platform-part-2"></a>Nível de serviço para fins gerais: Plataforma computacional geração 5 (parte 2)

|Tamanho do cálculo|GP_Gen5_16|GP_Gen5_18|GP_Gen5_20|GP_Gen5_24|GP_Gen5_32|GP_Gen5_40|GP_Gen5_80|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|Geração computacional|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|
|vCores|16|18|20|24|32|40|80|
|Memória (GB)|83|93.4|103.8|124.6|166.1|207.6|415.2|
|DBs max por piscina <sup>1</sup>|500|500|500|500|500|500|500|
|Suporte de loja de colunas|Sim|Sim|Sim|Sim|Sim|Sim|Sim|
|Armazenamento OLTP na memória (GB)|N/D|N/D|N/D|N/D|N/D|N/D|N/D|
|Tamanho máximo dos dados (GB)|2048|3072|3072|3072|4096|4096|4096|
|Tamanho do tronco máximo (GB)|614|922|922|922|1229|1229|1229|
|Tamanho máximo de dados tempDB (GB)|512|576|640|768|1024|1280|2560|
|Tipo de armazenamento|Armazenamento Premium (Remoto)|Armazenamento Premium (Remoto)|Armazenamento Premium (Remoto)|Armazenamento Premium (Remoto)|Armazenamento Premium (Remoto)|Armazenamento Premium (Remoto)|Armazenamento Premium (Remoto)|
|Latência IO (aproximada)|5-7 ms (escrever)<br>5-10 ms (ler)|5-7 ms (escrever)<br>5-10 ms (ler)|5-7 ms (escrever)<br>5-10 ms (ler)|5-7 ms (escrever)<br>5-10 ms (ler)|5-7 ms (escrever)<br>5-10 ms (ler)|5-7 ms (escrever)<br>5-10 ms (ler)|5-7 ms (escrever)<br>5-10 ms (ler)|
|Dados máximos IOPS por pool <sup>2</sup> |6,400|7,200|8,000|9600|12,800|16 000|16 000|
|Taxa máxima de registo por piscina (MBps)|37.5|37.5|37.5|37.5|37.5|37.5|37.5|
|Trabalhadores máximos simultâneos por piscina (pedidos) <sup>3</sup>|1680|1890|2100|2520|3360|4200|8400|
|Logins simultâneos máximos por piscina (pedidos) <sup>3</sup>|1680|1890|2100|2520|3360|4200|8400|
|Sessões simultâneas máx.|30,000|30,000|30,000|30,000|30,000|30,000|30,000|
|Min/max elástico piscina vCore escolhas por base de dados|0, 0.25, 0.5, 1...16|0, 0.25, 0.5, 1...18|0, 0.25, 0.5, 1...20|0, 0.25, 0.5, 1...20, 24|0, 0.25, 0.5, 1...20, 24, 32|0, 0.25, 0.5, 1...16, 24, 32, 40|0, 0.25, 0.5, 1...16, 24, 32, 40, 80|
|Número de réplicas|1|1|1|1|1|1|1|
|Multi-AZ|N/D|N/D|N/D|N/D|N/D|N/D|N/D|
|Escalamento Horizontal de Leituras|N/D|N/D|N/D|N/D|N/D|N/D|N/D|
|Armazenamento de backup incluído|Tamanho 1X DB|Tamanho 1X DB|Tamanho 1X DB|Tamanho 1X DB|Tamanho 1X DB|Tamanho 1X DB|Tamanho 1X DB|

<sup>1</sup> Consulte [a gestão de recursos em piscinas elásticas densas](elastic-pool-resource-management.md) para obter considerações adicionais.

<sup>2</sup> O valor máximo para os tamanhos de IO que variam entre 8 KB e 64 KB. Os IOPS reais dependem da carga de trabalho. Para mais informações, consulte [a Governação do IO de Dados.](resource-limits-logical-server.md#resource-governance)

<sup>3</sup> Para os trabalhadores máximos simultâneos (pedidos) para qualquer base de dados individual, consulte [os limites de recursos de base de dados individuais](resource-limits-vcore-single-databases.md). Por exemplo, se a piscina elástica estiver a utilizar a Gen5 e o max vCore por base de dados for definido em 2, então o valor máximo simultâneo dos trabalhadores é de 200.  Se o máximo vCore por base de dados for definido para 0,5, então o valor máximo simultâneo dos trabalhadores é de 50, uma vez que na Gen5 existe um máximo de 100 trabalhadores simultâneos por vCore. Para outras definições max vCore por base de dados que sejam inferiores a 1 vCore ou menos, o número de trabalhadores máximos simultâneos é igualmente redimensionado.

## <a name="general-purpose---provisioned-compute---fsv2-series"></a>Finalidade geral - cálculo provisionado - Série Fsv2

### <a name="fsv2-series-compute-generation-preview"></a>Geração de computação da série Fsv2 (pré-visualização)

|Tamanho do cálculo|GP_Fsv2_72|
|:--- | --: |
|Geração computacional|Série Fsv2|
|vCores|72|
|Memória (GB)|136.2|
|DBs max por piscina <sup>1</sup>|500|
|Suporte de loja de colunas|Yes|
|Armazenamento OLTP na memória (GB)|N/D|
|Tamanho máximo dos dados (GB)|4096|
|Tamanho do tronco máximo (GB)|1024|
|Tamanho máximo de dados tempDB (GB)|333|
|Tipo de armazenamento|Armazenamento Premium (Remoto)|
|Latência IO (aproximada)|5-7 ms (escrever)<br>5-10 ms (ler)|
|Dados máximos IOPS por pool <sup>2</sup>|16 000|
|Taxa máxima de registo por piscina (MBps)|37.5|
|Trabalhadores máximos simultâneos por piscina (pedidos) <sup>3</sup>|3780|
|Logins simultâneos máximos por piscina (pedidos) <sup>3</sup>|3780|
|Sessões simultâneas máx.|30,000|
|Min/max elástico piscina vCore escolhas por base de dados|0-72|
|Número de réplicas|1|
|Multi-AZ|N/D|
|Escalamento Horizontal de Leituras|N/D|
|Armazenamento de backup incluído|Tamanho 1X DB|

<sup>1</sup> Consulte [a gestão de recursos em piscinas elásticas densas](elastic-pool-resource-management.md) para obter considerações adicionais.

<sup>2</sup> O valor máximo para os tamanhos de IO que variam entre 8 KB e 64 KB. Os IOPS reais dependem da carga de trabalho. Para mais informações, consulte [a Governação do IO de Dados.](resource-limits-logical-server.md#resource-governance)

<sup>3</sup> Para os trabalhadores máximos simultâneos (pedidos) para qualquer base de dados individual, consulte [os limites de recursos de base de dados individuais](resource-limits-vcore-single-databases.md). Por exemplo, se a piscina elástica estiver a utilizar a Gen5 e o max vCore por base de dados for definido em 2, então o valor máximo simultâneo dos trabalhadores é de 200.  Se o máximo vCore por base de dados for definido para 0,5, então o valor máximo simultâneo dos trabalhadores é de 50, uma vez que na Gen5 existe um máximo de 100 trabalhadores simultâneos por vCore. Para outras definições max vCore por base de dados que sejam inferiores a 1 vCore ou menos, o número de trabalhadores máximos simultâneos é igualmente redimensionado.

## <a name="business-critical---provisioned-compute---gen4"></a>Business critical - provisioned compute - Gen4

> [!IMPORTANT]
> As novas bases de dados da Gen4 já não são suportadas nas regiões leste ou sul da Austrália.

### <a name="business-critical-service-tier-generation-4-compute-platform-part-1"></a>Nível de serviço crítico de negócios: Plataforma computacional geração 4 (parte 1)

|Tamanho do cálculo|BC_Gen4_2|BC_Gen4_3|BC_Gen4_4|BC_Gen4_5|BC_Gen4_6|
|:--- | --: |--: |--: |--: |--: |--: |
|Geração computacional|Gen4|Gen4|Gen4|Gen4|Gen4|
|vCores|2|3|4|5|6|
|Memória (GB)|14|21|28|35|42|
|DBs max por piscina <sup>1</sup>|50|100|100|100|100|
|Suporte de loja de colunas|Sim|Sim|Sim|Sim|Sim|
|Armazenamento OLTP na memória (GB)|2|3|4|5|6|
|Tipo de armazenamento|Local SSD|Local SSD|Local SSD|Local SSD|Local SSD|
|Tamanho máximo dos dados (GB)|1024|1024|1024|1024|1024|
|Tamanho do tronco máximo (GB)|307|307|307|307|307|
|Tamanho máximo de dados tempDB (GB)|64|96|128|160|192|
|Latência IO (aproximada)|1-2 ms (escrever)<br>1-2 ms (ler)|1-2 ms (escrever)<br>1-2 ms (ler)|1-2 ms (escrever)<br>1-2 ms (ler)|1-2 ms (escrever)<br>1-2 ms (ler)|1-2 ms (escrever)<br>1-2 ms (ler)|
|Dados máximos IOPS por pool <sup>2</sup>|9000|13,500|18 000|22,500|27,000|
|Taxa máxima de registo por piscina (MBps)|20|30|40|50|60|
|Trabalhadores máximos simultâneos por piscina (pedidos) <sup>3</sup>|420|630|840|1050|1260|
|Logins simultâneos máximos por piscina (pedidos) <sup>3</sup>|420|630|840|1050|1260|
|Sessões simultâneas máx.|30,000|30,000|30,000|30,000|30,000|
|Min/max elástico piscina vCore escolhas por base de dados|0, 0.25, 0.5, 1, 2|0, 0.25, 0.5, 1...3|0, 0.25, 0.5, 1...4|0, 0.25, 0.5, 1...5|0, 0.25, 0.5, 1...6|
|Número de réplicas|4|4|4|4|4|
|Multi-AZ|Sim|Sim|Sim|Sim|Sim|
|Escalamento Horizontal de Leituras|Sim|Sim|Sim|Sim|Sim|
|Armazenamento de backup incluído|Tamanho 1X DB|Tamanho 1X DB|Tamanho 1X DB|Tamanho 1X DB|Tamanho 1X DB|

<sup>1</sup> Consulte [a gestão de recursos em piscinas elásticas densas](elastic-pool-resource-management.md) para obter considerações adicionais.

<sup>2</sup> O valor máximo para os tamanhos de IO que variam entre 8 KB e 64 KB. Os IOPS reais dependem da carga de trabalho. Para mais informações, consulte [a Governação do IO de Dados.](resource-limits-logical-server.md#resource-governance)

<sup>3</sup> Para os trabalhadores máximos simultâneos (pedidos) para qualquer base de dados individual, consulte [os limites de recursos de base de dados individuais](resource-limits-vcore-single-databases.md). Por exemplo, se a piscina elástica estiver a utilizar a Gen5 e o max vCore por base de dados for definido em 2, então o valor máximo simultâneo dos trabalhadores é de 200.  Se o máximo vCore por base de dados for definido para 0,5, então o valor máximo simultâneo dos trabalhadores é de 50, uma vez que na Gen5 existe um máximo de 100 trabalhadores simultâneos por vCore. Para outras definições max vCore por base de dados que sejam inferiores a 1 vCore ou menos, o número de trabalhadores máximos simultâneos é igualmente redimensionado.

### <a name="business-critical-service-tier-generation-4-compute-platform-part-2"></a>Nível de serviço crítico de negócios: Plataforma computacional geração 4 (parte 2)

|Tamanho do cálculo|BC_Gen4_7|BC_Gen4_8|BC_Gen4_9|BC_Gen4_10|BC_Gen4_16|BC_Gen4_24|
|:--- | --: |--: |--: |--: |--: |--: |
|Geração computacional|Gen4|Gen4|Gen4|Gen4|Gen4|Gen4|
|vCores|7|8|9|10|16|24|
|Memória (GB)|49|56|63|70|112|159.5|
|DBs max por piscina <sup>1</sup>|100|100|100|100|100|100|
|Suporte de loja de colunas|N/D|N/D|N/D|N/D|N/D|N/D|
|Armazenamento OLTP na memória (GB)|7|8|9.5|11|20|36|
|Tipo de armazenamento|Local SSD|Local SSD|Local SSD|Local SSD|Local SSD|Local SSD|
|Tamanho máximo dos dados (GB)|1024|1024|1024|1024|1024|1024|
|Tamanho do tronco máximo (GB)|307|307|307|307|307|307|
|Tamanho máximo de dados tempDB (GB)|224|256|288|320|512|768|
|Latência IO (aproximada)|1-2 ms (escrever)<br>1-2 ms (ler)|1-2 ms (escrever)<br>1-2 ms (ler)|1-2 ms (escrever)<br>1-2 ms (ler)|1-2 ms (escrever)<br>1-2 ms (ler)|1-2 ms (escrever)<br>1-2 ms (ler)|1-2 ms (escrever)<br>1-2 ms (ler)|
|Dados máximos IOPS por pool <sup>2</sup>|31,500|36,000|40,500|45 000|72,000|96,000|
|Taxa máxima de registo por piscina (MBps)|70|80|80|80|80|80|
|Trabalhadores máximos simultâneos por piscina (pedidos) <sup>3</sup>|1470|1680|1890|2100|3360|5040|
|Logins simultâneos máximos por piscina (pedidos) <sup>3</sup>|1470|1680|1890|2100|3360|5040|
|Sessões simultâneas máx.|30,000|30,000|30,000|30,000|30,000|30,000|
|Min/max elástico piscina vCore escolhas por base de dados|0, 0.25, 0.5, 1...7|0, 0.25, 0.5, 1...8|0, 0.25, 0.5, 1...9|0, 0.25, 0.5, 1...10|0, 0.25, 0.5, 1...10, 16|0, 0.25, 0.5, 1...10, 16, 24|
|Número de réplicas|4|4|4|4|4|4|
|Multi-AZ|Sim|Sim|Sim|Sim|Sim|Sim|
|Escalamento Horizontal de Leituras|Sim|Sim|Sim|Sim|Sim|Sim|
|Armazenamento de backup incluído|Tamanho 1X DB|Tamanho 1X DB|Tamanho 1X DB|Tamanho 1X DB|Tamanho 1X DB|Tamanho 1X DB|

<sup>1</sup> Consulte [a gestão de recursos em piscinas elásticas densas](elastic-pool-resource-management.md) para obter considerações adicionais.

<sup>2</sup> O valor máximo para os tamanhos de IO que variam entre 8 KB e 64 KB. Os IOPS reais dependem da carga de trabalho. Para mais informações, consulte [a Governação do IO de Dados.](resource-limits-logical-server.md#resource-governance)

<sup>3</sup> Para os trabalhadores máximos simultâneos (pedidos) para qualquer base de dados individual, consulte [os limites de recursos de base de dados individuais](resource-limits-vcore-single-databases.md). Por exemplo, se a piscina elástica estiver a utilizar a Gen5 e o max vCore por base de dados for definido em 2, então o valor máximo simultâneo dos trabalhadores é de 200.  Se o máximo vCore por base de dados for definido para 0,5, então o valor máximo simultâneo dos trabalhadores é de 50, uma vez que na Gen5 existe um máximo de 100 trabalhadores simultâneos por vCore. Para outras definições max vCore por base de dados que sejam inferiores a 1 vCore ou menos, o número de trabalhadores máximos simultâneos é igualmente redimensionado.

## <a name="business-critical---provisioned-compute---gen5"></a>Business critical - provisioned compute - Gen5

### <a name="business-critical-service-tier-generation-5-compute-platform-part-1"></a>Nível de serviço crítico de negócios: Plataforma computacional geração 5 (parte 1)

|Tamanho do cálculo|BC_Gen5_4|BC_Gen5_6|BC_Gen5_8|BC_Gen5_10|BC_Gen5_12|BC_Gen5_14|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|Geração computacional|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|
|vCores|4|6|8|10|12|14|
|Memória (GB)|20.8|31.1|41.5|51.9|62.3|72.7|
|DBs max por piscina <sup>1</sup>|50|100|100|100|100|100|
|Suporte de loja de colunas|Sim|Sim|Sim|Sim|Sim|Sim|
|Armazenamento OLTP na memória (GB)|3.14|4.71|6.28|8.65|11.02|13.39|
|Tamanho máximo dos dados (GB)|1024|1536|1536|1536|3072|3072|
|Tamanho do tronco máximo (GB)|307|307|461|461|922|922|
|Tamanho máximo de dados tempDB (GB)|128|192|256|320|384|448|
|Tipo de armazenamento|Local SSD|Local SSD|Local SSD|Local SSD|Local SSD|Local SSD|
|Latência IO (aproximada)|1-2 ms (escrever)<br>1-2 ms (ler)|1-2 ms (escrever)<br>1-2 ms (ler)|1-2 ms (escrever)<br>1-2 ms (ler)|1-2 ms (escrever)<br>1-2 ms (ler)|1-2 ms (escrever)<br>1-2 ms (ler)|1-2 ms (escrever)<br>1-2 ms (ler)|
|Dados máximos IOPS por pool <sup>2</sup>|18 000|27,000|36,000|45 000|54,000|63,000|
|Taxa máxima de registo por piscina (MBps)|60|90|120|120|120|120|
|Trabalhadores máximos simultâneos por piscina (pedidos) <sup>3</sup>|420|630|840|1050|1260|1470|
|Logins simultâneos máximos por piscina (pedidos) <sup>3</sup>|420|630|840|1050|1260|1470|
|Sessões simultâneas máx.|30,000|30,000|30,000|30,000|30,000|30,000|
|Min/max elástico piscina vCore escolhas por base de dados|0, 0.25, 0.5, 1...4|0, 0.25, 0.5, 1...6|0, 0.25, 0.5, 1...8|0, 0.25, 0.5, 1...10|0, 0.25, 0.5, 1...12|0, 0.25, 0.5, 1...14|
|Número de réplicas|4|4|4|4|4|4|
|Multi-AZ|Sim|Sim|Sim|Sim|Sim|Sim|
|Escalamento Horizontal de Leituras|Sim|Sim|Sim|Sim|Sim|Sim|
|Armazenamento de backup incluído|Tamanho 1X DB|Tamanho 1X DB|Tamanho 1X DB|Tamanho 1X DB|Tamanho 1X DB|Tamanho 1X DB|

<sup>1</sup> Consulte [a gestão de recursos em piscinas elásticas densas](elastic-pool-resource-management.md) para obter considerações adicionais.

<sup>2</sup> O valor máximo para os tamanhos de IO que variam entre 8 KB e 64 KB. Os IOPS reais dependem da carga de trabalho. Para mais informações, consulte [a Governação do IO de Dados.](resource-limits-logical-server.md#resource-governance)

<sup>3</sup> Para os trabalhadores máximos simultâneos (pedidos) para qualquer base de dados individual, consulte [os limites de recursos de base de dados individuais](resource-limits-vcore-single-databases.md). Por exemplo, se a piscina elástica estiver a utilizar a Gen5 e o max vCore por base de dados for definido em 2, então o valor máximo simultâneo dos trabalhadores é de 200.  Se o máximo vCore por base de dados for definido para 0,5, então o valor máximo simultâneo dos trabalhadores é de 50, uma vez que na Gen5 existe um máximo de 100 trabalhadores simultâneos por vCore. Para outras definições max vCore por base de dados que sejam inferiores a 1 vCore ou menos, o número de trabalhadores máximos simultâneos é igualmente redimensionado.

### <a name="business-critical-service-tier-generation-5-compute-platform-part-2"></a>Nível de serviço crítico de negócios: Plataforma computacional geração 5 (parte 2)

|Tamanho do cálculo|BC_Gen5_16|BC_Gen5_18|BC_Gen5_20|BC_Gen5_24|BC_Gen5_32|BC_Gen5_40|BC_Gen5_80|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|Geração computacional|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|
|vCores|16|18|20|24|32|40|80|
|Memória (GB)|83|93.4|103.8|124.6|166.1|207.6|415.2|
|DBs max por piscina <sup>1</sup>|100|100|100|100|100|100|100|
|Suporte de loja de colunas|Sim|Sim|Sim|Sim|Sim|Sim|Sim|
|Armazenamento OLTP na memória (GB)|15,77|18.14|20.51|25.25|37.94|52.23|131.68|
|Tamanho máximo dos dados (GB)|3072|3072|3072|4096|4096|4096|4096|
|Tamanho do tronco máximo (GB)|922|922|922|1229|1229|1229|1229|
|Tamanho máximo de dados tempDB (GB)|512|576|640|768|1024|1280|2560|
|Tipo de armazenamento|Local SSD|Local SSD|Local SSD|Local SSD|Local SSD|Local SSD|Local SSD|
|Latência IO (aproximada)|1-2 ms (escrever)<br>1-2 ms (ler)|1-2 ms (escrever)<br>1-2 ms (ler)|1-2 ms (escrever)<br>1-2 ms (ler)|1-2 ms (escrever)<br>1-2 ms (ler)|1-2 ms (escrever)<br>1-2 ms (ler)|1-2 ms (escrever)<br>1-2 ms (ler)|1-2 ms (escrever)<br>1-2 ms (ler)|
|Dados máximos IOPS por pool <sup>2</sup>|72,000|81,000|90.000|108,000|144,000|180,000|256,000|
|Taxa máxima de registo por piscina (MBps)|120|120|120|120|120|120|120|
|Trabalhadores máximos simultâneos por piscina (pedidos) <sup>3</sup>|1680|1890|2100|2520|3360|4200|8400|
|Logins simultâneos máximos por piscina (pedidos) <sup>3</sup>|1680|1890|2100|2520|3360|4200|8400|
|Sessões simultâneas máx.|30,000|30,000|30,000|30,000|30,000|30,000|30,000|
|Min/max elástico piscina vCore escolhas por base de dados|0, 0.25, 0.5, 1...16|0, 0.25, 0.5, 1...18|0, 0.25, 0.5, 1...20|0, 0.25, 0.5, 1...20, 24|0, 0.25, 0.5, 1...20, 24, 32|0, 0.25, 0.5, 1...20, 24, 32, 40|0, 0.25, 0.5, 1...20, 24, 32, 40, 80|
|Número de réplicas|4|4|4|4|4|4|4|
|Multi-AZ|Sim|Sim|Sim|Sim|Sim|Sim|Sim|
|Escalamento Horizontal de Leituras|Sim|Sim|Sim|Sim|Sim|Sim|Sim|
|Armazenamento de backup incluído|Tamanho 1X DB|Tamanho 1X DB|Tamanho 1X DB|Tamanho 1X DB|Tamanho 1X DB|Tamanho 1X DB|Tamanho 1X DB|

<sup>1</sup> Consulte [a gestão de recursos em piscinas elásticas densas](elastic-pool-resource-management.md) para obter considerações adicionais.

<sup>2</sup> O valor máximo para os tamanhos de IO que variam entre 8 KB e 64 KB. Os IOPS reais dependem da carga de trabalho. Para mais informações, consulte [a Governação do IO de Dados.](resource-limits-logical-server.md#resource-governance)

<sup>3</sup> Para os trabalhadores máximos simultâneos (pedidos) para qualquer base de dados individual, consulte [os limites de recursos de base de dados individuais](resource-limits-vcore-single-databases.md). Por exemplo, se a piscina elástica estiver a utilizar a Gen5 e o max vCore por base de dados for definido em 2, então o valor máximo simultâneo dos trabalhadores é de 200.  Se o máximo vCore por base de dados for definido para 0,5, então o valor máximo simultâneo dos trabalhadores é de 50, uma vez que na Gen5 existe um máximo de 100 trabalhadores simultâneos por vCore. Para outras definições max vCore por base de dados que sejam inferiores a 1 vCore ou menos, o número de trabalhadores máximos simultâneos é igualmente redimensionado.

## <a name="business-critical---provisioned-compute---m-series"></a>Business critical - provisioned compute - M-series

### <a name="m-series-compute-generation-preview"></a>Geração de computação série M (pré-visualização)

|Tamanho do cálculo|BC_M_128|
|:--- | --: |
|Geração computacional|Série M|
|vCores|128|
|Memória (GB)|3767.1|
|DBs max por piscina <sup>1</sup>|100|
|Suporte de loja de colunas|Yes|
|Armazenamento OLTP na memória (GB)|1768|
|Tamanho máximo dos dados (GB)|4096|
|Tamanho do tronco máximo (GB)|2048|
|Tamanho máximo de dados tempDB (GB)|4096|
|Tipo de armazenamento|Local SSD|
|Latência IO (aproximada)|1-2 ms (escrever)<br>1-2 ms (ler)|
|Dados máximos IOPS por pool <sup>2</sup>|200,000|
|Taxa máxima de registo por piscina (MBps)|333|
|Trabalhadores máximos simultâneos por piscina (pedidos) <sup>3</sup>|13,440|
|Logins simultâneos máximos por piscina (pedidos) <sup>3</sup>|13,440|
|Sessões simultâneas máx.|30,000|
|Min/max elástico piscina vCore escolhas por base de dados|0-128|
|Número de réplicas|4|
|Multi-AZ|Yes|
|Escalamento Horizontal de Leituras|Yes|
|Armazenamento de backup incluído|Tamanho 1X DB|

<sup>1</sup> Consulte [a gestão de recursos em piscinas elásticas densas](elastic-pool-resource-management.md) para obter considerações adicionais.

<sup>2</sup> O valor máximo para os tamanhos de IO que variam entre 8 KB e 64 KB. Os IOPS reais dependem da carga de trabalho. Para mais informações, consulte [a Governação do IO de Dados.](resource-limits-logical-server.md#resource-governance)

<sup>3</sup> Para os trabalhadores máximos simultâneos (pedidos) para qualquer base de dados individual, consulte [os limites de recursos de base de dados individuais](resource-limits-vcore-single-databases.md). Por exemplo, se a piscina elástica estiver a utilizar a Gen5 e o max vCore por base de dados for definido em 2, então o valor máximo simultâneo dos trabalhadores é de 200.  Se o máximo vCore por base de dados for definido para 0,5, então o valor máximo simultâneo dos trabalhadores é de 50, uma vez que na Gen5 existe um máximo de 100 trabalhadores simultâneos por vCore. Para outras definições max vCore por base de dados que sejam inferiores a 1 vCore ou menos, o número de trabalhadores máximos simultâneos é igualmente redimensionado.

Se todos os vCores de uma piscina elástica estiverem ocupados, então cada base de dados na piscina recebe uma quantidade igual de recursos computacional para processar consultas. A Azure SQL Database fornece equidade de partilha de recursos entre bases de dados, garantindo fatias iguais de tempo de computação. A equidade de partilha de recursos de piscina elástica é além de qualquer quantidade de recursos garantidos de outra forma a cada base de dados quando o vCore min por base de dados é definido para um valor não zero.

## <a name="database-properties-for-pooled-databases"></a>Propriedades da base de dados para bases de dados agrizadas

A tabela seguinte descreve as propriedades para bases de dados aginhadas.

> [!NOTE]
> Os limites de recursos de bases de dados individuais em piscinas elásticas são geralmente os mesmos que para bases de dados individuais fora de piscinas que têm o mesmo tamanho de cálculo. Por exemplo, os trabalhadores máximos simultâneos para uma base de dados GP_Gen4_1 são 200 trabalhadores. Assim, os trabalhadores máximos simultâneos para uma base de dados numa piscina de GP_Gen4_1 também são 200 trabalhadores. Nota: o número total de trabalhadores simultâneos na GP_Gen4_1 piscina é de 210.

| Propriedade | Descrição |
|:--- |:--- |
| Max vCores por base de dados |O número máximo de vCores que qualquer base de dados do pool pode utilizar, se disponível com base na utilização por outras bases de dados na piscina. Max vCores por base de dados não é uma garantia de recurso para uma base de dados. Esta definição é uma definição global que se aplica a todas as bases de dados no conjunto. Desacorra max vCores por base de dados suficientemente alto para lidar com picos na utilização da base de dados. Espera-se um certo grau de compromisso excessivo, uma vez que a piscina geralmente pressupõe padrões de utilização quentes e frios para bases de dados onde todas as bases de dados não estão simultaneamente no pico.|
| Min vCores por base de dados |O número mínimo de vCores que qualquer base de dados na piscina está garantida. Esta definição é uma definição global que se aplica a todas as bases de dados no conjunto. O min vCores por base de dados pode ser definido para 0, e é também o valor padrão. Esta propriedade está definida para qualquer lugar entre 0 e a média vCores utilização por base de dados. O produto do número de bases de dados na piscina e do min vCores por base de dados não pode exceder os vCores por pool.|
| Armazenamento máximo por base de dados |O tamanho máximo da base de dados definido pelo utilizador para uma base de dados numa piscina. As bases de dados partilhadas partilham o armazenamento de piscinas atribuídos, pelo que o tamanho que uma base de dados pode alcançar é limitado ao menor do armazenamento de piscinas restante e do tamanho da base de dados. O tamanho máximo da base de dados refere-se ao tamanho máximo dos ficheiros de dados e não inclui o espaço utilizado pelos ficheiros de registo. |
|||

## <a name="next-steps"></a>Passos seguintes

- Para limites de recursos vCore para uma única base de [dados, consulte os limites de recursos para bases de dados únicas utilizando o modelo de compra vCore](resource-limits-vcore-single-databases.md)
- Para limites de recursos DTU para uma única base de [dados, consulte os limites de recursos para bases de dados únicas utilizando o modelo de compra do DTU](resource-limits-dtu-single-databases.md)
- Para limites de recursos DTU para piscinas elásticas, consulte [limites de recursos para piscinas elásticas utilizando o modelo de compra DTU](resource-limits-dtu-elastic-pools.md)
- Para limites de recursos para casos geridos, consulte [os limites de recursos de exemplo geridos](../managed-instance/resource-limits.md).
- Para obter informações sobre os limites gerais do Azure, consulte [os limites de subscrição e serviços, quotas e restrições da Azure.](../../azure-resource-manager/management/azure-subscription-service-limits.md)
- Para obter informações sobre os limites de recursos num servidor lógico do SQL, consulte [a visão geral dos limites de recursos num servidor lógico SQL](resource-limits-logical-server.md) para obter informações sobre limites nos níveis de servidor e subscrição.
