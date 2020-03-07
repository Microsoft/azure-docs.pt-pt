---
title: Os recursos dTU limitam bases de dados únicas
description: Esta página descreve alguns limites comuns de recursos DTU para bases de dados únicas na Base de Dados Azure SQL.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/20/2019
ms.openlocfilehash: a4c435b4874301fe6fb804085c5b265954cd4f5a
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78359948"
---
# <a name="resource-limits-for-single-databases-using-the-dtu-purchasing-model"></a>Limites de recursos para bases de dados únicas utilizando o modelo de compra dTU

Este artigo fornece os limites de recursos detalhados para as bases de dados únicas da Azure SQL utilizando o modelo de compra dTU.

Para a aquisição de limites de recursos do modelo dTU para piscinas elásticas, consulte [os limites de recursos DTU - piscinas elásticas](sql-database-dtu-resource-limits-elastic-pools.md). Para os limites de recursos vCore, consulte os limites de [recursos vCore - bases de dados únicas](sql-database-vcore-resource-limits-single-databases.md) e limites de [recursos vCore - piscinas elásticas](sql-database-vcore-resource-limits-elastic-pools.md). Para obter mais informações sobre os diferentes modelos de compra, consulte [modelos de compra e níveis de serviço.](sql-database-purchase-models.md)

## <a name="single-database-storage-sizes-and-compute-sizes"></a>Base de dados única: tamanhos de armazenamento e tamanhos de cálculo

