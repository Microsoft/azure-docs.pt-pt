---
title: Bases de dados individuais de limites de recursos baseados em DTU de base de dados SQL do Azure | Documentos da Microsoft
description: Esta página descreve alguns limites comuns do recurso baseado em DTU para bases de dados individuais na base de dados do Azure SQL.
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
ms.date: 03/14/2019
ms.openlocfilehash: 37dcde3d55ce704c7a5dbb6d7757420ba8c393f6
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57885106"
---
# <a name="resource-limits-for-single-databases-using-the-dtu-based-purchasing-model"></a>Limites de recursos para bases de dados individuais com o modelo de compra baseado em DTU

Este artigo fornece os limites de recursos detalhados para a base de dados do Azure SQL únicas bases de dados com o modelo de compra baseado em DTU.

Para baseado em DTU compra modelo limites de recursos para conjuntos elásticos, veja [limites de recursos baseados em DTU - conjuntos elásticos](sql-database-dtu-resource-limits-elastic-pools.md). Para limites de recursos baseados em vCore, consulte [limites de recursos baseados em vCore - bases de dados individuais](sql-database-vcore-resource-limits-single-databases.md) e [limites de recursos baseados em vCore - conjuntos elásticos](sql-database-vcore-resource-limits-elastic-pools.md). Para obter mais informações sobre os diferentes modelos de compras, veja [escalões de serviço e modelos de compra](sql-database-purchase-models.md).

## <a name="single-database-storage-sizes-and-compute-sizes"></a>Base de dados: Tamanhos de armazenamento e tamanhos de computação

As tabelas seguintes mostram os recursos disponíveis para uma base de dados em cada escalão de serviço e tamanho de computação. Pode definir o escalão de serviço, o tamanho de computação e a quantidade de armazenamento para uma base de dados individual utilizando o [portal do Azure](sql-database-single-databases-manage.md#manage-an-existing-sql-database-server), [Transact-SQL](sql-database-single-databases-manage.md#transact-sql-manage-sql-database-servers-and-single-databases), [PowerShell](sql-database-single-databases-manage.md#powershell-manage-sql-database-servers-and-single-databases), o [ CLI do Azure](sql-database-single-databases-manage.md#azure-cli-manage-sql-database-servers-and-single-databases), ou o [API de REST](sql-database-single-databases-manage.md#rest-api-manage-sql-database-servers-and-single-databases).

> [!IMPORTANT]
> Para dimensionar a orientação e considerações, consulte [dimensionar uma base de dados](sql-database-single-database-scale.md)

### <a name="basic-service-tier"></a>Camada de serviços básicos

| **Tamanho de computação** | **Básica** |
| :--- | --: |
| Máximo de DTUs | 5 |
| Armazenamento incluído (GB) | 2 |
| Opções de armazenamento máximo (GB) | 2 |
| Armazenamento em OLTP na memória máx. (GB) |N/A |
| Máximo de trabalhadores simultâneos (pedidos) | 30 |
| Sessões simultâneas máx. | 300 |
|||

### <a name="standard-service-tier"></a>Camada de serviços padrão

| **Tamanho de computação** | **S0** | **S1** | **S2** | **S3** |
| :--- |---:| ---:|---:|---:|
| Máximo de DTUs | 10 | 20 | 50 | 100 |
| Armazenamento incluído (GB) | 250 | 250 | 250 | 250 |
| Opções de armazenamento máximo (GB) | 250 | 250 | 250 | 250, 500, 750, 1024 |
| Armazenamento em OLTP na memória máx. (GB) | N/A | N/D | N/D | N/A |
| Máximo de trabalhadores simultâneos (pedidos)| 60 | 90 | 120 | 200 |
| Sessões simultâneas máx. |600 | 900 | 1200 | 2400 |
||||||

### <a name="standard-service-tier-continued"></a>Camada de serviço Standard (continuada)

| **Tamanho de computação** | **S4** | **S6** | **S7** | **S9** | **S12** |
| :--- |---:| ---:|---:|---:|---:|
| Máximo de DTUs | 200 | 400 | 800 | 1600 | 3000 |
| Armazenamento incluído (GB) | 250 | 250 | 250 | 250 | 250 |
| Opções de armazenamento máximo (GB) | 250, 500, 750, 1024 | 250, 500, 750, 1024 | 250, 500, 750, 1024 | 250, 500, 750, 1024 | 250, 500, 750, 1024 |
| Armazenamento em OLTP na memória máx. (GB) | N/A | N/D | N/D | N/D |N/A |
| Máximo de trabalhadores simultâneos (pedidos)| 400 | 800 | 1600 | 3200 |6000 |
| Sessões simultâneas máx. |4800 | 9600 | 19200 | 30000 |30000 |
|||||||

### <a name="premium-service-tier"></a>Camada de serviços premium

| **Tamanho de computação** | **P1** | **P2** | **P4** | **P6** | **P11** | **P15** |
| :--- |---:|---:|---:|---:|---:|---:|
| Máximo de DTUs | 125 | 250 | 500 | 1000 | 1750 | 4000 |
| Armazenamento incluído (GB) | 500 | 500 | 500 | 500 | 4096 | 4096 |
| Opções de armazenamento máximo (GB) | 500, 750, 1024 | 500, 750, 1024 | 500, 750, 1024 | 500, 750, 1024 | 4096 | 4096 |
| Armazenamento em OLTP na memória máx. (GB) | 1 | 2 | 4 | 8 | 14 | 32 |
| Máximo de trabalhadores simultâneos (pedidos)| 200 | 400 | 800 | 1600 | 2400 | 6400 |
| Sessões simultâneas máx. | 30000 | 30000 | 30000 | 30000 | 30000 | 30000 |
|||||||

> [!IMPORTANT]
> Mais de 1 TB de armazenamento no escalão Premium está atualmente disponível em todas as regiões, exceto: Leste da China, Norte da China, Alemanha Central, Nordeste da Alemanha, e.u.a. centro-oeste, US DoD regiões e Governo dos E.u. a centro. Noutras regiões, o armazenamento máximo no escalão Premium está limitado a 1 TB.  Para obter mais informações, consulte limitações atuais do P11-P15.  
> [!NOTE]
> Para `tempdb` limites, consulte [limites de tempdb](https://docs.microsoft.com/sql/relational-databases/databases/tempdb-database?view=sql-server-2017#tempdb-database-in-sql-database).

## <a name="next-steps"></a>Passos Seguintes

- Para limites de recursos de vCore para uma base de dados, consulte [limites de recursos para bases de dados individuais com o modelo de compra baseado em vCore](sql-database-vcore-resource-limits-single-databases.md)
- Para limites de recursos de vCore para conjuntos elásticos, consulte [limites de recursos para conjuntos elásticos com o modelo de compra baseado em vCore](sql-database-vcore-resource-limits-elastic-pools.md)
- Para limites de recursos DTU para conjuntos elásticos, consulte [limites de recursos para conjuntos elásticos com o modelo de compra baseado em DTU](sql-database-dtu-resource-limits-elastic-pools.md)
- Para limites de recursos para instâncias geridas, consulte [geridos limites de recursos de instância](sql-database-managed-instance-resource-limits.md).
- Para obter informações sobre os limites do Azure gerais, consulte [subscrição do Azure e limites do serviço, quotas e restrições](../azure-subscription-service-limits.md).
- Para obter informações sobre limites de recursos num servidor de base de dados, consulte [descrição geral dos limites de recursos num servidor de base de dados SQL](sql-database-resource-limits-database-server.md) para obter informações sobre os limites nos níveis de servidor e de subscrição.