As tabelas que se seguem mostram os recursos disponíveis para uma única base de dados em cada nível de serviço e tamanho da computação. Pode definir o nível de serviço, o tamanho da computação e o valor de armazenamento para uma única base de dados utilizando o [portal Azure,](sql-database-single-databases-manage.md#manage-an-existing-sql-database-server) [Transact-SQL,](sql-database-single-databases-manage.md#transact-sql-manage-sql-database-servers-and-single-databases) [PowerShell,](sql-database-single-databases-manage.md#powershell-manage-sql-database-servers-and-single-databases)o [Azure CLI](sql-database-single-databases-manage.md#azure-cli-manage-sql-database-servers-and-single-databases)ou o [REST API](sql-database-single-databases-manage.md#rest-api-manage-sql-database-servers-and-single-databases).

> [!IMPORTANT]
> Para orientação e considerações de escala, consulte [Escala uma única base de dados](sql-database-single-database-scale.md)

### <a name="basic-service-tier"></a>Escalão de serviços básicos

| **Tamanho da computação** | **Básica** |
| :--- | --: |
| Máximo de DTUs | 5 |
| Armazenamento incluído (GB) | 2 |
| Escolhas de armazenamento max (GB) | 2 |
| Armazenamento em OLTP na memória máx. (GB) |N/A |
| Trabalhadores simultâneos max (pedidos) | 30 |
| Sessões simultâneas máx. | 300 |
|||

> [!IMPORTANT]
> O nível de serviço Básico fornece menos de um vCore (CPU).  Para cargas de trabalho intensivas em CPU, recomenda-se um nível de serviço de S3 ou superior. 
>
>No que diz respeito ao armazenamento de dados, o nível de serviço Básico é colocado em Standard Page Blobs. Standard Page Blobs usam suportes de armazenamento baseados em disco rígido (HDD) e são mais adequados para desenvolvimento, teste e outras cargas de trabalho pouco acedidas que são menos sensíveis à variabilidade do desempenho.
>

### <a name="standard-service-tier"></a>Escalão de serviço Standard

| **Tamanho da computação** | **S0** | **S1** | **S2** | **S3** |
| :--- |---:| ---:|---:|---:|
| Máximo de DTUs | 10 | 20 | 50 | 100 |
| Armazenamento incluído (GB) | 250 | 250 | 250 | 250 |
| Escolhas de armazenamento max (GB) | 250 | 250 | 250 | 250, 500, 750, 1024 |
| Armazenamento em OLTP na memória máx. (GB) | N/A | N/A | N/A | N/A |
| Trabalhadores simultâneos max (pedidos)| 60 | 90 | 120 | 200 |
| Sessões simultâneas máx. |600 | 900 | 1200 | 2400 |
||||||

> [!IMPORTANT]
> Os níveis Standard S0, S1 e S2 fornecem menos de um vCore (CPU).  Para cargas de trabalho intensivas em CPU, recomenda-se um nível de serviço de S3 ou superior. 
>
>No que diz respeito ao armazenamento de dados, os níveis de serviço Standard S0 e S1 são colocados em Standard Page Blobs. Standard Page Blobs usam suportes de armazenamento baseados em disco rígido (HDD) e são mais adequados para desenvolvimento, teste e outras cargas de trabalho pouco acedidas que são menos sensíveis à variabilidade do desempenho.
>

### <a name="standard-service-tier-continued"></a>Nível de serviço padrão (continuação)

| **Tamanho da computação** | **S4** | **S6** | **S7** | **S9** | **S12** |
| :--- |---:| ---:|---:|---:|---:|
| Máximo de DTUs | 200 | 400 | 800 | 1600 | 3000 |
| Armazenamento incluído (GB) | 250 | 250 | 250 | 250 | 250 |
| Escolhas de armazenamento max (GB) | 250, 500, 750, 1024 | 250, 500, 750, 1024 | 250, 500, 750, 1024 | 250, 500, 750, 1024 | 250, 500, 750, 1024 |
| Armazenamento em OLTP na memória máx. (GB) | N/A | N/A | N/A | N/A |N/A |
| Trabalhadores simultâneos max (pedidos)| 400 | 800 | 1600 | 3200 |6000 |
| Sessões simultâneas máx. |4800 | 9600 | 19200 | 30000 |30000 |
|||||||

### <a name="premium-service-tier"></a>Escalão de serviços Premium

| **Tamanho da computação** | **P1** | **P2** | **P4** | **P6** | **P11** | **P15** |
| :--- |---:|---:|---:|---:|---:|---:|
| Máximo de DTUs | 125 | 250 | 500 | 1000 | 1750 | 4000 |
| Armazenamento incluído (GB) | 500 | 500 | 500 | 500 | 4096* | 4096* |
| Escolhas de armazenamento max (GB) | 500, 750, 1024 | 500, 750, 1024 | 500, 750, 1024 | 500, 750, 1024 | 4096* | 4096* |
| Armazenamento em OLTP na memória máx. (GB) | 1 | 2 | 4 | 8 | 14 | 32 |
| Trabalhadores simultâneos max (pedidos)| 200 | 400 | 800 | 1600 | 2400 | 6400 |
| Sessões simultâneas máx. | 30000 | 30000 | 30000 | 30000 | 30000 | 30000 |
|||||||

\* De 1024 GB até 4096 GB em incrementos de 256 GB

> [!IMPORTANT]
> Mais de 1 TB de armazenamento no nível Premium está atualmente disponível em todas as regiões, exceto: China Leste, China North, Alemanha Central, Alemanha Nordeste, Oeste Dos EUA, regiões do DoD dos EUA e Central do Governo dos EUA. Nestas regiões, o máximo de armazenamento no nível Premium está limitado a 1 TB.  Para mais informações, consulte as [limitações atuais p11-P15](sql-database-single-database-scale.md#p11-and-p15-constraints-when-max-size-greater-than-1-tb).  
> [!NOTE]
> Para `tempdb` limites, consulte [os limites temporários.](https://docs.microsoft.com/sql/relational-databases/databases/tempdb-database?view=sql-server-2017#tempdb-database-in-sql-database)

## <a name="next-steps"></a>Passos seguintes

- Para os limites de recursos vCore para uma única base de dados, consulte [os limites de recursos para bases de dados únicas utilizando o modelo](sql-database-vcore-resource-limits-single-databases.md) de compra vCore
- Para os limites de recursos vCore para piscinas elásticas, consulte [os limites de recursos para piscinas elásticas utilizando o modelo](sql-database-vcore-resource-limits-elastic-pools.md) de compra vCore
- Para os limites de recursos dTU para piscinas elásticas, consulte [os limites de recursos para piscinas elásticas utilizando o modelo](sql-database-dtu-resource-limits-elastic-pools.md) de compra dTU
- Para os limites de recursos para casos geridos, consulte [os limites de recursos geridos](sql-database-managed-instance-resource-limits.md)por exemplo .
- Para obter informações sobre os limites gerais do Azure, consulte [os limites de subscrição e serviço do Azure, quotas e restrições.](../azure-resource-manager/management/azure-subscription-service-limits.md)
- Para obter informações sobre os limites de recursos num servidor de base de dados, consulte a [visão geral dos limites de recursos num servidor de base de dados SQL](sql-database-resource-limits-database-server.md) para obter informações sobre limites nos níveis de servidor e subscrição.
